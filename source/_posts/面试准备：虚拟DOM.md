---
title: 面试准备：虚拟 DOM
date: 2020-08-11 09:02:38
tags: [ Vue, React, 面试 ]
---

## 真实 DOM

1. 检查 HTML 并构建 DOM 树
2. 检查 CSS 构建 Style Rules
3. 结合 DOM & Style Rules，构建 Render 树
	* 每个 DOM 节点都有 attach 方法，接收样式信息，返回一个 render 对象
	* render 对象构成 render 树
4. 根据 render 树确定节点坐标，调用每个节点的 paint 方法，绘制页面

### 注意点

* DOM 树渐进构建，不必等整个 HTML 解析完
* Render 树、DOM 树、CSSOM 树构建并不完全独立，可能会交叉
* CSS 从右往左解析，嵌套标签越多，解析越慢
	* 最右可以快速判断当前元素是否匹配，向上验证
	* 从左开始类似深搜，需要回溯多次
* 原生 JS 操作 DOM 时，浏览器会从构建 DOM 树开始从头到尾执行一遍流程，如果需要更新 10 个节点，就会操作 10 次。

## 虚拟 DOM

* 如果需要更新 10 个节点，虚拟 DOM 不会立即操作 DOM，而是将 10 次更新的内容保存到本地 js 对象中，再一次性 attch 到 DOM 树上，避免无用的 DOM 操作。

### 算法步骤

1. 用 JavaScript 对象结构表示 DOM 树的结构，然后用对象构造一个真正的 DOM 树，插到文档中
2. 当状态变更时，重新构造一棵对象树，然后用新的树和旧的树比较，记录差异
3. 把差异应用到真的 DOM 树上，视图就更新了

> **虚拟 DOM 本质上就是在 JS 和 DOM 之间做了一个缓存**

## 算法实现

### 一、用 JS 对象模拟 DOM 树

#### 构建 JS 节点

```js
function Element (tagName, props, children) {
  this.tagName = tagName
  this.props = props
  this.children = children
}

module.exports = function (tagName, props, children) {
  return new Element(tagName, props, children)
}

// 用例
var el = require('./element')

var ul = el('ul', {id: 'list'}, [
  el('li', {class: 'item'}, ['Item 1']),
  el('li', {class: 'item'}, ['Item 2']),
  el('li', {class: 'item'}, ['Item 3']),
])
// <ul id='list'>
//  <li class='item'>Item 1</li>
//  <li class='item'>Item 2</li>
//  <li class='item'>Item 3</li>
// </ul>
```

#### 构建 DOM 节点

```js
Element.prototype.render = function () {
  var el = document.createElement(this.tagName)
  var props = this.props
  
  for (var propName in props) {
    var propValue = props[propName]
    el.setAttribute(propName, propValue)
  }
  
  var children = this.children || []
  
  children.forEach(function(child) {
    var childEl = (child instanceof Element)
    	? child.render() // 如果子节点也是虚拟DOM，递归构建DOM节点
    	: document.createTextNode(child)	// 如果是字符串，构建文本节点
    el.appendChild(childEl)
  })
  
  return el
}
```

### 二、比较两棵树的差异（diff）

* 通用算法，树的最小编辑距离，O(n^3)
* 同层比较，降低时间复杂度，O(n)

#### 深搜，记录差异

* 每遍历到一个节点就把该节点和新的的树进行对比

```js
function diff (oldTree, newTree) {
  var index = 0  // 当前节点标志，按深搜顺序从 9 开始
  var patches = {}  // 用来记录每个节点差异的对象
  dfsWalk(oldTree, newTree, index, patches)
  return patches
}

function dfsWalk (oldNode, newNode, index, patches) {
  // 对比 oldNode 和 newNode 的不同，记录下来
  patches[index] = [{difference}, {difference}, ...]
  diffChildren(oldNode.children, newNode.children, index, patches)
}

function diffChildren (oldChildren, newChildren, index, patches) {
  var leftNode = null
  var currentNodeIndex = index
  oldChildren.forEach(function (child, i) {
    var newChild = newChildren[i]
    currentNodeIndex = (leftNode && leftNode.count)	// 计算节点标识
    	? currentNodeIndex + leftNode.count + 1
    	: currentNodeIndex + 1
    dfsWalk(child, newChild, currentNodeIndex, patches)
    leftNode = child
  })
}
```

##### 差异类型

* 替换成新节点 REPLACE = 0
* 移动删除新增 REORDER = 1
* 修改部分属性 PROPS = 2
* 修改文本内容 TEXT = 3

```js
patches[0] = [{
  type: REPLACE,
  node: newNode // el('div', props, children)
}, {
  type: PROPS,
  props: {
    id: "container"
  }
}]

patches[2] = [{
  type: TEXT,
  content: "Virtual DOM2"
}]
```

#### 列表对比算法

* 最小编辑距离，针对 REORDER，同层元素对比顺序，解决如何移动最佳
* 需要用到唯一标识 key

```js
patches[0] = [{
  type: REORDER,
  moves: [{remove or insert}, {remove or insert}, ...]
}]
```

### 三、把差异应用到真的 DOM 树上

```js
function patch (node, patches) {
  var walker = {index: 0}
  dfsWalk(node, walker, patches)
}

function dfsWalk (node, walker, patches) {
  var currentPatches = patchea[walker.index]
  
  var len = node.childNodes
  	? node.childNodes.length
  	: 0
  for (var i = 0; i < len; i++) {
    var child = node.childNodes[i]
    walker.index++
    dfsWalk(child, walker, patches)
  }
  
  if (currentPatches) {
    applyPatches(onde, currentPatches)	// 对当前节点进行 DOM 操作
  }
}
```

```js
function applyPatches (node, currentPatches) {
  currentPatches.forEach(function (currentPatch) {
    switch (currentPatch.type) {
      case REPLACE:
        node.parentNode.replaceChild(currentPatch.node.render(), node)
        break
      case REORDER:
        reorderChildren(node, currentPatch.moves)
        break
      case PROPS:
        setProps(node, currentPatch.props)
        break
      case TEXT:
        node.textContent = currentPatch.content
        break
      default:
        throw new Error('Unknown patch type ' + currentPatch.type)
    }
  })
}
```

### 总结

```js
// 1. 构建虚拟DOM
var tree = el('div', {'id': 'container'}, [
    el('h1', {style: 'color: blue'}, ['simple virtal dom']),
    el('p', ['Hello, virtual-dom']),
    el('ul', [el('li')])
])

// 2. 通过虚拟DOM构建真正的DOM
var root = tree.render()
document.body.appendChild(root)

// 3. 生成新的虚拟DOM
var newTree = el('div', {'id': 'container'}, [
    el('h1', {style: 'color: red'}, ['simple virtal dom']),
    el('p', ['Hello, virtual-dom']),
    el('ul', [el('li'), el('li')])
])

// 4. 比较两棵虚拟DOM树的不同
var patches = diff(tree, newTree)

// 5. 在真正的DOM元素上应用变更
patch(root, patches)
```

## references

* [深度剖析：如何实现一个 Virtual DOM 算法](https://github.com/livoras/blog/issues/13)

* [深入剖析：Vue核心之虚拟DOM](https://juejin.im/post/6844903895467032589)

