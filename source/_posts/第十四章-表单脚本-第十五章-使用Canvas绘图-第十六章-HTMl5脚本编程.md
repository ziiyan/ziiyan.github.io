---
title: 第十四章 表单脚本 & 第十五章 使用 Canvas 绘图 & 第十六章 HTMl5 脚本编程
date: 2020-03-07 14:05:31
tags: JavaScript高级程序设计
---

# 第十四章 表单脚本

## 表单的基本知识

HTML 中，`<form>` 元素

Javascript 中，`HTMLFormElement `类型，继承 `HTMLElement`

* `acceptCharset` 服务器能够处理的字符集 `accept-charset`
* `action` 接受请求的 URL `action`
* `elements` 表单中所有控件集合 `HTMLCollection`
* `enctype` 请求的编码类型 `enctype`
* `length` 表单中控件的数量
* `method` 要发送的HTTP请求类型 get/post `method`
* `name` 表单名称 `name`
* `reset()` 将所有表单域重置为默认值
* `submit()` 提交表单
* `target` 用于发送和接受响应窗口的名称 `target`

### 提交表单

* `<input>` 和 `<button>` 都可以定义提交按钮，`type = submit` 即可

* 提交表单时，浏览器将请求发送给服务器之前触发 submit 事件，这样就有机会验证表单数据。
* 提交表单最大的问题是重复提交表单
	* 第一次提交后禁用按钮
	* 利用 onsubmit 事件处理程序 取消后续的表单提交操作

### 重置表单

* `<input>` 或 `<button>` 中 `type = reset`，单击即可重置

### 表单字段

每个表单都有 `elements` 属性，内容是所有表单元素的集合。集合是一个有序列表，包含表单中所有字段。

* 共有的表单字段属性
	* `disabled`
	* `form`
	* `name`
	* `readOnly`
	* `tabIndex`
	* `type`
	* `value`
* 共有的表单字段方法
	* `focus()`
	* `blur()`
	* `autofocus` 页面加载时自动获得焦点
* 共有的表单字段事件
	* `blur`
	* `change`
	* `focus`
		* `blur` 和 `change` 先后顺序在各浏览器中不同

## 文本框脚本

`<input>`、`<textarea>`

处理文本框时最好不要使用 DOM 方法（`setAttribute`）

### 选择文本

`select()` 方法获得焦点时选择其所有文本

* 选择事件
* 取得选择的文本
	* `selectionStart` 数值，开头位置
	* `selectionEnd` 数值，结尾位置
* 选择部分文本
	* `setSelectionRange()`

### 过滤输入

* 屏蔽字符

* 操作剪贴板

	* 剪贴板事件
		* `beforecopy`
		* `copy`
		* `beforecut`
		* `cut`
		* `beforepaste`
		* `paste`

	* clipboardData 对象
		* `getData()`
		* `setData()`
		* `clearData()`

### 自动切换焦点

`tabForward()`

### HTML5 约束验证 API

* 必填字段 `required` 属性
* 其它输入类型
	* `email`
	* `url`
* 数值范围
	* `min`
	* `max`
* 输入模式 `pattern`
* 检测有效性
	*  `checkValidity()`
	* `validity`
* 禁用验证 `novalidate`

## 选择框脚本

`<select>`、`<option>`

* `add(newOption, relOption)`
* `multiple`
* `options` 控件中所有 `<option>` 元素的 `HTMLCollection`，每个元素都是  `HTMLOptionElement` 对象
	* `index`
	* `label`
	* `selected`
	* `text`
	* `value`
* `remove(index)`
* `selectedIndex`
* `size`

## 表单序列化

>  表单提交期间，浏览器怎样将数据发送给服务器？
>
> * 对表单字段的名称和值进行 URL 编码，使用 & 分隔
>
> * 不发送禁用的表单字段
>
> * 只发送勾选的复选框和单选按钮
>
> * 不发送 type 为 reset 和 button 的按钮
> * 多选选择框中的每个选中的值单独一个条目
> * 在单击提交按钮提交表单的情况下，也会发送提交按钮；否则，不发送提交按钮。
> * \<select\> 元素的值，就是选中的 \<option\> 元素的 value 特性的值

## 富文本编辑

WYSIWYG（What You See Is What You Get）

