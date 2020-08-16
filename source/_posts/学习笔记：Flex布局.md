---
title: 学习笔记：Flex 布局
date: 2020-08-02 10:00:03
tags: CSS
---

## 1、Flex 布局

```css
.box {
  display: flex;
}
```

* 行内元素

```css
.box {
  display: inline-flex;
}
```

* 设为 Flex 布局以后，子元素的`float`、`clear`和`vertical-align`属性将失效

## 2、基本概念

* 容器（flex container）
	* 设置 flex 布局的元素 
* 项目（flex item）
	* 容器的直接子元素
* 主轴（main axis）：默认沿主轴排列
	* 开始位置（main start）
	* 结束位置（main end）
* 交叉轴（cross axis）
	* 开始位置（across start）
	* 结束位置（across end）
* 单个项目占据主轴的空间（main size）
* 单个项目占据交叉轴的空间（cross size）

## 3、容器的属性

* `flex-direction`
* `flex-wrap`
* `flex-flow`
* `justify-content`
* `align-items`
* `align-content`

### 3.1、flex-direction

* `flex-direction` 决定主轴的方向
	* `row` （默认）水平，左 -> 右
	* `row-reverse` 水平，左 <- 右
	* `column` 垂直，上 -> 下
	* `column-reverse` 垂直，上 <- 下

### 3.2、 flex-wrap

* `flex-wrap` 如果一条轴线排不下，如何换行
	* `nowrap` （默认）不换行
	* `wrap` 换行，第一行在上方
	* `wrap-reverse` 换行，第一行在下方

### 3.3、flex-flow

* `flex-flow` 是 `flex-direction` 和 `flex-wrap` 的简写
	* `flex-flow: <flex-direction> || <flex-wrap>`

### 3.4、justify-content

* `justify-content` 项目在主轴上的对齐方式
	* `flex-start` （默认）左对齐
	* `flex-end` 右对齐
	* `center` 居中
	* `space-between` 两端对齐，项目间隔相等
	* `space-around` 每个项目两侧间隔相等（项目之间的间距是项目与边框间距的两倍）

### 3.5、align-item

* `align-item` 项目在交叉轴上的对齐方式
	* `flex-start` 起点对齐
	* `flex-end` 终点对齐
	* `center` 居中
	* `baseline` 项目第一行文字的基线对齐
	* `stretch` （默认）项目未设置高度或高度 auto，占满整个容器高度

### 3.6、align-content

* `align-content` （如果有多根主轴）多根主轴的对齐方式，只有一根不起作用
	* `flex-start` 与交叉轴的起点对齐
	* `flex-end` 与交叉轴的终点对齐
	* `center` 与交叉轴的中点对齐
	* `space-between` 与交叉轴两端对齐，轴线之间的间隔平均分布
	* `space-around` 每根轴线两侧的间隔都相等。所以，轴线之间的间隔比轴线与边框的间隔大一倍
	* `stretch` 轴线占满整个交叉轴

## 4、项目的属性

* `order`
* `flex-grow`
* `flex-shrink`
* `flex-basis`
* `flex`
* `align-self`

### 4.1、order

* `order` 项目排列顺序，默认 0，数值越小越靠前
	* `order: <integer>`

### 4.2、flex-grow

* `flex-grow` 项目方大比例，默认 0，即不放大
	* `flex-grow: <number>`

### 4.3、flex-shrink

* `flex-shrink` 项目方大比例，默认 1，即缩小
	* `flex-shrink: <number>`

### 4.4、flex-basis

* `flex-basis` 项目占据的默认主轴空间，即放大或缩小之前项目占据的空间，浏览器根据这个属性计算主轴是否有多余空间，默认 auto
	* `flex-basis: <length> | auto`

### 4.5、flex

* `flex`属性是`flex-grow`, `flex-shrink` 和 `flex-basis`的简写
	* `flex: <flex-grow> <flex-shrink> <flex-basis>`
	* `auto (1 1 auto)`
	* `none (0 0 auto)`

### 4.6、align-self

* `align-self`属性允许单个项目有与其他项目不一样的对齐方式，可覆盖`align-items`，默认值为`auto`，表示继承父元素的`align-items`属性
	* `auto`
	* `flex-start`
	* `flex-end`
	* `center`
	* `baseline`
	* `stretch`

## 5、实战

### 5.1、骰子布局

#### 垂直居中

```html
<div class="box">
  <div class="item"></div>
</div>
```

```css
.box {
  display: flex;
  justify-content: center;
  align-items: center;
}
```

### 5.2、网格布局

### 5.3、圣杯布局

### 5.4、输入框的布局

### 5.5、悬挂式布局

### 5.7、流式布局

## reference

* [Flex 布局教程：语法篇](http://www.ruanyifeng.com/blog/2015/07/flex-grammar.html)
* [Flex 布局教程：实例篇](http://www.ruanyifeng.com/blog/2015/07/flex-examples.html)

