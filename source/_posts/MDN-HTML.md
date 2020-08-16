---
title: MDN-HTML
date: 2020-06-04 21:42:32
tags: HTML
---



# 语义

## 为什么需要结构化

* 用户经常只阅读开头的标题
* SEO
* 屏幕阅读器



## 为什么需要语义

* SEO
* 屏幕阅读器



## 语义化标签

### 标题/段落

* `<h1> ~ <h6>`

* `<p>`

> 最好只对每个页面使用一次 <h1> 

### 列表 Lists

* 无序 `<ul><li></li></ul>`

* 有序 `<ol><li></li></ol>`

### 嵌套列表

```html
<ol>
  <li>item</li>
  <li>item</li>
  <li>
    <ul>
      <li>item</li>
      <li>item</li>
    </ul>
  </li>
</ol>
```

### 强调

* `<em>`

* `<strong>`



### **表象元素**（没有语义）

* `<i>` 被用来传达传统上用斜体表达的意义：外国文字，分类名称，技术术语，一种思想……

* `<b>` 被用来传达传统上用粗体表达的意义：关键字，产品名称，引导句……

* `<u>`被用来传达传统上用下划线表达的意义：专有名词，拼写错误……



### 描述列表

* `<dl>` description list - 描述列表
* `<dt>` description term - 描述术语
* `<dd>` description description - 描述列表的描述部分

> dt 与 dd 之间有缩进



### 引用

* 块引用 `<blockquote cite="http://xxx...">` cite 不显示
* 行内引用 `<q cite="http://xxx...">` cite 不显示
* 引文 `<cite>MDN</cite>` 斜体，显示在页面上



### 缩略语

* `<abbr title="全称">缩写</abbr>`



### 上标/下标

* `<sub>`
* `<sup>`



### 构建内容

* `<header> `页眉

* `<nav>` 导航栏
* `<main>` 主内容，存放每个页面独有的内容。每个页面上只能用一次 `<main>`，且直接位于`<body>` 中。最好不要把它嵌套进其它元素。
	* `<article>` 包围的内容即一篇文章，与页面其它部分无
	* `<section>`
	* `<div>`
* `<aside>` 侧边栏，嵌套在 `<main>` 中
* `<footer>`



### 换行水平分割线

* `<br>`
* `<hr>` 段落级元素之间的主题转换



## 无语义（少用）

* `<div>`
* `<span>`





### 代码

- `<code>` 用于标记计算机通用代码。
- `<pre>` 用于保留空白字符（通常用于代码块）
- `<var>`用于标记具体变量名。
- `<kbd>` 用于标记输入电脑的键盘（或其他类型）输入。
- `<samp>` 用于标记计算机程序的输出。



### 时间

* `<time datetime="">`



### 说明文字

* `<figure>`
* `<figcaption>`

```html
<figure>
  <img src="..." />
  <figcaption>描述说明</figcaption>
</figure>
```



>  [HTML元素参考](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element)



# 超链接

* 尽可能使用相对链接
	* 首先，检查代码要容易得多——相对URL通常比绝对URL短得多，这使得阅读代码更容易。
	* 其次，在可能的情况下使用相对URL更有效。
		* 当使用绝对URL时，浏览器首先通过DNS查找服务器的真实位置，然后再转到该服务器并查找所请求的文件。另一方面，相对URL，浏览器只在同一服务器上查找被请求的文件。
		* 因此，如果你使用绝对URL而不是相对URL，你就会不断地让你的浏览器做额外的工作，这意味着它的效率会降低。



# 多媒体&嵌入

* `<video>` 视频
	* `<resource src="xxx" type="video/mp4">`
		* 没有 `type` 会顺序尝试加载
	* `<video>` 中多个 `<resource>` ，播放第一个匹配的源

* `<audio>` 音频
* `<iframe>`
	* `sandbox` 提高安全性
* `<embed>、<object>` 少用

> Iframe - 安全问题
>
> * 黑客利用 iframe 嵌入交互组件，例如按钮、表单，诱导用户点击
> * 只在有必要时嵌入
> * 使用 HTTPS
> 	* HTTPS减少了远程内容在传输过程中被篡改的机会，
> 	* HTTPS防止嵌入式内容访问您的父文档中的内容，反之亦然。
> * 始终使用sandbox属性
> * 配置CSP指令



# svg

- 位图使用像素网格来定义 — 一个位图文件精确得包含了每个像素的位置和它的色彩信息。流行的位图格式包括 Bitmap (`.bmp`), PNG (`.png`), JPEG (`.jpg`), and GIF (`.gif`.)
- 矢量图使用算法来定义 — 一个矢量图文件包含了图形和路径的定义，电脑可以根据这些定义计算出当它们在屏幕上渲染时应该呈现的样子。

### img

#### 优点

- 快速，熟悉的图像语法与`alt`属性中提供的内置文本等效。
- 可以通过在[``](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/a)元素嵌套`<img>`，使图像轻松地成为超链接。

#### 缺点

- 无法使用JavaScript操作图像。
- 如果要使用CSS控制SVG内容，则必须在SVG代码中包含内联CSS样式。 （从SVG文件调用的外部样式表不起作用）
- 不能用CSS伪类来重设图像样式（如`:focus`）。



### 代码

#### 优点

- 将 SVG 内联减少 HTTP 请求，可以减少加载时间。
- 您可以为 SVG 元素分配`class`和`id`，并使用 CSS 修改样式，无论是在SVG中，还是 HTML 文档中的 CSS 样式规则。 实际上，您可以使用任何 [SVG外观属性](https://developer.mozilla.org/zh-CN/docs/Web/SVG/Attribute#Presentation_attributes) 作为CSS属性。
- 内联SVG是唯一可以让您在SVG图像上使用CSS交互（如`:focus`）和CSS动画的方法（即使在常规样式表中）。
- 您可以通过将 SVG 标记包在[``](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/a)元素中，使其成为超链接。

#### 缺点

- 这种方法只适用于在一个地方使用的SVG。多次使用会导致资源密集型维护（resource-intensive maintenance）。
- 额外的 SVG 代码会增加HTML文件的大小。
- 浏览器不能像缓存普通图片一样缓存内联SVG。
- 您可能会在[``](https://developer.mozilla.org/zh-CN/docs/Web/SVG/Element/foreignObject) 元素中包含回退，但支持 SVG 的浏览器仍然会下载任何后备图像。你需要考虑仅仅为支持过时的浏览器，而增加额外开销是否真的值得。



### iframe

* 不建议

#### 缺点

- 如你所知， `iframe`有一个回退机制，如果浏览器不支持`iframe`，则只会显示回退。
- 此外，除非 SVG 和您当前的网页具有相同的 [origin](https://developer.mozilla.org/zh-CN/docs/Glossary/源)，否则你不能在主页面上使用 JavaScript 来操纵 SVG。



# 响应式图片

* `<img>`
	* srcset 属性
	* sizes 属性

> `<meta name="viewport" content="width=device-width"></meta>`
>
>  这行代码会强制地让手机浏览器采用它们真实可视窗口的宽度来加载网页

* `<picture>`
	* `<source>`
	* `<img>`

> 为什么不用 CSS 或 JavaScript 来做响应式？
>
> 性能问题：先加载 HTML，再用 JavaScript 检测可视窗口的宽度，如果不合适再加载图片替换已经加载的图片。



# 表格

* `<colgroup>`
	* `<col>`

* `<caption>` 表格标题

* `scope` 行/列标题
* `id`
* `header`

