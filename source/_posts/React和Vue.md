---
title: React和Vue
date: 2020-03-24 17:53:27
tags: [ Vue, React ]
---

内容转自掘金小册《前端面试之道》

# React 和 Vue

## MVVM

不管是 React 还是 Vue，它们都不是 MVVM 框架，只是借鉴 MVVM 的思路

* View ：用户看到的视图
* Model：本地数据和数据库中的数据

传统的 MVC 架构通常是使用控制器更新模型，视图从模型中获取数据去渲染。当用户有输入时，会通过控制器去更新模型，并且通知视图进行更新。

<img src="/Users/wuziyan/Library/Application Support/typora-user-images/image-20200324175720375.png" alt="image-20200324175720375" style="zoom:50%;" />

* ViewModel：只关心数据和业务的处理，不关心 View 如何处理数据

<img src="/Users/wuziyan/Library/Application Support/typora-user-images/image-20200324175836531.png" alt="image-20200324175836531" style="zoom:50%;" />

* Binnder（隐式）：实现了 View 和 ViewModel 的绑定

<img src="/Users/wuziyan/Library/Application Support/typora-user-images/image-20200324180259118.png" alt="image-20200324180259118" style="zoom:50%;" />

* 精髓：**通过 ViewModel 将视图中的状态和用户的行为分离出一个抽象**

## Virtual DOM

### JS 模拟 DOM

* 操作 DOM 很慢，相较于 DOM 来说，操作 JS 对象会快很多，可以通过 JS 来模拟 DOM

```js
const ul = {
  tag: 'ul',
  props: {
    class: 'list'
  },
  children: {
    tag: 'li',
    children: '1'
  }
}
```

上述代码对应的 DOM 就是

```html
<ul class='list'>
  <li>1</li>
</ul>
```

### Virtual DOM 的 diff 算法

* 通过 JS 来模拟 DOM 并且渲染对应的 DOM 只是第一步，难点在于<u>如何判断新旧两个 JS 对象的**最小差异**并且实现**局部更新** DOM。</u>
* Virtual DOM 只会对同一个层级的元素进行对比
	* 深度优先遍历，每遍历到一个节点就把该节点和新的的树进行对比，如果有差异的话就记录到一个对象里
		* key 作为唯一表示
	* 差异类型
		* 替换掉原来的节点
		* 移动、删除、新增子节点
		* 修改了节点的属性
		* 对于文本节点，文本内容可能会改变

### 把差异应用到真正的DOM树上

* 对 DOM 树也进行深度优先的遍历，遍历的时候从保存差异的对象中找出当前遍历的节点差异，然后进行 DOM 操作

### 总结

```js
// 1\. 构建虚拟DOM
var tree = el('div', {'id': 'container'}, [
    el('h1', {style: 'color: blue'}, ['simple virtal dom']),
    el('p', ['Hello, virtual-dom']),
    el('ul', [el('li')])
])

// 2\. 通过虚拟DOM构建真正的DOM
var root = tree.render()
document.body.appendChild(root)

// 3\. 生成新的虚拟DOM
var newTree = el('div', {'id': 'container'}, [
    el('h1', {style: 'color: red'}, ['simple virtal dom']),
    el('p', ['Hello, virtual-dom']),
    el('ul', [el('li'), el('li')])
])

// 4\. 比较两棵虚拟DOM树的不同
var patches = diff(tree, newTree)

// 5\. 在真正的DOM元素上应用变更
patch(root, patches)
```

## 路由原理

* **监听 URL 的变化**，然后匹配路由规则，显示相应的页面

### Hash 模式

* `www.test.com/#/` 就是 Hash URL，当 `#` 后面的哈希值发生变化时，可以通过 `hashchange` 事件来监听到 URL 的变化，从而进行跳转页面，并且无论哈希值如何变化，服务端接收到的 URL 请求永远是 `www.test.com`。

### History 模式

* History 模式是 HTML5 新推出的功能，主要使用 `history.pushState` 和 `history.replaceState` 改变 URL。
* 通过 History 模式改变 URL 同样不会引起页面的刷新，只会更新浏览器的历史记录。

### 对比

- Hash 模式只可以更改 `#` 后面的内容，History 模式可以通过 API 设置任意的同源 URL
- History 模式可以通过 API 添加任意类型的数据到历史记录中，Hash 模式只能更改哈希值，也就是字符串
- Hash 模式无需后端配置，并且兼容性好。History 模式在用户手动输入地址或者刷新页面的时候会发起 URL 请求，后端需要配置 `index.html` 页面用于匹配不到静态资源的时候。

## Vue 和 React 之间的区别

* v-model
* setState
* JSX















