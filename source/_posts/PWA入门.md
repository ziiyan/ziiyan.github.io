---
title: PWA入门
date: 2020-05-23 17:30:58
tags: PWA
---

# PWA 基本介绍

## PWA 是什么

Progressive Web App，渐进式 Web 应用，提升 Web App 体验的方法，给用户原生用户的体验。https://developer.mozilla.org/zh-CN/docs/Web/Progressive_web_apps



## PWA 的优势

* 渐进式 - 适用于所有浏览器，不影响原 Web App 功能
* 流畅 - 借助 Wervice Worker 在离线或者网络较差的情况下正常访问
* 可安装 - 用户可以添加常用的 webapp 到桌面，免去应用商店下载的麻烦
* 原生体验 - 和 app 一样，拥有首屏加载动画，可以隐藏地址栏等沉浸式体验
* 粘性 - 通过推送离线通知等，可以让用户回流



# PWA 核心技术揭秘

## web app manifest：应用程序清单

https://developer.mozilla.org/zh-CN/docs/Web/Manifest

### 基本介绍

* web app manifest 是 PWA 技术集合的一部分

* web app manifest 可以让网站安装到设备的主屏幕，而不需要用户通过应用商店进行下载

* web app manifest，在一个 **JSON 文本文件**中提供有关应用程序的信息（如名称、作者、图标和描述）

* 传统的 web app 入口：

	1. 网址

	2. 书签，收藏夹

	3. 直接搜索

* Web app manifest

	* 可以添加到桌面，有唯一的图标和名称
	* 有启动时界面，避免生硬的过渡
	* 隐藏浏览器相关的 UI，比如地址栏等

### 使用步骤

1. 项目根目录下创建一个 manifes.json 文件

2. 在 index.html 中引入 manifes.json 文件

	```html
	<link rel="manifest" href="manifest.json" />
	```

3. 在 manifest.json 文件中提供常见的配置
4. 需要在 https 协议或 http://localhost 下访问项目



### 常见配置

* name
	* 应用的名称
	* 用户安装横幅提示的名称
	* 启动画面中的文字
* short_name
	* 应用的短名称
	* 主屏幕显示
* start_url
	* 设备启动应用程序时加载的 url
	* 绝对路径 / 相对路径
* icons：图标对象数组
	* 一般配 144x144
* background_color：启动动画的背景颜色
* theme_color：应用程序的主题颜色
* display：app 显示模式
	* fullscreen
	* **standalone**
	* minimal-ui



## service worker

* 一个标准的 PWA 程序，必须包含 3 个部分
	* https 服务器或者 http://localhost
	* manifest.json
	* service worker
* W3C 组织在 14 年 5 月提出 service worker 这样一个 HTML5 API，主要用来做持久的离线缓存
* 前端有很多性能优化手段（必须联网）
	* CDN
	* CSS Sprite
	* 文件的合并压缩
	* 异步加载
	* 资源缓存
* service worker 允许 web 应用在网络环境比较差或者是离线的环境下依旧可以使用
* service worker 可以极大的提升 web app 的用户体验
* service worker  是一个独立的 worker 线程，独立于当前网页进程，是一种特殊的 web worker



### web worker

#### 简介

* 浏览器中的 JavaScript 都是运行在一个单一主线程上的，在同一时间内只能做一件事
* 随着 web 业务不断复杂，逐渐在 JavaScript 中加了很多复杂运算过程，如果在主线程中计算，会造成性能问题
* W3c 提供 web worker 的 API，web worker 脱离在主线程之外，将复杂的功能独立于主线程
* 完成后通过 postMessage 方法告诉主线程
* web worker 是一个独立的运行环境，不能操作 DOM 和 BOM

#### 使用

1. 创建

```js
var worker = new Worker('work.js')
```

2. 执行复杂计算

3. 通知主线程

```js
self.postMessage(msg)
```

4. 主线程监听消息

```js
worker.onmessage = function(msg) {}
```



### service worker 基本介绍

* web worker 是临时的，无法操作缓存，无法持久存储
* service worker 一旦被 install，就永远存在，除非被手动 unregister
* 用到的时候可以直接唤醒，不用的时候自动睡眠
* 可编程拦截代理请求和返回，缓存文件，缓存的文件可以被网页进程取到（包括网络离线状态）
* 离线内容开发者可控
* 必须在 HTTPS 环境下才能工作
* 异步实现，内部大都是通过 Promise 实现

