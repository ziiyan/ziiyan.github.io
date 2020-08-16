---
title: 学习笔记：CSS布局学习指南
date: 2020-07-31 10:01:37
tags: CSS
---

## 1、正常文档流（Normal Flow）

* 没有用任何CSS来改变页面布局的网页
* 在正常流中，元素盒子（boxes）会基于文档的写作模式（writing mode）一个接一个地排列

### 1.1、通过页面结构来发挥正常文档流的优势

* CSS加载失败了，用户仍然能阅读你的页面内容
* 阅读器会按照元素在文档中的位置来读取页面内容
* 内容顺序和用户预期的阅读顺序一致则不必布局
* **思考如何布局之前需要认真思考文档结构**

### 1.2、脱离正常文档流

* 一旦你有了一个结构良好的页面，你就需要去决定如何利用它并将它变为我们需要的布局结构

## 2、浮动（Float）

* 浮动被用来将盒子（box）置于左侧或右侧，同时让内容环绕其展示

### 2.1、清除浮动

* 一旦你对一个元素应用了浮动，所有接下来的元素都会环绕它直到内容处于它下方且开始应用正常文档流
* 当你不想要某个元素受到其之前的浮动元素影响时，为其添加`clear`属性即可
* 最常见的清除浮动的hack方案是：在容器内添加一个CSS伪元素，并将其`clear`属性设置为both

```css
.container::after {
  content: "";
  display: table;
  clear: both;
}
```

### 2.2、块级格式上下文（Block Formatting Context）

* 清除浮动的另一个方法是在容器内创建BFC
* 一个BFC元素完全包裹住了它内部的所有元素，包括内部的浮动元素，保证浮动元素不会超出其底部
* 创建BFC的方式有很多种，其中最常用的一种清除浮动的方式是为元素设置除visible（默认）之外的`overflow`属性值

```css
.container {
    overflow: auto;
}
```

* 副作用：滚动条
* 为了使清除浮动的意图更加直观，并且避免BFC的负面影响，你可以使用`flow-root`作为`display`属性的值

```css
.container {
    display: flow-root;
}
```

### 2.3、浮动的一些遗留用法

* 在新的布局方式出现以前，`float`经常会被用来创建多栏布局
* 不建议再使用

## 3、定位（position）

* 当处于正常文档流时，元素的`position`属性为`static`

* 当改变元素的position属性时，通常情况下也会设置一些偏移量（top / left / bottom / right）来使元素相对于参照点进行一定的移动
* 不同的position值会产生不同的参照点

### 3.1、相对定位（relative）

* 参照位：元素在正常文档流中的位置
* 保留正常文档流中的位置

### 3.2、绝对定位（absolute）

* 参照位：position 不为 static 的父元素
* 从正常文档流中移除

### 3.3、固定定位（fixed）

* 参照位：视口
* 从正常文档流中移除

### 3.4、粘性定位（sticky）

* 让元素在页面滚动时如同在正常流中，但当其滚动到相对于视口的某个特定位置时就会固定在屏幕上，如同fixed一般
	* 导航
	* 菜单
* 比较新，有兼容性问题

## 4、弹性布局（Flex Layout）

* 一维布局（按行或者列）
* 使用`display: flex`来将元素变为弹性布局
* 直接子元素会变为弹性项（flex item），并按行排列

### 4.1、弹性盒子的轴（axis）

* 主轴：通过设置 `flex-direction` 属性
	*  `row` (默认)
	* `column`
* 交叉轴：与主轴垂直

### 4.2、方向和次序

* 通过设置`flex-direction` 属性来改变主轴方向
	* `row-reverse`
	* `column-reverse`
* 也可以通过设置 `order` 属性，改变某一个弹性项的顺序

### 4.3. 一些Flex的属性

