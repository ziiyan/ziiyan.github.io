---
title: 第二十一章 Ajax与Comet
date: 2020-03-13 00:20:44
tags: JavaScript高级程序设计
---

AJAX - Asynchronous JavaScript + XML

向服务器请求额外的数据而无须卸载页面

核心是 XMLHttpRequest 对象，简称 XHR

使用 XHR 对象取得新数据，通过 DOM 将新数据插入页面



## XMLHttpRequest 对象

```js
var xhr = new XMLHttpRequest();
```

### XHR的用法

1. `open(请求类型, URL, 是否异步)`
	* get / post
	* 相对于当前页面 / 绝对路径
	* Boolean
2. `send(请求主体)`
	* 无内容则传入 null
3. 收到响应后，自动填充 XHR 对象的属性
	* `responseText` 做为响应主体
	* `responseXML` "text/xml" 或 "application/xml"
	* `status` 响应的 HTTP 状态
		* 200 成功，responseText 就绪 
		* 304 资源未修改，可使用缓存
	* `statusText` HTTP 状态的说明
	* `readyState` 异步请求的活动阶段
		* 0 位初始化
		* 1 启动
		* 2 发送
		* 3 接收
		* 4 完成
		* 值每次改变都触发 `readystatechange` 事件
	* 收到响应前调用 `abort()` 取消异步请求



### HTTP头部信息

XHR 提供操作请求头部和响应头部信息的方法

默认情况，发送 XHR 请求的同时还会发送以下头部信息

* Accept：浏览器能够处理的内容类型
* Accept-Charset：浏览器能够显示的字符集
* Accept-Encoding：浏览器能够处理的压缩编码
* Accept-Language：浏览器当前设置的语言
* Connection：浏览器与服务器之间连接的类型
* Cookie：当前页面设置的任何 Cookie
* Host：发出请求的页面所在的域
* Referer：发出请求的页面的 URI
* User-Agent：浏览器的用户代理字符串



* `setRequestHeader(头部字段名称，头部字段值)` 
	* 设置自定义的请求头部信息
	* open 之后 send 之前

* `getRequestHeader(头部字段名称)`
* `getAllResponseHeaders()`



### GET 请求

每个参数的名称和值必须使用 `encodeURIComponent()` 编码才能放到 URL 末尾，所有名-值对都由 & 分隔

### POST 请求

`serialize()` 格式化数据



## XMLHttpRequest 2 级

* FormData 序列化表单

```js
var data = new FormData();
data.append(键, 值);
```

* 超时设定 `timeout`属性

open 之后 send 之前设定，请求在等待响应多少毫秒之后停止

触发 timeout 事件，调用 ontimeout 事件处理程序

* `overrideMimeType()` 方法

重写 XHR 响应的 MIME 类型，send 之前调用



## 进度事件

* loadstart：接收到响应数据的第一个字节时触发
* progress：接收响应期间不断地触发
* error：请求发生错误时触发
* abort：在因为调用 abort() 方法而终止连接时触发
* load：在接收到完整的响应数据时触发
* loaded：在通信完成或者触发 error、abort 或 load 事件后触发



## 跨资源共享 CORS

基本思想：使用自定义的 HTTP 头部让浏览器与服务器进行沟通

发送：`Origin：http://www...`

接收：`Access-Control-Allow-Origin：http://www...`



* 在 XHR 对象的 open 中传入绝对 URL 可以请求跨域，：限制
	* 不能使用 setRequestHeader() 设置自定义头部
	* 不能发送和接收 cookie
	* 调用 getAllResponseHeaders() 会返回空字符串

* Preflighted Requests 透明服务器验证机制

	* 发送
		*  Origin：与简单的请求相同
		* Access-Control-Request-Method:请求自身使用的方法
		* Access-Control-Request-Headers:(可选)自定义的头部信息，多个头部以逗号分隔
	* 接收
		* Access-Control-Allow-Origin：与简单的请求相同
		*  Access-Control-Allow-Methods：允许的方法，多个方法以逗号分隔
		* Access-Control-Allow-Headers：允许的头部，多个头部以逗号分隔
		* Access-Control-Max-Age：应该将这个 Preflight 请求缓存多长时间(以秒表示)

* 带凭据的请求

	* 默认跨源请求不提供凭据（cookie、HTTP 认证及客户端 SSL 证明）
	* 将 withCredentials 属性设置为 true 可以指定某个请求应该发送凭据
	* 响应：`    Access-Control-Allow-Credentials: true`

* 跨浏览器的 CROS

	* 所有浏览器都支持简单的（非 Preflight 和不带凭据的）请求

	

## 其他跨域技术

* 图像 Ping
	*  `<img>` 标签
	* 只能发送 GET 请求
	* 无法访问服务器的响应文本
* JSONP - JSON with padding
	* 包含两部分：回调函数和数据
	* 通过 `<script>` 元素使用
	* 能直接访问响应文本
	* 从其他域中加载代码执行，不安全
	* 很难确定 JSONP 请求是否成功
* Comet 服务器推送
	* 长轮询：浏览器发送请求，服务器保持连接，直到有数据发送才发送数据。浏览器收到数据后关闭连接，再重新发起新请求
	* HTTP 流：浏览器发送请求，服务器保持连接，周期性地向浏览器发送数据
* SSE 服务器发送事件
	* 围绕只读 Comet 交互推出的 API 或者模式
* Web Socket
	* 单独的持久连接上提供全双工、双向通信
* SSE 与 Web Socket
	* Web Socket 需要服务器支持，双向通信
	* SSE 不需要服务器支持，一般单向，结合 XHR 可以双向

## 安全

* 要求以 SSL 连接来访问可以通过 XHR 请求的资源
* 要求每一次请求都要附带经过响应算法计算得到的验证码

以下措施对防范 CSRF（Cross-Saite Request Forgery）攻击不起作用

* 要求发送 POST 而不是 GET —— 容易改变
* 检查来源 URL 以确定 是否可信 —— 容易伪造
* 基于 cookie 信息进行验证 ——容易伪造





















