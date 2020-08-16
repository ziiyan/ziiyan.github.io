---
title: 三种主流的Web服务实现方案(REST+SOAP+RPC)
date: 2020-06-03 22:27:52
tags: 其它
---

# RPC

* Remote recedure call，远程过程调用
	* 像调用本地服务（方法）一样调用服务器的服务（方法）



# SOAP

* Simple Object Access Protocol，简单对象访问协议



# REST

* Representation State Transfer，表现层状态转移
	* Resource Representation State Transfer
	* 资源在网络中以某种表现形式进行状态转移
		* Resource：资源，即数据（前面说过网络的核心）。比如 newsfeed，friends等
		* Representational：某种表现形式，比如用JSON，XML，JPEG等
		* State Transfer：状态变化。通过HTTP动词实现

> 看 url 就知道要什么
> 看 http method 就知道干什么
> 看 http status code 就知道结果如何
>
> ---
>
> 用URL定位资源，用HTTP描述操作