* 控制弹性项在主轴上空间大小
	* flex-grow：如果为正数，允许拉伸，值越大拉伸比例越大
	* flex-shrink：如果为正数，元素溢出容器时会进行收缩，值越大压缩越多
	* flex-basis：为弹性项设置初始大小
		* 大多数情况下容器元素大小不会正好被分为许多初始大小的项，而是可能有一些不足或剩余空间。`flex-grow`和`flow-shrink`属性允许我们在容器大小不足或有空余时控制各个弹性项的大小
	* 简写 `flex: [flex-grow] [flex-shrink] [flex-basis]`

## 5、网格布局（Grid Layout）

* 二维布局（按行和列）
* `grid: grid`
* 使用`grid-template-columns`和`grid-template-rows`属性来控制网格中的行与列，默认值为`auto`
	* 常用的模式是为网格制定列宽度，但是允许网格按需添加行
* `fr`是一种弹性单位，它可以指定网格容器内的空间被如何划分

### 5.1、关于网格的一些术语

* `inline axis` 
* `block axis`
* `grid line`
* `grid cell`
* `grid area`

### 5.2、网格的自动排列规则

* 网格容器的直接子元素就会开始将它们自己一个一个地放置在网格的单元格中
* 网格中任何没有被进行定位的直接子元素都会根据自动排列规则进行放置

### 5.3、基于行/列的基本定位方法

* 定位网格元素最简单的方式是使用基于行/列（line）的定位方法，只需告诉浏览器从哪一排到哪一排来进行合并
	* `grid-column-start`
	* `grid-column-end`
	* `grid-row-start`
	* `grid-row-end`
	* 缩写 `grid-column: [start] / [end]`、`grid-row: [start] / [end]`

### 5.4、通过命名区域来定位元素

* `grid-area` 设置元素名字
* `grid-template-areas` 设置布局方式
* `.` 空出单元格

```css
.item1 {
    grid-area: a;
}

.item2 {
    grid-area: b;
}

.item3 {
    grid-area: c;
}

.container {
    display: grid;
    grid-template-columns: 1fr 1fr 1fr 1fr;
    grid-template-areas: 
     "a a b b"
     "a a c c";
}
```

## 6、显示顺序和文档顺序（visual and document order）

* 从上到下的阅读顺序来组织文档顺序
* 会有助于可读性和CSS的布局方式

## 7、盒模型的生成（box generation）

* 写在网页里的任何东西都会生成一个盒子（box）

### 7.1、不生成盒子或内容（`display: none`）

* 不会被展示，并且不会保留其本该占有的空间

### 7.2 不生成该元素，但是生成其所有子元素（`display: contents`）

* 自身的盒子不生成但所有的子元素都会照常生成
	* 如果希望一个弹性布局或网格布局中的非直接子元素能应用这些布局，这就会非常有用

## 8、对齐

* `justify-content`
* `align-content`
* `place-content`
* `justify-items`
* `align-items`
* `place-items`
* `justify-self`
* `align-self`
* `place-self`
* `row-gap`
* `column-gap`
* `gap`

## 9、多栏布局（多列布局）

* 支持创建多栏的布局类型
* 当展示一组高度不同的卡片或产品时多栏布局也非常有用

### 9.1、设置栏的宽度

```css
.container {
    column-width: 300px;
}
```

### 9.2、设置栏的数目

```css
.container {
    column-count: 3;
}
```

### 9.3、间距和栏规则

* `column-gap` 间距
	* 默认 `1em`
* `column-rule` 两栏间规则，类似 border，不占用空间
	* `column-rule-width`
	* `column-rule-color`
	* `column-rule-style`

### 9.4、允许元素横跨多栏

* `column-span`
	* `all`
	* `none`

## 10、碎片化（Fragmentation）

* `break-inside: avoid`：确保卡片不会被截为两半分到不同的栏
* `reak-after: avoid`：元素后禁止断行

## reference

* [一篇全面的CSS布局学习指南 [译]](https://juejin.im/post/6844903634849759239)