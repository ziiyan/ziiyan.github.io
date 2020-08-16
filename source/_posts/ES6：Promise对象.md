---
title: ES6：Promise对象
date: 2020-03-21 13:22:36
tags: ECMAScript 6 入门
---



# Promise 对象

## Promise 的含义

* 异步编程的一种解决方案，比传统的解决方案更合理和更强大
	* 传统的解决方案：回调函数和事件
* Promise 简单说就是一个容器，保存着某个未来才会结束的事件（通常是一个异步操作）的结果
* 从语法上说，Promise 是一个对象，可以获取异步操作的消息
* 特点：
	* 对象状态不受外界影响
		* Promise 对象代表一个异步操作，有三种状态
			* pending 进行中
			* fulfilled 已成功
			* rejected 已失败
		* 只有异步操作的结果才能改变状态
	* 一旦状态改变，就不会再改变，任何时候都可以得到这个结果
		* 状态改变只有两种可能
			* pending -> fulfilled
			* pending -> rejected
* 缺点：
	* 无法取消。一旦新建就会立即执行，无法中途取消
	* 如果不设置回调函数，内部抛出的错误无法反应到外部
	* 处于 pending 状态的时候，无法得知进度
	* 如果事件不断地反复发生， Stream 模式是比 Promise 更好的选择

## 基本用法

1. 创建 Promise 实例

```js
const promise = new Promise(function(resolve, reject) {
  // ...
  if (/* 成功 */) {
      resolve(value);	// 将 Promise 对象的状态 pending -> fulfilled
  } else {
  		reject(error);	// 将 Promise 对象的状态 pending -> rejected
  }
});
```

2. 回调函数

```js
promise.then(function(value){
  // success
}, function(error){
  // failure
});
```

* 调用 `resolve` 或 `reject` 并不会终结 Promise 的参数函数的执行
* 立即 resolved 的 Promise 是在本轮事件循环的末尾执行，总是晚于本轮循环的同步任务

```js
new Promise((resolve, reject) => {
  resolve(1);
  console.log(2);
}).then(r => {
  console.log(r);
});
// 2
// 1
```

* 一般来说，`resolve` 或 `reject` 之后 Promise 的使命就完成了，后续操作应该放到 `then` 里
	* 所以可以 `return resolve()` 或 `return reject()` 

## Promise.prototype.then()

* `then` 方法的返回值是一个**新的** Promise 实例，因此可以采用链式写法

```js
getJSON("/post/1.json").then(function(post){
  return getJSON(post.commentURL);
}).then(function(comments){
  console.log(...)
}, function(err){
   console.log(...)
});
```

* 箭头函数写法

```js
getJSON("/post/1.json").then(
  post => getJSON(post.commentURL);
).then(
	comments => console.log(...),
  err => console.log(...)
);
```

## Promise.prototype.catch()

* `Promise.prototype.catch()` 是 `.then(null, rejection)` 或 `.then(undefined, rejection)` 的别名，用于指定发生错误的回调函数
	* 一般来说，不要用 `then` 的第二个参数，直接用 `catch`，可以捕获第一个参数的执行错误

```js
getJSON('/posts.json').then(function(post){
  // ...
}).catch (function(error) {
  // 处理 getJOSN 和 前一个回调函数运行时发生的错误
});
```

* `reject` 方法的作用等于抛出错误
	* 如果 Promise 状态已经变成 `resulved` 再抛出错误是无效的
* Promise 对象的错误具有"冒泡"性质，会一直向后传递，知道被捕获为止
	* 也就是说，错误总是会被下一个 `catch` 语句捕获
* 如果没有使用 `carch` 方法指定错误处理的回调函数，Promise 对象抛出的错误不会传递到外层代码
* Node 有一个 `unhandledRejection` 事件（计划废除），专门监听未捕获的 `reject` 错误
* `catch` 返回值也是 Promise 对象，还可以接 `then` ，也可以抛出错误被下一个 `catch` 捕获

## Promise.prototype.finally()

* 无论 Promise 最后状态如何，都会执行的操作，不接收参数
* 本质上是 `then` 方法的特例（`then` 中两种情况代码一样）

```js
Promise.prototype.finally = function (callback) {
  let P = this.constructor;
  return this.then(
  	value => P.resolve(callback()).then(() => value),
    reason => P.resolve(callback()).then(() => { throw reason })
  );
};
```

* `finally` 方法总是返回原来的值

## Promise.prototype.all()

* 将多个 Promise 实例包装成一个新的 Promise 实例
	* 参数可以不是数组，但必须具有 Iterator 接口，且每个成员都是 Promise 实例

```js
const p = Promise.all([p1, p2, p3]);
```

* p 的状态由 p1, p2, p3 决定
	* 都为 `fulfilled`，p 为 `fulfilled`
		* 返回值组成数组，传递给 p 的回调函数
	* 任一 `rejected`，p 为 `rejected`
		* 第一个被 `reject` 的实例的返回值，传递给 p 的回调函数
		* 如果 p1, p2, p3 自己定义了 `catch`，则会执行 `catch`，状态变成 `catch` 返回的 `resolve`
		* 如果没定义 `catch` 才会调用 `Promise.all()` 的 `catch` 方法

## Promise.prototype.race()

* 将多个 Promise 实例包装成一个新的 Promise 实例

```js
const p = Promise.race([p1, p2, p3]);
```

* 只要 p1, p2, p3 任一改变状态，p 就跟着改变
	* 率先改变的 Promise 实例的返回值，传递给 p 的回调函数

## Promise.prototype.allSettled()

* 接受一组 Promise 实例作为参数，包装成一个新的 Promise 实例
* 所有参数实例都返回结果，包装实例才会结束
* 返回新的 Promise 实例，状态总是 `fulfilled`
* 监听函数接收到的参数是对象数组，每个对象分别对应 `allSettled` 的参数
	* 对象属性 status, value, reason

## Promise.prototype.any()

* 接受一组 Promise 实例作为参数，包装成一个新的 Promise 实例
	* 任一 `fulfilled` ，包装实例就 `fulfilled`
	* 全部 `rejectd` ，包装实例才 `rejectd`

## Promise.prototype.resolve()

* 将现有对象转为 Promise 对象

```js
Promise.resolve('foo')
// 等价于
new Promise(resolve => resolve('foo'))
```

* 参数分 4 种情况
	* Promise 实例
		* 不做任何修改
	* `thenable` 对象（具有 `then` 方法的对象）
		* 转为 Promise 对象，立即执行 `then`
	* 不是 `thenable` 对象（其它对象或不是对象）
		* 返回一个新的 Promise 对象，状态 `resolved`
	* 不带参数
		* 直接返回一个 `resolved` 的Promise 对象
* 立即 `resolve` 的 Promise 对象，在本轮"事件循环"的结束时执行，不是在下一轮"事件循环"开始时

## Promise.prototype.reject()

* 返回一个新的 Promise 实例，状态为 `rejected`
* 参数会原封不动作为 `reject` 理由，和 `resolve` 处理不同

## 应用

* 加载图片
* Generator 函数与 Promise 结合

## Promise.try()

* 同步函数同步执行，异步函数异步执行
* 模拟 `try` 代码块