---
title: 第十六章 HTMl5 脚本编程
date: 2020-03-07 14:05:31
tags: JavaScript高级程序设计
---

# 第十六章 HTML5 脚本编程

## 跨文档消息传递 XDM

来自不同域的页面间传递消息

* `postMessage(消息，域)` 发送【消息】给【域】中的 `<iframe>` 元素
* 接收到 XDM 消息时，会触发 window 对象的 message 事件，异步，有延迟
* `onmessage`
	* `data`
	* `origin`
	* `source`

## 原生拖放

### 拖放事件

1. `dragstart` 按下鼠标触发
2. `drag ` 拖动期间持续触发
3. `dragend` 拖动停止时触发，无论放置目标是否有效

* 当被拖动到一个有效的放置目标上

1. `dragenter` 拖动到放置目标上
2. `dragover` 放置目标范围内移动持续触发
3. `dragleave` 拖出放置目标范围 或 `drop` 放到放置目标中



### 自定义放置目标

元素默认不允许放置，修改为可放置：

```js
var droptarget = document.getElementById("droptarget");

EventUtil.addHandler(droptarget, "dragover", function(event) {
  EventUtil.preventDefault(event);
});

EventUtil.addHandler(droptarget, "dragenter", function(event) {
  EventUtil.preventDefault(event);
})
```



### dataTransfet 对象

用于从被拖动元素向放置目标传递字符串格式的数据

* `getData()`
* `setData()`

保存在 dataTransfer 对象中的数据只能在 drop 事件中被读取

* `dropEffect` 被拖动元素能执行的放置行为
	* none
	* move
	* copy
	* link
* `effectAllowed` 允许拖动元素的哪种 dropEffect
	* uninitialized
	* none
	* copy
	* link
	* move
	* copyLink
	* copyMove
	* linkMove
	* all

### 可拖动

默认情况下，图像、链接、文本可拖动

其它元素可以手动设置 draggable 属性为 ture



## 媒体元素

`<audio>`、`<video>` 必须 src 指向要加载的媒体

### 属性

表示媒体的当前状态

### 事件

触发事件，监听属性变化，可能是播放结果，也可能是操作结果



## 历史状态管理

history 对象

























