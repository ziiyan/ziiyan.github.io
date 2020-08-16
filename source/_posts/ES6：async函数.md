---
title: ES6：async函数
date: 2020-03-24 21:15:56
tags: ECMAScript 6 入门
---

# async 函数

## 含义

* async 使异步操作变得更加方便，Generator 的语法糖
* 将 Generator 函数的星号（*）替换成 async，将 yield 替换成 await

* async 对 Generator 函数的改进：
	* 内置执行器，会自动执行
		* Generator 需要 co 模块才能自动执行
		* 或者调用 next 手动执行
	* 更好的语义
	* 更广的适用性
		* await 后面可以是 Promise 对象和原始类型的值（数值、字符串和布尔值，会自动转成立即 resolved 的 Promise 对象）
	* 返回值是 Promise
* async 可以看作多个异步操作，包装成的一个 Promise 对象，而 await 命令就是内部 then 命令的语法糖

## 基本用法

* async 函数返回一个 Promise 对象，可以使用 `then` 方法添加回调函数
* 当函数执行的时候，一旦遇到 `await` 就会先返回，等到异步操作完成，再接着执行函数体内后面的语句

## 语法

* 返回 Promise 对象

	* `async` 函数内部 `return` 语句返回值，会成为 `then` 方法回调函数的参数
	* `async` 函数内部抛出错误，会导致返回的 Promise 对象变为 `reject` 状态

* Promise 对象的状态变化

	* `async` 函数返回的 Promise 对象，必须等到内部所有 `await` 命令后面的 Promise 对象执行完，才会发生状态改变
	* 除非 `return` 或抛出错误
	* 也就是说，只有 `async` 函数内部的异步操作执行完，才会执行 `then` 方法指定的回调函数

* await 命令

	* 如果 await 后面是一个 `thenable` 对象（定义了 `then` 方法的对象），会将其同等于 Promise 对象
	* 任何一个 `await` 语句后面的 Promise 对象变为 `reject` 状态，整个 `async` 函数都会中断执行
		* 可以在 `async`  内部进行错误处理，catch 错误就不会影响后续执行

* 注意点

	* 最好把 `await` 命令放在 `try...catch` 代码块中

	* 多个 `await` 命令后面的异步操作，如果不存在继发关系，最好同时触发，比较不耗时

		* ```js
			let [foo, bar] = await Promise.all([getFoo(), getBar()]);
			// 或
			let fooPromise = getFoo();
			let barPromise = getBar();
			let foo = await fooPromise;
			let bar = await barPromise;
			```

	* `await` 只用在 `async` 函数之中

	* `async` 可以保留运行堆栈

		* 如果内层函数异步出错，外层已经执行完，错误栈不包括外层
		* 用到 `async` 的话，会保存上下文环境

## async 函数的实现原理

* 将 Generator 函数和自动执行器包装在一个函数里

```js
async function fn(args) {
  // ...
}
// 等同于
function fn(args) {
  return spawn(function* () {
    // ...
  });
}
// spawn 就是自动执行器
function spawn(genF) {
  return new Promise(function(resolve, reject) {
    const gen = genF();
    function step(nextF) {
      let next;
      try {
        next = nextF();
      } catch(e) {
        return reject(e);
      }
      if (next.done) {
        return resolve(next.value);
      }
      Promise.resolve(next.value).then(function(v) {
        step(function() {
          return gen.nect(v);
        });
      }, function(e) {
        step(function() {
          return gen.throw(e);
        });
      });
    }
    step(function() {
      return gen.next(undefined);
    });
  });
}
```

## 顶层 await

* 语法提案
* 借用 `await` 解决模块异步加载的问题