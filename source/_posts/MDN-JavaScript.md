---
title: MDN-JavaScript
date: 2020-06-09 23:35:41
tags: JavaScript

---

# 数据类型

## 字符串

* `indexOf` 查找子串
* `slice` 分片
* `replace` 替换
* `split` 字符串转数组

## 数组

* `join` 数组转字符串（指定分隔符）
* `toString` 数组转字符串（逗号分隔）



# 事件



# 原型

## \__proto__

* 在javascript中，函数可以有属性，每个函数都有一个特殊的属性叫作原型
* 原型链中的方法和属性**没有**被复制到其他对象——它们被访问需要通过前面所说的“原型链”的方式

> 没有官方的方法用于直接访问一个对象的原型对象——原型链中的“连接”被定义在一个内部属性中，在 JavaScript 语言标准中用 `[[prototype]]` 表示（参见 [ECMAScript](https://developer.mozilla.org/zh-CN/docs/Glossary/ECMAScript)）。然而，大多数现代浏览器还是提供了一个名为 `__proto__` （前后各有2个下划线）的属性，其包含了对象的原型

## prototype

* `prototype` 属性的值是一个对象，我们希望被原型链下游的对象继承的属性和方法，都被储存在其中
	* 不在 `prototype` 对象内的成员，不会被“对象实例”或“继承自 `Object()` 的对象类型”所继承
	* 这些方法/属性仅能被 `Object()` 构造器自身使用

```js
person = function () {}
person1 = new function()
Person1.__proto__ === person.prototype
```

## constructor

* 每个实例对象都从原型中继承了一个constructor属性，该属性指向了用于构造此实例对象的**构造函数**（可以用来 new）

```js
person = function () {}
person1 = new function()
person1.constructor === person
// new function() <=> new person1.constructor()
person1.constructor.name
// "person"
```

## 原型式继承

```js
function Person(first, last, age, gender, interests) {
  this.name = {
    first,
    last
  };
  this.age = age;
  this.gender = gender;
  this.interests = interests;
};
Person.prototype.greeting = function() {
  alert('Hi! I\'m ' + this.name.first + '.');
};
```

```js
function Teacher(first, last, age, gender, interests, subject) {
  Person.call(this, first, last, age, gender, interests);

  this.subject = subject;
}

Teacher.prototype = Object.create(Person.prototype);
Teacher.prototype.constructor = Teacher;
```

> 当我们输入类似`var person1=new Person(...)`来构造对象时，JavaScript实际上参考的是`Person.prototype`指向的对象来生成`person1`。
>
> 另一方面，`Person()`函数是`Person.prototype`的构造函数，也就是说`Person===Person.prototype.constructor`

## 委托

* **委托** - 特殊的对象将功能委托给通用的对象类型完成

> 考虑到JavaScript的工作方式，由于原型链等特性的存在，在不同对象之间功能的共享通常被叫做 **委托** - 特殊的对象将功能委托给通用的对象类型完成。这也许比将其称之为继承更为贴切，因为“被继承”了的功能并没有被拷贝到正在“进行继承”的对象中，相反它仍存在于通用的对象中。



# 异步

## 线程

* 一个**线程**是一个基本的处理过程，每个线程一次只能执行一个任务
* 现在的计算机大都有多个内核（core），因此可以同时执行多个任务
* 支持多线程的编程语言可以使用计算机的多个内核，同时完成多个任务

### JavaScript 是单线程的

* 即使有多个内核，也只能在单一线程上运行多个任务，称为主线程（**main thread**）

## 异步代码

### Web workers

* 通过 [Web workers](https://developer.mozilla.org/en-US/docs/Web/API/Web_Workers_API) 可以把一些任务交给一个名为 worker 的单独的线程
	* 用一个 worker来运行一个耗时的任务，主线程就可以处理用户的交互（避免了阻塞）
	* web workers 不能访问 [DOM](https://developer.mozilla.org/zh-CN/docs/Glossary/DOM) 

### Promise

* [Promises](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)

* [fetch](https://developer.mozilla.org/en-US/docs/Web/API/WindowOrWorkerGlobalScope/fetch)
	* 现代版的，更高效的 [`XMLHttpRequest`](https://developer.mozilla.org/zh-CN/docs/Web/API/XMLHttpRequest)

### 事件队列

> 像promise这样的异步操作被放入事件队列中，事件队列在主线程完成处理后运行，这样它们就不会阻止后续JavaScript代码的运行。排队操作将尽快完成，然后将结果返回到JavaScript环境。

### 间隔

* setTimeout()
	* 在指定的时间后执行一段代码

* setInterval()
	* 以固定的时间间隔，重复运行一段代码

> ```js
> let i = 1;
> 
> setTimeout(function run() {
>   console.log(i);
>   i++;
>   setTimeout(run, 100);
> }, 100);
> ```
>
> ```js
> let i = 1;
> 
> setInterval(function run() {
>   console.log(i);
>   i++
> }, 100);
> ```
>
> 区别：
>
> - 递归 `setTimeout()` 保证执行之间的延迟相同，例如在上述情况下为100ms。 代码将运行，然后在它再次运行之前等待100ms，因此无论代码运行多长时间，间隔都是相同的。
> - 使用 `setInterval()` 的示例有些不同。 我们选择的间隔包括执行我们想要运行的代码所花费的时间。假设代码需要40毫秒才能运行 - 然后间隔最终只有60毫秒。
> - 当递归使用 `setTimeout()` 时，每次迭代都可以在运行下一次迭代之前计算不同的延迟。 换句话说，第二个参数的值可以指定在再次运行代码之前等待的不同时间（以毫秒为单位）。
>
> 当你的代码有可能比你分配的时间间隔，花费更长时间运行时，最好使用递归的 `setTimeout()` - 这将使执行之间的时间间隔保持不变，无论代码执行多长时间，你不会得到错误。
>
> 

* requestAnimationFrame()
	* 在浏览器下一次重新绘制显示之前执行指定的代码块，从而允许动画在适当的帧率下运行，而不管它在什么环境中运行

# 客户端 API

## 浏览器 API

* [DOM（文档对象模型）](https://developer.mozilla.org/zh-CN/docs/Web/API/Document_Object_Model)
	* window是载入浏览器的标签
	* navigator表示浏览器存在于web上的状态和标识（即用户代理）
	* document（在浏览器中用DOM表示）是载入窗口的实际页面
* [XMLHttpRequest](https://developer.mozilla.org/zh-CN/docs/Web/API/XMLHttpRequest)
* [Fetch API](https://developer.mozilla.org/zh-CN/docs/Web/API/Fetch_API)
* [Canvas](https://developer.mozilla.org/en-US/docs/Web/API/Canvas_API)
* [Web Storage API](https://developer.mozilla.org/zh-CN/docs/Web/API/Web_Storage_API)
* [IndexedDB API](https://developer.mozilla.org/zh-CN/docs/Web/API/IndexedDB_API)

## 画图

## 音频与视频

## 客户端存储

### 传统方法：cookie

* 过时、存在各种[安全问题](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Cookies#安全)，而且无法存储复杂数据
* 唯一优势是它们得到了非常旧的浏览器的支持

### 新流派：Web Storage 和 IndexedDB

- [Web Storage API](https://developer.mozilla.org/en-US/docs/Web/API/Web_Storage_API) 提供了一种非常简单的语法，用于存储和检索较小的、由名称和相应值组成的数据项。当您只需要存储一些简单的数据时，比如用户的名字，用户是否登录，屏幕背景使用了什么颜色等等，这是非常有用的。
- [IndexedDB API](https://developer.mozilla.org/en-US/docs/Web/API/IndexedDB_API) 为浏览器提供了一个完整的数据库系统来存储复杂的数据。这可以用于存储从完整的用户记录到甚至是复杂的数据类型，如音频或视频文件。

#### Web Storage

* 所有的 web storage 数据都包含在浏览器内两个类似于对象的结构中： [`sessionStorage`](https://developer.mozilla.org/zh-CN/docs/Web/API/Window/sessionStorage) 和 [`localStorage`](https://developer.mozilla.org/zh-CN/docs/Web/API/Window/localStorage)。 
	* sessionStorage：只要浏览器开着，数据就会一直保存；关闭浏览器时数据会丢失
	* localStorage：一直保存数据，甚至到浏览器关闭又开启后也是这样
* 每个域都有一个单独的数据存储区

#### IndexedDB

### 未来：Cache API

*  [`Cache`](https://developer.mozilla.org/zh-CN/docs/Web/API/Cache) API 是为存储特定HTTP请求的响应文件而设计的，它对于像存储离线网站文件这样的事情非常有用，这样网站就可以在没有网络连接的情况下使用。
* 缓存通常与 [Service Worker API](https://developer.mozilla.org/en-US/docs/Web/API/Service_Worker_API) 组合使用，尽管不一定非要这么做。

