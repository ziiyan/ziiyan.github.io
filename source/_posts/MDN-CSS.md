---
title: MDN-CSS(Cascading Style Sheets)
date: 2020-06-07 14:05:20
tags: CSS
---

# CSS 工作原理

1. 浏览器载入HTML文件（比如从网络上获取）。
2. 将HTML文件转化成一个DOM（Document Object Model）。
3. 接下来，浏览器会拉取该HTML相关的大部分资源，比如嵌入到页面的图片、视频和CSS样式。JavaScript则会稍后进行处理。
4. 浏览器拉取到CSS之后会进行解析，根据选择器的不同类型（比如element、class、id等等）把他们分到不同的“桶”中。浏览器基于它找到的不同的选择器，将不同的规则（基于选择器的规则，如元素选择器、类选择器、id选择器等）应用在对应的DOM的节点中，并添加节点依赖的样式（这个中间步骤称为渲染树）。
5. 上述的规则应用于渲染树之后，渲染树会依照应该出现的结构进行布局。
6. 网页展示在屏幕上（这一步被称为着色）。

![img](https://mdn.mozillademos.org/files/11781/rendering.svg)

# 冲突规则

## 层叠

* 同级，后面 > 前面

## 优先级

* **千位**： 如果声明在 `style` 的属性（内联样式）则该位得一分。这样的声明没有选择器，所以它得分总是1000。
* **百位**： 选择器中包含ID选择器则该位得一分。
* **十位**： 选择器中包含类选择器、属性选择器或者伪类则该位得一分。
* **个位**：选择器中包含元素、伪元素选择器则该位得一分。

> 在进行计算时不允许进行进位，例如，20 个类选择器仅仅意味着 20 个十位，而不能视为 两个百位，也就是说，无论多少个类选择器的权重叠加，都不会超过一个 ID 选择器。

* `!important` 覆盖普通规则的层叠

## 继承

* 有些属性可以被继承，有些不行

* 控制继承，所有属性都可以取以下三个值
	* `inherit` 开启继承
	* `initial` 不继承
	* `unset` 自然继承



# 选择器

| 选择器                                                       | 示例                | 学习CSS的教程                                                |
| :----------------------------------------------------------- | :------------------ | :----------------------------------------------------------- |
| [类型选择器](https://developer.mozilla.org/zh-CN/docs/Web/CSS/Type_selectors) | `h1 { }`            | [类型选择器](https://developer.mozilla.org/zh-CN/docs/user:chrisdavidmills/CSS_Learn/CSS_Selectors/Type_Class_and_ID_Selectors#Type_selectors) |
| [通配选择器](https://developer.mozilla.org/zh-CN/docs/Web/CSS/Universal_selectors) | `* { }`             | [通配选择器](https://developer.mozilla.org/zh-CN/docs/user:chrisdavidmills/CSS_Learn/CSS_Selectors/Type_Class_and_ID_Selectors#The_universal_selector) |
| [类选择器](https://developer.mozilla.org/zh-CN/docs/Web/CSS/Class_selectors) | `.box { }`          | [类选择器](https://developer.mozilla.org/zh-CN/docs/user:chrisdavidmills/CSS_Learn/CSS_Selectors/Type_Class_and_ID_Selectors#Class_selectors) |
| [ID选择器](https://developer.mozilla.org/zh-CN/docs/Web/CSS/ID_selectors) | `#unique { }`       | [ID选择器](https://developer.mozilla.org/zh-CN/docs/user:chrisdavidmills/CSS_Learn/CSS_Selectors/Type_Class_and_ID_Selectors#ID_Selectors) |
| [标签属性选择器](https://developer.mozilla.org/zh-CN/docs/Web/CSS/Attribute_selectors) | `a[title] { }`      | [标签属性选择器](https://developer.mozilla.org/zh-CN/docs/User:chrisdavidmills/CSS_Learn/CSS_Selectors/Attribute_selectors) |
| [伪类选择器](https://developer.mozilla.org/zh-CN/docs/Web/CSS/Pseudo-classes) | `p:first-child { }` | [伪类](https://developer.mozilla.org/zh-CN/docs/User:chrisdavidmills/CSS_Learn/CSS_Selectors/Pseuso-classes_and_Pseudo-elements#What_is_a_pseudo-class) |
| [伪元素选择器](https://developer.mozilla.org/zh-CN/docs/Web/CSS/Pseudo-elements) | `p::first-line { }` | [伪元素](https://developer.mozilla.org/zh-CN/docs/User:chrisdavidmills/CSS_Learn/CSS_Selectors/Pseuso-classes_and_Pseudo-elements#What_is_a_pseudo-element) |
| [后代选择器](https://developer.mozilla.org/zh-CN/docs/Web/CSS/Descendant_combinator) | `article p`         | [后代运算符](https://developer.mozilla.org/zh-CN/docs/User:chrisdavidmills/CSS_Learn/CSS_Selectors/Combinators#Descendant_Selector) |
| [子代选择器](https://developer.mozilla.org/zh-CN/docs/Web/CSS/Child_combinator) | `article > p`       | [子代选择器](https://developer.mozilla.org/zh-CN/docs/User:chrisdavidmills/CSS_Learn/CSS_Selectors/Combinators#Child_combinator) |
| [相邻兄弟选择器](https://developer.mozilla.org/zh-CN/docs/Web/CSS/Adjacent_sibling_combinator) | `h1 + p`            | [相邻兄弟](https://developer.mozilla.org/zh-CN/docs/User:chrisdavidmills/CSS_Learn/CSS_Selectors/Combinators#Adjacent_sibling) |
| [通用兄弟选择器](https://developer.mozilla.org/zh-CN/docs/Web/CSS/General_sibling_combinator) | `h1 ~ p`            | [通用兄弟](https://developer.mozilla.org/zh-CN/docs/User:chrisdavidmills/CSS_Learn/CSS_Selectors/Combinators#General_sibling) |



# 盒模型

## 外部显示类型

### 块级

- `display: block`
- 每个盒子都会换行
- `width` 和 `height` 属性可以发挥作用
- `padding`、`margin`、`border` 会将其他元素从当前盒子周围“推开”
- 盒子会在内联的方向上扩展并占据父容器在该方向上的所有可用空间，在绝大数情况下意味着盒子会和父容器一样宽

### 内联

- `display: inline`

- 盒子不会产生换行
-  `width` 和 `height` 属性将不起作用
- 垂直方向的`padding`、`margin`、`border`会被应用但是不会把其他处于 `inline` 状态的盒子推开
- 水平方向的`padding`、`margin`、`border`会被应用而且也会把其他处于 `inline` 状态的盒子推开

### 中间状态

- `display: inlin-block`
- 不会换行

- 设置`width` 和`height` 属性会生效。
- `padding`, `margin`, 以及`border` 会推开其他元素。



## 内部显示类型

* flex
* grid



## 盒模型

- **Content box**: 这个区域是用来显示内容
- **Padding box**: 包围在内容区域外部的空白区域
	- 不可以为负数
- **Border box**: 边框盒包裹内容和内边距
- **Margin box**: 这是最外面的区域，是盒子和其他元素之间的空白区域
	- 可以为负数
	- 注意**外边距折叠**

### 标准盒模型

* `box-sizing: content-box`
* width 和 height 包含 content box

### 替代（IE）盒模型

* `box-sizing: border-box`

* width 和 height 包含 content box、padding box、border box



# 溢出

## 块级排版上下文（Block Formatting Context，BFC）



# CSS 的值

## 数字，长度和百分比

### 绝对长度单位

| 单位 | 名称         |
| :--- | :----------- |
| `cm` | 厘米         |
| `mm` | 毫米         |
| `Q`  | 四分之一毫米 |
| `in` | 英寸         |
| `pc` | 十二点活字   |
| `pt` | 点           |
| `px` | 像素         |

### 相对长度单位

| 单位   | 相对于                                                       |
| :----- | :----------------------------------------------------------- |
| `em`   | 在 font-size 中使用是相对于父元素的字体大小，在其他属性中使用是相对于自身的字体大小，如 width |
| `ex`   | 字符“x”的高度                                                |
| `ch`   | 数字“0”的宽度                                                |
| `rem`  | 根元素的字体大小                                             |
| `lh`   | 元素的line-height                                            |
| `vw`   | 1vw 视窗宽度的1%                                             |
| `vh`   | 1vh 视窗高度的1%                                             |
| `vmin` | 1vmin 视窗较小尺寸的1%                                       |
| `vmax` | 1vmax 视图大尺寸的1%                                         |

#### ems and rems

* `em` 父元素字体大小
* `rem` 根元素字体大小

## 颜色

## 图片

## 位置

## 字符串和标识符

## 函数



# 编码风格

## 方法论

### OOCSS

### BEM

* [BEM](http://getbem.com/) 块级元素修饰字符（Block Element Modifier）

## SASS

* Sass（Syntactically Awesome StyleSheets）严格缩进
* Scss（Sassy CSS）兼容 css 的 sass

### 定义变量

原生css也可以



# 布局

## 正常布局流（默认）

### 覆盖默认布局

* display
	* block、inline、inline-block 是正常布局流
	* 其他（如 grid、flex）不是
* 浮动
	* float
* position
	* 默认 static，正常布局流
* 表格布局
	* display: table
* 多列布局

## 弹性盒子（Flexbox）

* 一维

* `display: flex` **直接子元素**会按照flex进行布局

## 网格（Grid）

* 二维

* `display: grid`

## 浮动（float）

* 从正常布局流(normal flow)中移除
* 其他内容会在这个被设置浮动的元素周围环绕
* 清除浮动 `clear`
	* `left`：停止任何活动的左浮动
	* `right`：停止任何活动的右浮动
	* `both`：停止任何活动的左右浮动

## 定位（position）

### static

* 将元素放在文档布局流的默认位置

### relative

* 相对于元素在正常的文档流中的位置移动它

### absolute

* 将元素完全从页面的正常布局流中移出，类似将它单独放在一个图层中
* 相对于该元素的**最近被定位祖先元素**

### fixed

* 相对浏览器视口固定

### sticky

* 让元素先保持和`position: static`一样的定位
* 当它的相对视口位置达到某一个预设值时，像`position: fixed`一样定位

## CSS 表格布局（table）

##多列布局

* column-count
* column-width
* column-gap
* column-rule



# 响应式设计

* RWD，responsive web design（允许Web页面适应不同屏幕宽度因素等，进行布局和外观的调整）

## 媒体查询

```css
@media media-type and (media-feature-rule) {
  /* CSS rules go here */
}
```

- 一个媒体类型，告诉浏览器这段代码是用在什么类型的媒体上的（例如印刷品或者屏幕）
	- `all`
	- `print`
	- `screen`
	- `speech`
- 一个媒体表达式，是一个被包含的CSS生效所需的规则或者测试
	- 宽和高
	- 朝向 orientation
		- portrait
		- landscape
	- hover
	- pointer
- 一组CSS规则，会在测试通过且媒体类型正确的时候应用

- 媒体查询匹配逻辑：与/或/非

> **移动优先**设计：为窄屏设备（例如移动设备）创建一个简单的单栏布局，然后检查是否是大些的屏幕，在你知道你有足够容纳的屏幕宽度的时候，开始采用一种多栏的布局 

## 灵活网格

* 每个元素都有一个作为宽度的百分数，而且确保整个布局的和不会超过100%

## 现代布局技术

### 多列布局

### flex

### Grid

## 响应式图像

* `<picture>`
* `<img>`
* `srcset`
* `sizes`

## 响应式排版

* 通过媒体查询改变字体大小
* 使用视口单位实现响应式排版

## 视口元标签

```html
<meta name="viewport" content="width=device-width,initial-scale=1">
```

* 移动端浏览器因此会把视口宽度设为960像素，并以这个宽度渲染页面
* 用设备的实际宽度 `device-width` 覆写了默认的 `width=960px`



# @规则

* [`@charset`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/@charset), 定义样式表使用的字符集.
* [`@import`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/@import), 告诉 CSS 引擎引入一个外部样式表.
* [`@namespace`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/@namespace), 告诉 CSS 引擎必须考虑XML命名空间。
* 嵌套@规则, 是嵌套语句的子集,不仅可以作为样式表里的一个语句，也可以用在条件规则组里：
	- [`@media`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/@media), 如果满足媒介查询的条件则条件规则组里的规则生效。
	- [`@page`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/@page), 描述打印文档时布局的变化.
	- [`@font-face`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/@font-face), 描述将下载的外部的字体。 
	- [`@keyframes`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/@keyframes), 描述 CSS 动画的中间步骤 . 
	- [`@supports`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/@supports), 如果满足给定条件则条件规则组里的规则生效。 
	- [`@document`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/@document), 如果文档样式表满足给定条件则条件规则组里的规则生效。 *(推延至 CSS Level 4 规范)*