在页面中嵌入一个包含空 HTML 页面的 iframe

通过设置 designMode 属性，可以编辑，编辑的对象是该页面 body 元素的 HTML 代码

# 第十五章 使用 Canvas 绘图

## 基本用法

1. 设置 width 和 height 属性，制定绘图区域
```html
<canvas id="drawing" width="200" height="200">A drawing of something.</canvas>
```
2. 取得绘图上下文
```js
var drawing = document.getElementById("drawing");

//确定浏览器支持 <canvas> 元素
if (drawing.getContext) {
    var context = drawing.getContext("2d");
    ……
}
```
3. 导出绘制的图像
```js
var drawing = document.getElementById("drawing");

if (drawing.getContext) {
    //取得图像的数据 URI
    var imgURI = drawing.toDataURL("imge/png");
    
    //显示图像
    var image = document.createElement("img");
    image.src = imgURI;
    document.body.appendChild(image);
}
```

## 2D上下文
* 坐标开始于左上角，原点坐标 (0, 0)
* x 越大靠右，y 越大靠下
* width 和 height 表示水平和垂直两个方向上可用的像素数

### 填充和描边
* `fillStyle` 填充（字符串、渐变对象或模式对象）
* `strokeStyle` 描边（字符串、渐变对象或模式对象）
* `lineWidth` 线条宽度（整数）
* `lineCap` 线条末端形状（“butt”、“round”、“square”）
* `lineJoin` 线条相交形状（“round”、“bevel”、“miter”）

### 绘制矩形
* `fillRect(x, y, w, h)`
* `strokeRect(x, y, w, h)`
* `clearRect(x, y, w, h)`
    * x: x 坐标
    * y: y 坐标
    * w: 矩形宽度
    * h: 矩形高度

### 绘制路径

1. 调用`beginPath()`表示开始绘制新路径
2. 绘制路径
    * `arc(x, y, radius, startAngle, endAngle, counterclockwise)` 
    * `arcTo(x1, y1, x2, y2, radius)` 
    * `bezierCurveTo(c1x, c1y, c2x, c2y, x, y)`
    * `lineTo(x, y)`
    * `moveTo(x, y)`
    * `quadraticCurveTo(cx, cy, x, y)`
    * `rect(x, y, width, height)`
3. 绘制到画布
    * `closePath()`
    * `fill()`
    * `stroke()`
    * `clip()`

* `isPointInpath(x, y)` 点(x, y)是否在路径上

### 绘制文本
* `fillText(string, x, y, (可选)font-size)`
* `strokeText(string, x, y, (可选)font-size)`

---
* `font` 文本样式、大小、字体
* `textAlign` 文本对齐方式
* `textBaseline` 文本基线

---
* `measureText()` 确定文本大小

### 变换
* `rotate(angle)` 绕原点旋转图像
* `scale(scaleX, scaleY)` 缩放图像
* `translate(x, y)` 将坐标原点移动到 (x, y)
* `transform(m1_1, m1_2, m2_1, m2_2, dx, dy)` 修改变换矩阵
* `setTransform(m1_1, m1_2, m2_1, m2_2, dx, dy)` 将变换矩阵重置为默认状态再调用 transform()

---
* `save()` 保存上下文状态
* `restore()` 读取保存的状态

### 绘制图像
* `drawImage()`
    * 要绘制的图像
    * 源图像的 x 坐标
    * 源图像的 y 坐标
    * 源图像的宽度
    * 源图像的高度
    * 目标图像的 x 坐标
    * 目标图像的 y 坐标
    * 目标图像的宽度
    * 目标图像的高度

### 阴影
* `shadowColor`
* `shadowOffsetXr`
* `shadowOffsetY`
* `shadowBlur`

### 渐变
1. `cg = createLinearGradient()` 创建新的渐变，返回 CanvasGradient 对象
2. `cg.addColorStop()` 制定色标
3. `fillStyle = cg`/`strokeStyle = cg` 绘制

### 模式
* 重复的图像
* `createPattern()`

### 使用图像数据
* `getImageData()`

### 合成
* `globalAlpha` 透明度
* `globalCompositionOperation`
    * source-over(默认值):后绘制的图形位于先绘制的图形上方
    * ……



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

























