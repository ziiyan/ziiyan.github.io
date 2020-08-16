---
title: canvas从入门到放弃
date: 2020-04-25 12:46:30
tags: JavaScript
---

# 简介

H5 新增的标签，可以用脚本（js）在其中绘制图像的 HTML 元素。



# 基本使用

## 元素

可以用 css 设置属性，但比例不一致会扭曲，建议直接用 width height 属性

默认 width 300 height 150 单位 px

```html
<canvas>替换内容</canvas>
```

不支持 canvas 的浏览器会显示替换内容，尾标签不可省略



## 渲染上下文

`<canvas>` 创建一个固定大小的画布，公开一个或多个**渲染上下文**（画笔），使用**渲染上下文**来绘制和处理要展示的内容。

主要使用 2D 上下文，WebGL 使用了基于 OpenGL ES 的 3D 上下文



## 检测支持特性

```js
var canvas = document.getElementById('myCanvas');
if (canvas.getContext) {
	var ctx = canvas.getContext('2d');
  ...
} else {sr
  ...
}
```



# 绘制形状

## 绘制矩形

原生只支持矩形绘制，其他所有图形都至少需要生成一种路径（path）

* `fillRect(x, y, width, height)` 填充
* `strokeRect(x, y,width, height)` 空心
* `clearRect(x, y, width, height)` 清除区域



## 绘制路径

**路径**是通过不同颜色和宽度的线段或曲线相连形成的不同形状的点的集合，每个路径都是闭合的。

**路径绘制图形步骤：**

1. 创建路径起始点
2. 调用绘制方法绘制路径
3. 封闭路径
4. 通过描边或填充路径区域来渲染图形

**绘制方法：**

* `beginPath()` 创建路径，画笔指向路径
* `moveTo(x, y)` 画笔移动到坐标，设置路径起始点
* `closePath()` 闭合路径（自动连接终点和起始点），画笔指向上下文
* `stroke()` 线条
* `fill()` 填充

**线段**

* `lineTo(x, y)`

**弧形**

* `arc(x, y, r, startAngle, endAngle, anticlockwise)` (x, y) 圆心，r 半径，startAngle 弧度到 endAngle 弧度结束，anticlockwise true 逆时针，false（默认）顺时针

```js
// 角度换算弧度
const radians = (Math.PI / 180) * degrees 
```

* `arcTo(x1, y1, x2, y2, radius)`根据给定的控制点和半径画弧，直线连接两个点

画一条与起始点->(x1, y1)，(x1, y1)->(x2, y2) 这两条切线相切的圆弧



## 绘制贝塞尔曲线

**二次贝塞尔曲线**

* `quadraticCurveTo(cp1x, cp1y, x, y)` (cp1x, cp1y) 控制点 (x, y) 结束点

**三次次贝塞尔曲线**

`quadraticCurveTo(cp1x, cp1y, cp2x, cp2y, x, y)` 



## 添加样式和颜色

* `fillStyle = color` 填充颜色
* `strokeStyle = color` 轮廓颜色

color：表示 css 颜色值的字符串、渐变对象、图案对象。默认黑色。设置后保持直到重新设置。

* `globalAlpha = transparencyValue`全局透明度

* `lineWidth = 1.0`  线宽，默认 1.0
* `lineCap = type` 线条末端样式
	* butt 方形
	* round 圆形 
	* square方形，多了一个高度是线段厚度一半的矩形
* `lineJoin = type` 同一个 path 内，线条与线条之间接合处的样式
	* round 圆
	* bevel 平
	* miter 尖（默认）
* 虚线
	* `setLineDash([实现长度，间隙长度])`
	* `lineDashOffset` 偏移量



# 绘制文本

* `fillText(text, x, y [, maxWidth])` 在 (x, y) 填充文本，最大宽度可选
* `strokeText(text, x, y [, maxWidth])` 在 (x, y) 绘制边框，最大宽度可选

**样式**

* `font = value` 文本样式，同 css font 属性
* `textAlign = value` 对齐选项
	* start（默认）
	* end
	* left
	* right
	* center
* `textBaseline = value` 基线对齐选项
	* top
	* hanging
	* middle
	* alphabetic（默认）
	* ideographic
	* bottom
* `direction = value` 文本方向
	* ltr
	* trl
	* Inherit（默认）



# 绘制图片

## 由零开始创建图片

* `drawImage(img, x, y)`

```js
var img = new Image();
img.src = 'myImage.png';
img.onload = function () { // 加载后再绘制
  ctx.drawImage(img, 0, 0);
}
```

**img 可以是DOM元素**

```js
var img = document.querySelector("img");
ctx.drawImage(img, 0, 0);
```

* `drawImage(img, x, y, width, height)` 图片缩放
* `drawImage(image, sx, sy, sWidth, sHeight, dx, dy, dWidth, dHeight)` (sx, sy) 原图像中的点，剪裁 (sx+sWidth, sy+sHeight)；绘制到画布（dx, dy）上



# 状态的保存和恢复

* `save()` 存储在栈中，可以多次调用，类似于 push()
* `restore()` 恢复，可以多次调用，类似于 pop()



# 变形

使用前先保存状态便于恢复

* `translate(x, y)` 移动原点到(x, y)
* `rotate(angle)` 旋转坐标轴，顺指针
* `scale(x, y)` x 轴和 y 轴的缩放因子
* `transform(a, b, c, d, e, f)`  变形矩阵



# 合成

* `globalCompositeOperation = type`
	* 默认：新图像+老图像（新图像在老图像上面）
	* source-in：新图像的重叠部分
	* Source-out：新图像的没重叠部分
	* source-atop：老图像+重叠部分
	* destination-over：新图像+老图像（老图像在新图像上面）
	* destination-in：老图像的重叠部分
	* destination-out：老图像的没重叠部分
	* destination-atop：老图像重叠部分+新图像（新图像在老图像下面）
	* lighter：新图像+老图像（重叠区域颜色叠加）
	* darken：新图像+老图像（重叠区域颜色取最深）
	* lighten：新图像+老图像（重叠区域颜色取最亮）
	* xor：重叠部分透明
	* copy：新图像保留，清楚其余部分



# 裁剪路径

* `clip()` 遮罩，只显示路径内的区域



# 动画

## 基本步骤

1. 清空 canvas
	* 绘制每一帧动画前，要清空所有
	* clearRect()
2. 保存 canvas 状态
3. 控制动画图形
	* 真正的绘制动画帧
4. 恢复 canvas 状态



## 控制动画

* `setInterval()`
* `setTimeout()`
* `requestAnimationFrame()`











