---
title: 第十五章 使用 Canvas 绘图
date: 2020-03-02 22:15:13
tags: JavaScript高级程序设计
---

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