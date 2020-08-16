---
title: ES6：Generator函数的语法
date: 2020-03-21 17:18:23
tags: ECMAScript 6 入门
---

# Generator 函数的语法

## 简介

### 基本概念

* 异步编程解决方案，语法行为与传统函数完全不同
* 理解
	* 语法上，Generator 函数是一个状态机，封装了多个内部状态
		* 执行 Generator 函数会返回一个遍历器对象
			* 遍历器对象可以依次遍历 Generator 函数内部的每一个状态
	* 形式上，Generator 函数是一个普通函数
		* 两个特征
			* `function` 关键字与函数名之间有一个星号，星号位置和空格位置的先后关系没有规定
			* 函数体内部使用 `yield` 表达式，定义不同的内部状态

```js
function* helloWorldGenerator() {
  yield 'hello';
  yield 'world';
  return 'ending';
}
// 三个状态

var hw = helloWorldGenerator();
// 调用
```

* 调用 Generator 函数后，该函数并不执行，返回一个指向内部状态的指针对象（iterator Object）
* 调用遍历器对象的 `next` 方法，使得指针移向下一个状态
	* 也就是说，每次调用 `next` 方法，内部指针就从函数头部或上一次停下来的地方开始执行，直到遇到下一个 `yield` 表达式（或 `return` 语句）
	* 换言之，Generator 函数是分段执行的，`yield` 表达式是暂停执行的标记，`next` 方法恢复执行
* `next` 返回一个对象
	* 属性 `value` 是当前 `yield` 表达式的值
	* 属性 `done` 表示遍历是否结束

```js
hw.next()
// { value: 'hello', done: false }
hw.next()
// { value: 'world', done: false }
hw.next()
// { value: 'ending', done: true }
hw.next()
// { value: undefined, done: true }
```

### yield 表达式

* `next` 方法的运行逻辑
	* 遇到 `yield` 表达式，就暂停执行，并将紧跟在 `yield` 后面的表达式的值作为对象的 `value` 属性返回
	* 下次调用 `yield` 时，再继续往下执行，直到遇到下一个 `yield` 表达式
	* 如果没有遇到新的表达式，就一直运行到 `return`，并将 `return` 语句后面的表达式的值作为对象的 `value` 属性
	* 如果没有`return` 语句，则返回对象的 `value` 属性值为 `undefined`
* `yield` 表达式后面的表达式，只有调用 `next` 方法、内部指针指向该语句时才会执行
	* 给 JavaScript 提供了手动**惰性求值**的语法功能
* 不用 `yield` 的情况下，Generator 就是一个单纯的暂缓执行函数
* `yield` 只能用在 Generator 函数里面，其它地方会报错
* `yield` 如果用在另一个表达式中，必须放在括号里面
* `yield` 用做函数参数或放在赋值表达式右边，不用括号

## next 方法的参数

> 这节没看懂

* `yield` 表达式本身没有返回值，或者说总是返回 `undefined`
* `next` 方法可以带一个参数，该参数会被当作上一个 `yield` 表达式的返回值

## for...of 循环

* `for...of` 循环自动遍历 Generator 函数运行时生成的 `Iterator` 对象，此时不需要调用 `next` 方法
* 斐波那契

```js
function* fibonacci() {
  let [prev, curr] = [0, 1];
  for (;;) {
    yield curr;
    [prev, curr] = [curr, prev + curr];
  }
}

for (let n of fibonacci()) {
  if (n > 1000) break;
  console.log(n);
}
```

## Generator.prototype.throw()

* `throw` 方法可以在函数体外抛出错误，在函数体内捕获
	* 前提是必须进行过一次 `next` 方法
	* 捕获后会自动执行一次 `next` 方法，不影响下次遍历
* 如果 Generator 函数内部没有部署 `try...catch` 代码块，那么错误会被外部的 `try...catch` 代码块捕获
* 如果都没有，那么程序将报错，直接中断执行

## Generator.prototype.return()

* 返回给定的值，并终结遍历 Generator 函数

## next()、throw()、return() 的共同点

* 作用都是让 Generator 函数恢复执行，使用不同的语句替换 `yield` 表达式
* `next()` 是将 `yield` 表达式替换成一个值
* `throw()` 是将 `yield` 表达式替换成一个 `throw` 语句
* `return()` 是将 `yield` 表达式替换成一个 `return` 语句

## yield* 表达式

* 在一个 Generator 函数里面执行另一个 Generator 函数

```js
function* foo() {
  yield 'a';
  yield 'b';
}

function* bar() {
  yield 'x';
  yield* foo();
  yield 'y';
}

// 等同于
function* bar() {
  yield 'x';
  yield 'a';
  yield 'b';
  yield 'y';
}

// 等同于
function* bar() {
  yield 'x';
  for (let v of foo()) {
    yield v;
  }
  yield 'y';
}

for (let v of bar()) {
  console.log(v);
}
// "x"
// "a"
// "b"
// "y"
```

* 从语法角度看，如果`yield`表达式后面跟的是一个遍历器对象，需要在`yield`表达式后面加上星号，就会执行这个遍历器
* 任何数据结构只要有 Iterator 接口，就可以被`yield*`遍历

## 作为对象属性的 Generator 函数

```js
let obj = {
  * myGeneratorMethod() {
    ···
  }
};
// 等价于
let obj = {
  myGeneratorMethod: function* () {
    // ···
  }
};
```