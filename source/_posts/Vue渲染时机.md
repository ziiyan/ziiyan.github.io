---
title: Vue渲染时机
date: 2020-03-24 12:39:49
tags: [ Vue, 浏览器 ]
---



​		最近在改样式时碰到一个很神奇的bug：qrcode 生成一个二维码，绘制在对应的 canvas 标签中，bug 是二维码不显示。代码总共三行，qrcode 的回调 error 是空的，说明至少二维码的创建到绘制是没有错误的。

​		查看分支记录，发现了一些相关修改：二维码码是放在一个弹窗中的，以前的弹窗是用绝对定位移出可视区域，实际上弹窗是一直存在的。因为设计的需求换成了 AntD，这个弹窗也换成了 modal 组件，只有在点击个人信息的时候才显示。

##  Modal 组件渲染时机

* 项目采用的都是同步引入的方式，父子组件渲染顺序如下：

> 加载渲染过程
>
> 父 beforeCreate
> 父 created
> 父 beforeMount
> 子 beforeCreate
> 子 created
> 子 beforeMount
> 子 mounted
> 父 mounted
>
> ---
>
> 更新过程
>
> 父 beforeUpdate
> 子 beforeUpdate
> 子 updated
> 父 updated

生命周期介绍：[https://cn.vuejs.org/v2/api/#%E9%80%89%E9%A1%B9-%E7%94%9F%E5%91%BD%E5%91%A8%E6%9C%9F%E9%92%A9%E5%AD%90](https://cn.vuejs.org/v2/api/#选项-生命周期钩子)

## Bug 分析流程

* 现在在 mouted 里面加入绘制二维码的代码

><img src="/Users/wuziyan/Library/Application Support/typora-user-images/image-20200324140454111.png" alt="image-20200324140454111" style="zoom:50%;" />
>
>document.getElementById('canvas') 获取到的元素为空
>
>qrcode 报错，这里主要是用来生成二维码的 userInfo.pri_Key 还没获取到值

* 在 watch 里面加入绘制二维码的代码，监听的是控制弹窗是否显示的变量

> <img src="/Users/wuziyan/Library/Application Support/typora-user-images/image-20200324141141884.png" alt="image-20200324141141884" style="zoom:50%;" />
>
> document.getElementById('canvas') 获取到的元素为空
>
> userInfo.pri_Key 有值，qrcode 不报错了

* 很显然，问题就在为什么这个 canvas 标签一直获取不到，那什么时候才能获取到呢？

> <img src="/Users/wuziyan/Library/Application Support/typora-user-images/image-20200324150813733.png" alt="image-20200324150813733" style="zoom:50%;" />
>
> Click 点击显示 modal，要到子组件 updated 时才能获取到标签
>
> 也就是说在 updated 里面才更新 DOM，watch 里面是不更新的

* 官网里面关于 updated 的描述

> 由于数据更改导致的虚拟 DOM 重新渲染和打补丁，在这之后会调用该钩子。
>
> 当这个钩子被调用时，组件 DOM 已经更新，所以你现在可以执行依赖于 DOM 的操作。然而在大多数情况下，你应该避免在此期间更改状态。如果要相应状态改变，通常最好使用[计算属性](https://cn.vuejs.org/v2/api/#computed)或 [watcher](https://cn.vuejs.org/v2/api/#watch) 取而代之。

* 但我们知道，watch 里 DOM 还没更新，我们如何在 watch 里面修改 canvas 呢？

> 如果你希望等到整个视图都重绘完毕，可以在 `updated` 里使用 [vm.$nextTick](https://cn.vuejs.org/v2/api/#vm-nextTick)

## nextTick

> 将回调延迟到下次 DOM 更新循环之后执行。在修改数据之后立即使用它，然后等待 DOM 更新。

* 显然，这就是我们需要的方法了。
	* 等待 DOM 更新，可以得到 canvas 标签
	* 调用 qrcode 库创建二维码并会绘制到 canvas 上

* 将绘制二维码的代码封装一层 nextTick

> <img src="/Users/wuziyan/Library/Application Support/typora-user-images/image-20200324141636792.png" alt="image-20200324141636792" style="zoom:50%;" />
>
> document.getElementById('canvas') 可以正常获取到元素，二维码正常显示

## 异步更新队列

> Vue 在更新 DOM 时是**异步**执行的。
>
> 只要侦听到数据变化，Vue 将开启一个队列，并缓冲在同一事件循环中发生的所有数据变更。
>
> 如果同一个 watcher 被多次触发，只会被推入到队列中一次。
>
> 这种在缓冲时去除重复数据对于避免不必要的计算和 DOM 操作是非常重要的。
>
> 然后，在下一个的事件循环 “tick” 中，Vue 刷新队列并执行实际 (已去重的) 工作。
>
> Vue 在内部对异步队列尝试使用原生的 `Promise.then`、`MutationObserver` 和 `setImmediate`，如果执行环境不支持，则会采用 `setTimeout(fn, 0)` 代替。

* 之前误打误撞使用了 setTimeout(fn, 0)，实际上恰好实现了异步队列

> 如果你想基于更新后的 DOM 状态来做点什么，这就可能会有些棘手。
>
> 虽然 Vue.js 通常鼓励开发人员使用“数据驱动”的方式思考，避免直接接触 DOM，但是有时我们必须要这么做。
>
> 为了在数据变化之后等待 Vue 完成更新 DOM，可以在数据变化之后立即使用 `Vue.nextTick(callback)`。这样回调函数将在 DOM 更新完成后被调用。

## 总结

* moda 组件在 mounted 时并不会渲染到页面上
* 在控制 modal 显示变量为 true 之后，直到 updated 阶段才更新 DOM
* watch 当中有关 DOM 的操作，要利用 nextTick 延迟到 DOM 渲染之前执行

