---
title: Vue 常考知识点
date: 2020-03-24 18:54:30
tags: Vue
---

# Vue 常考知识点

## 生命周期

1. `beforeCreate` ：获取不到 `props` 或者 `data` 中的数据的，因为这些数据的初始化都在 `initState` 中。

2.  `created` ：可以访问到之前不能访问到的数据，但是这时候组件还没被挂载，所以是看不到的。

3. `beforeMount` ：开始创建 VDOM，组件中如果有子组件的话，会递归挂载子组件
4.  `mounted` ：将 VDOM 渲染为真实 DOM 并且渲染数据

5.  `beforeUpdate` ：数据更新前
6.  `updated`：数据更新后

* `keep-alive` 独有的生命周期，分别为 `activated` 和 `deactivated` 
	* 用 `keep-alive` 包裹的组件在切换时不会进行销毁，而是缓存到内存中并执行 `deactivated` 钩子函数，命中缓存渲染后会执行 `actived` 钩子函数。

7. `beforeDestroy` ：适合移除事件、定时器等等，否则可能会引起内存泄露的问题。然后进行一系列的销毁操作，如果有子组件的话，也会递归销毁子组件
8.  `destroyed`：所有子组件都销毁完毕后才会执行

## 组件通信

### 父子组件通信

* props、emit
* 访问 `$parent` 或者 `$children` 对象

### 兄弟组件通信

* 通过查找父组件中的子组件实现，也就是 `this.$parent.$children`
* 在 `$children` 中可以通过组件 `name` 查询到需要的组件实例

### 跨多层级组件通信

### 任意组件

* Vuex

## computed 和 watch 区别

* `computed` 是计算属性，依赖其他属性计算值，并且 `computed` 的值有缓存，只有当计算值变化才会返回内容。

* `watch` 监听到值的变化就会执行回调，在回调中可以进行一些逻辑操作。

## v-show 与 v-if 区别

* `v-show` 只是在 `display: none` 和 `display: block` 之间切换。
	* 无论初始条件是什么都会被渲染出来，后面只需要切换 CSS，DOM 还是一直保留着的。
	* 所以总的来说 `v-show` 在初始渲染时有更高的开销，但是切换开销很小，更适合于频繁切换的场景。

* `v-if` 的话就得说到 Vue 底层的编译了。
	* 当属性初始为 `false` 时，组件就不会被渲染，直到条件为 `true`，并且切换条件时会触发销毁/挂载组件，所以总的来说在切换时开销更高，更适合不经常切换的场景。
	* 并且基于 `v-if` 的这种惰性渲染机制，可以在必要的时候才去渲染组件，减少整个页面的初始渲染开销。

## 编译过程

* 通过正则表达式将模版解析为 AST（Abstract Syntax Tree，抽象语法树）
* 优化 AST：对节点进行了静态内容提取，也就是将永远不会变动的节点提取了出来，实现复用 Virtual DOM，跳过对比算法的功
* 将 AST 转换为 `render` 函数：遍历整个 AST，根据不同的条件生成不同的代码