![image-20200524233840196](/Users/wuziyan/Library/Application Support/typora-user-images/image-20200524233840196.png)

### service worker 使用步骤

1. 在 window.onload 中注册 service worker，防止与其他资源竞争
2. navigator 对象中内置了 serviceWorker 属性
3. service worker 在老版本的浏览器中不支持，需要进行浏览器兼容 if ('serviceWorker' in navigator) {}
4. 注册 service worker navigator.serviceWorker.register('./sw.js')，返回一个 promise 对象

```js
window.onload = function () {
  if ('serviceWorker' in navigator) {
    navigator.serviceWorker
    	.register('./sw.js')
    	.then(resitration => {console.log(resitration)})
    	.catch(err => {
      	console.log(err)
    })
  }
}
```



### service worker 生命周期事件

* Install 事件
	* 注册成功触发
	* 用于缓存资源
* activate 事件
	* 激活时候触发
	* 用于删除陈旧资源
* fetch 事件
	* 发送请求时候触发
	* 用于操作缓存或者读取网络资源

---

* sw.js 改变，install 事件重新触发
* activate 事件在 install 事件后触发
	* 但已存在 service worker 了，那么就处于等待状态，直到当前 service worker 终止
	* 可以通过 self.skipWaiting() 方法跳过等待，返回一个 promise 对象
	* event.waitUnitl(promise 对象)
		* promise 对象执行结束后才结束当前生命周期函数
		* 防止浏览器在异步操作前停止生命周期
* Service worker 激活后，会在下一次刷新页面的时候生效
	* 可以通过 self.clients.claim() 立即获取控制权



## promise

* 异步编程的一种解决方案，比传统方案（回调函数和事件）更合理
* 以链式的方式进行异步编程，解决回调地狱
* 常用方法
	* Promise.resolve()
	* Promise.reject()
	* Promise.all()
	* Promise.race()



## async / await

* 使异步操作更方便
* async 修饰函数，async function fn() {}
* await 函数返回一个 promise 对象
* await 只能出现在 async 函数中，后面跟一个 promise 对象，用于获取 promise 对象成功的结果
	* 如果不是 promise 对象，直接返回值
* await 阻塞 async 函数执行
* await 后面的 promise 如果没有成功，会抛出异常，需要 try...catch



## fetch api

* fetch api 提供了一个 JavaScript 接口，用于访问和操纵 HTTP 管道的部分
	* 例如请求和响应
* service worker 中发送请求只能用 fetch api
* fetch api 是基于 promise 实现的
* fetch(url, config) 用于发送 http 请求，返回一个包含响应结果的 promise 对象
* response 是一个二进制数据流，需要调用 json() 方法转换成 json 数据
* config 常见参数
	* body：设置请求体
	* headers：设置请求头
	* method：设置请求方式



## cache storage

### 基本使用

* cacheStorage 接口表示 Cache 对象的存储，配合 service worker 来实现资源的缓存
* caches api 类似于数据库的操作
	* caches.open(cacheName).then(function(cache) {}) 打开缓存，返回 promise
		* 类似连接数据库
	* caches.keys() 返回 promise 对象，包括所有缓存的 key
		* 数据库名
	* caches.delete(key) 根据 key 删除对应缓存（数据库）
* cache 对象常用方法（单条数据的操作）
	* cache 接口为缓存的 Request / Response 对象对提供存储机制
	* cache.put(req, res) 把请求当成 key，把对应响应存储起来
	* cache.add(url) 根据 url 发起请求，把响应结果存储起来
	* cache.addAll(urls) 抓取一个 url 数组，并且把结果存储起来
	* cache.match(req) 获取 req 对应的 response



## 离线处理

* caches 中已经缓存了我们需要的静态资源
* 当请求失败的时候，就可以去缓存中读取对应数据
* 通过 event.respondWith(response) 可以控制响应的内容

<img src="/Users/wuziyan/Library/Application Support/typora-user-images/image-20200602122835939.png" alt="image-20200602122835939" style="zoom:50%;" />



## notification api

### 基本使用

* notifications API 的通知接口用于向用户配置和显示桌面通知
* notification.permission 可以获取当前用户的授权情况
	* default 未授权
	* denied 拒绝，无法再次请求
	* granted
* 通过 notification.requestPermission() 可以请求用户的授权
* 通过 new notification('title', { body: ‘...’ , icon: ‘...’ }) 可以显示通知
* 在授权情况下，可以在 service worker 中显示通知 self.registration.showNotification('title', { body: 'msg' })