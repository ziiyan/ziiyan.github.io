---
title: 第二章 在 HTML 中使用 JavaScript
date: 2020-2-9 23:22:54
tags: JavaScript高级程序设计
---

## `script`元素
##### 属性
* src 可选。包含要执行代码的外部文件。
> 如果有 src，标签内嵌入的 JavaScript 代码会被忽略
> 可以包含来自外部域的 JavaScript 文件。这点与 `img` 相同

* async 可选。立即下载脚本，但不妨碍页面中其他操作。只对外部脚本文件有效。**不能保证按顺序执行。不要修改 DOM 会在load 事件前执行，可能会在 `DOMContentLoaded` 事件触发之前或之后执行。**
* defer 可选。延迟到文档完全被解析和显示之后（`/html`标签后）执行。**只对外部脚本文件有效。**（嵌入脚本的 defer 属性会被忽略）**延迟脚本不一定会按照顺序执行，也不一定会在 `DOMContentLoaded` 事件触发前执行，因此最好只有一个延迟脚本。**
> 只要不存在 defer 和 async 属性，浏览器都会按照 `scrip` 元素在页面中出现的先后顺序进行解析


* type 可选。language（已废弃）的替代属性；脚本语言的内容类型（MIME 类型）。默认值 text/javascript
> 不推荐 text/javascript 和 text/ecmascript
> 服务器在传送 JavaScript 文件时使用的 MIME 类型通常是 application/x-javascript
> 非 IE 浏览器中可以使用 application/javascript 和 application/ecmascript
* charset 可选。表示通过 src 属性指定代码的字符集。大多数浏览器忽略

### 标签的位置
放在 `head` 中必须先把 js 代码下载、解析和执行完成后才能开始呈现页面内容，此时页面空白。
把 js 引用放在 `body` 中，空白时间会缩短。
或者设置 defer 属性，立即下载，延迟（`/html`标签后）执行。

## 外部文件的优点
* 可维护性：集中精力编辑 js 代码
* 可缓存：如果两个页面使用同一个文件，只需下载一次
* 适应未来

## 文档模式
`!DOCTYPE html`

## `noscript`元素
* 浏览器不支持脚本
* 浏览器支持脚本，但脚本被禁用
符合上述条件，元素内容会被显示。