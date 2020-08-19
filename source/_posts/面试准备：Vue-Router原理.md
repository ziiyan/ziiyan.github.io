---
title: 面试准备：Vue-Router 原理
date: 2020-08-17 09:00:07
tags: [ Vue, 面试 ]
---

## 路由分类

* 匹配不同的 url 路径，进行解析，然后动态的渲染出区域 html 内容
* 但 url 改变会造成页面刷新，解决思路是改变 url 但不刷新页面

### hash 模式

* `#` 号后面 hash 值变化，不会导致浏览器发出请求，也就不会刷新页面
* hash 值变化会触发 `hashchange` 事件，通过监听 `hashchange` 实现更新页面部分内容
* 支持所有浏览器

### history 模式

* H5 API
	* `pushState`
	* `replaceState`
	* 触发 `popstate` 事件
* 没有 `#` 号，刷新会发送请求，需要服务器支持，把所有路由都重定向到根页面
* 依赖 HTML5 History API 和服务器配置

### abstract 模式

* 支持所有 JavaScript 运行环境，如 Node.js 服务器端
* **如果发现没有浏览器的 API，路由会自动强制进入这个模式**

## 源码分析

### 模式参数

```js
const router = new VueRouter({
  mode: 'history',
  routes: [...]
})
```

```js
export default class VueRouter {
  // ...
  mode: string	// 传入的字符串参数，指示history类别
  history: HashHistory | HTML5History | AbstractHistory	// 实际起作用的对象属性，必须是以上三个类的枚举
	// ...
  fallback: boolean	// 如浏览器不支持，'history'模式需回滚为'hash'模式
  // ...

  constructor (options: RouterOptions = {}) {
    // ...

    let mode = options.mode || 'hash'	// 默认为'hash'模式
    this.fallback =
      mode === 'history' && !supportsPushState && options.fallback !== false	// 通过supportsPushState判断浏览器是否支持'history'模式
    if (this.fallback) {
      mode = 'hash'
    }
    if (!inBrowser) {	// 不在浏览器环境下运行需强制为'abstract'模式
      mode = 'abstract'
    }
    this.mode = mode

    // 根据mode确定history实际的类并实例化
    switch (mode) {
      case 'history':
        this.history = new HTML5History(this, options.base)
        break
      case 'hash':
        this.history = new HashHistory(this, options.base, this.fallback)
        break
      case 'abstract':
        this.history = new AbstractHistory(this, options.base)
        break
      default:
        if (process.env.NODE_ENV !== 'production') {
          assert(false, `invalid mode: ${mode}`)
        }
    }
  }

	// ...

  init (app: any /* Vue component instance */) {
    // ...

    const history = this.history
    
    // 根据history的类别执行相应的初始化操作和监听
    if (history instanceof HTML5History || history instanceof HashHistory) {
      const handleInitialScroll = routeOrError => {
        const from = history.current
        const expectScroll = this.options.scrollBehavior
        const supportsScroll = supportsPushState && expectScroll

        if (supportsScroll && 'fullPath' in routeOrError) {
          handleScroll(this, routeOrError, from, false)
        }
      }
      const setupListeners = routeOrError => {
        history.setupListeners()
        handleInitialScroll(routeOrError)
      }
      history.transitionTo(
        history.getCurrentLocation(),
        setupListeners,
        setupListeners
      )
    }

    history.listen(route => {
      this.apps.forEach(app => {
        app._route = route
      })
    })
  }

	// ...

	// VueRouter类暴露的以下方法实际是调用具体history对象的方法
  push (location: RawLocation, onComplete?: Function, onAbort?: Function) {
    // ...
    this.history.push(location, onComplete, onAbort)
  }

  replace (location: RawLocation, onComplete?: Function, onAbort?: Function) {
    // ...
     this.history.replace(location, onComplete, onAbort)
  }

  go (n: number) {
    this.history.go(n)
  }

  back () {
    this.go(-1)
  }

  forward () {
    this.go(1)
  }

	// ...
```

* 作为参数传入的字符串属性mode只是一个标记，用来指示实际起作用的对象属性history的实现类
	* history - HTML5History
	* hash - HashHistory
	* abstract - AbstractHistory
* 初始化对应的 history 之前，会对 mode 做一些校验
	* 若浏览器不支持 HTML5History，则mode强制设为`hash`
	* 若不是在浏览器环境下运行，则mode强制设为`abstract`
* VueRouter 类中的`onReady()`、` push()`等方法只是一个代理
	* 实际是调用的具体`history`对象的对应方法
	* 在`init()`方法中初始化时，也是根据`history`对象具体的类别执行不同操作

### HashHistory

* hash（“#”）符号的本来作用是加在URL中指示网页中的位置，可通过 window.location.hash 属性读取
	* 虽然出现在URL中，但是用来指导浏览器动作的，不会被包括在HTTP请求中，对服务器端完全无用，因此，改变hash不会重新加载页面
	* 可以为hash的改变添加监听事件 `hashchange`
	* 每一次改变hash（window.location.hash），都会在浏览器的访问历史中增加一个记录

#### push

直接赋值，hash的改变会自动添加到浏览器的访问历史记录中

```js
window.location.hash = route.fullPath
```

#### replace

* 并不是将新路由添加到浏览器访问历史的栈顶，而是替换掉当前的路由

```js
window.location.replace(
  window.location.href.slice(0, i >= 0 ? i : 0) + '#' + path
)
```

#### 监听地址栏

* 监听 `hashchange`，调用的函数为 `replaceHash`
* 即在浏览器地址栏中直接输入路由相当于代码调用了 `replace()` 方法

### HTML5History

* History interface 是浏览器历史记录栈提供的接口，通过 back(), forward(), go() 等方法，我们可以读取浏览器历史记录栈的信息，进行各种跳转操作
* 从HTML5开始，History interface提供了两个新的方法：`pushState(),` `replaceState()` 使得我们可以对浏览器历史记录栈进行修改
* 这两个方法有个共同的特点：当调用他们修改浏览器历史记录栈后，虽然当前URL改变了，但浏览器不会立即发送请求该URL，这就为单页应用前端路由“更新视图但不重新请求页面”提供了基础

#### push

* 对 `window.location.hash` 直接进行赋值改为了调用 `history.pushState()` 方法

#### replace

* `window.location.replace` 改为了 `history.replaceState()` 方法

#### 请求问题

* 用户直接在地址栏中输入并回车时，浏览器会重启重新加载应用
* 如果 URL 匹配不到任何静态资源，则应该返回同一个 index.html 页面，这个页面就是你 app 依赖的页面

### 区别

* history 需要 H5 接口的支持，会有兼容性问题
* 需要后端配合设置静态资源重定向，否则会 404
* pushState设置的新URL可以是与当前URL同源的任意URL；而hash只可修改#后面的部分，故只可设置与当前同文档的URL
* pushState设置的新URL可以与当前URL一模一样，这样也会把记录添加到栈中；而hash设置的新值必须与原来不一样才会触发记录添加到栈中
* pushState通过stateObject可以添加任意类型的数据到记录中；而hash只可添加短字符串
* pushState可额外设置title属性供后续使用

## references

* [前端路由简介以及vue-router实现原理](https://juejin.im/post/6844903615283363848)
* [【源码拾遗】从vue-router看前端路由的两种实现](https://zhuanlan.zhihu.com/p/27588422)

