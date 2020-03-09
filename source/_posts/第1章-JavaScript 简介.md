---
title: 第一章 JavaScript 简介
date: 2020-1-16 23:22:54
tags: JavaScript高级程序设计
---

ECMA-262——定义一种名为 ECMAScript 的新脚本语言的标准
浏览器开发商将 ECMAScript 作为各自 JavaScript 实现的基础
> JavaScript 包括：
> * 核心： ECMAScript
> * 文档对象模型： DOM
> * 浏览器对象模型： BOM

### ECMAScript
* 没有输入和输出定义
* ECMA-262 定义了 ECMAScript 基础，在此基础上可以构建更完善的脚本语言
* 宿主环境提供基本的 ECMAScript 实现，也提供该语言的拓展，以便语言和环境之间对接交互

ECMA-262 标准规定了
* 语法
* 类型
* 语句
* 关键字
* 保留字
* 操作符
* 对象
ECMAScript 就是对实现该标准规定的各个方面内容的语言的描述
JavaScript 实现了 ECMAScript
Adobe ActionScript 也实现了 ECMAScript
###### 什么是 ECMAScript 兼容
> 要想成为 ECMAScript 的实现，必须做到：
> * 支持 ECMA-262 描述的所有“类型、值、对象、属性、函数以及程序语法和语义”
> * 支持 Unicode 字符标准
> * 可以进行拓展：
>   * 添加 ECMA-262 没有描述的“更多类型、值、对象、属性和函数”。主要指新对象和对象的新属性
>   * 支持 ECMA-262 没有定义的“程序和正则表达式语法”。即可以修改和拓展内置的正则表达式语法
>
> 为开发人员基于 ECMAScript 开发一门新语言提供了广阔的空间和极大的灵活性

### DOM
DOM（Document Object Model）是针对 XML 但经过拓展用于 HTML 的 API（Application Programming Interface）

* W3C 制定了 DOM 标准
* DOM 不止针对 JavaScript，很多别的语言也实现了 DOM
* DOM 包括 DOM 核心和 DOM HTML
    * DOM 核心规定如何映射基于 XML 的文档结构
    * DOM HTML 模块则在 DOM 核心 的基础上添加了针对 HTML 的对象和方法

除了 DOM 核心和 DOMHTML 接口之外，另外几种语言发布了只针对自己的 DOM 标准。以下语言都是基于 XML 的，每种语言的 DOM 标准都添加了与特定语言相关的新方法和新接口：
* SVG(Scalable Vector Graphic)
* MathML（Mathematical Markup Language）
* SMIL（Synchronized Multimedia Integration Language）
### BOM
开发人员使用 BOM（Browser Object Model）控制浏览器显示的页面以外的部分
BOM 作为 JavaScript 实现的一部分却没有相关的标准。H5 致力于把很多 BOM 功能写入正式规范。
根本上讲，BOM 只处理浏览器窗口和框架；但习惯上把所有针对浏览器的 JavaScript 扩展算作 BOM 的一部分。