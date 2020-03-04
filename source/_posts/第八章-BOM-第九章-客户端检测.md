---
title: 第八章 BOM & 第九章 客户端检测
date: 2020-03-02 23:41:03
tags: JavaScript高级程序设计
---

# 第八章 BOM
## window 对象
* BOM 的核心对象是 window，表示浏览器的一个实例
### 全局作用域
* Window 作为 Global 对象，所有在全局作用域中声明的变量、函数都会变成 window 对象的属性和方法
* 全局变量不能通过 delete 删除，直接在 window 对上定义的属性可以

### 窗口位置
* `window.screenLeft`
* `window.screenTop`
* `window.screenX`
* `window.screenY`
* `window.moveTo(x, y)`
* `window.moveBy(a, b)`

### 窗口大小
* `window.innerWidth`
* `window.innerHeight`
* `window.outerWidth`
* `window.outerHeight`
* `window.resizeTo(w, h)`
* `window.resizeBy(w, h)`

### 导航和打开窗口
* `window.open(URL, target, String, Boolean)`

### 间歇调用和超时调用
* `setTimeout(function, ms)`
    * ms 后把当前任务添加到任务队列，如果此时任务队列是空的才会立即执行
        * 所以不一定 ms 后就会执行 function
    * 返回一个数值 ID
* `clearTimeout(timeoutID)`
* `setInterVal(function, ms)`
* `clearInterval(timeoutID)`

### 系统对话框
样式由操作系统或浏览器决定
显示这些对话框的时候代码会停止执行

* `alert()`
* `confirm()`
* `prompt()`
* `find()`
* `print()`

## location 对象
* 既是 window 对象的属性，也是 document 对象的属性
* window.LOcation 和 document.location 引用的是同一个对象

### 位置操作
* `location.assign(url)`
* `window.location = "url"`
* `location.href = "url"`
修改 location 其它属性页面都会重新加载，浏览器的历史记录会生产一条新记录

* `replace(url)` 不会产生新记录
* `reload()` 
    * 无参数，可能是用缓存
    * 参数 true，强制从服务器重新加载
    * 调用之后的代码可能会也可能不会执行，这要取决于网络延迟或系统资源等因素。最好将 reload()放在代码的最后一行

## screen 对象
## history 对象

* `history.go()`
    * number 前进或后退页面
    * string 跳转到历史记录中包含string最近的位置
* `history.back()` 后退
* `history.forward()` 前进
* `history.length` 历史记录数量

# 第九章 客户端检测
先设计最通用的方案，再使用客户端检测
