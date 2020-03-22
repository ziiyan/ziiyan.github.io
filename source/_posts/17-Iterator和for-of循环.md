---
title: Iterator和for...of循环
date: 2020-03-22 14:38:06
tags: ECMAScript 6 入门
---

# Iterator 和 for...of 循环

## Iterator（遍历器）的概念

* JS 原来表示"集合"的是数组 `Array` 和对象 `Object`，ES6 添加了 `Map` 和 `Set`

* Iterator 是一种接口，任何数据结构只要部署 Iterator 接口，就可以完成便利操作（即依次处理该数据结构的所有成员）

* Iterator 作用：

	* 为各种数据结构提供统一的、简便的访问接口
	* 使数据结构的成员能够按某种次序排列
	* ES6 创造了新的遍历命令 `for...of` 

* 遍历过程

	* 创建指针对象，指向当前数据结构的起始位置

		* 遍历对象本质上是一个指针对象

		* > js 有指针的概念？

	* 第一次调用指针对象的 `next` 方法，可以将指针指向数据结构的第一个成员

	* 不断调用指针对象的 `next` 方法，直到它指向数据结构的结束位置

* 每次调用 `next` 返回一个包含 `value` he `done` 两个属性的对象。

	* `value` 属性是当前成员的值
	* `done` 属性是一个布尔值，表示遍历是否结束

```js
var it = makeIterator(['a', 'b']);

it.next();
it.next();
it.next();

function makeIterator(array) {
  var nextIndex = 0;
  return {
    next: function() {
      return nextIndex < array.length ?
        	{value: array[nextIndex++], done: false} : {value: undefined, done: true}
    }
  }
}
```

## 默认 Iterator 接口

* 当使用 `for...of` 循环遍历某种数据结构时，该循环会自动去寻找 Iterator 接口
* ES6 规定，默认的 Iterator 接口部署在数据结构的 `Symbol.iterator` 属性，或者说，一个数据结构只要有 `Symbol.iterator` 属性，就认为是可遍历的
	* `Symbol.iterator` 属性本身是一个函数，就是当前数据结构默认的遍历器生产函数
	* 执行这个函数，就会返回一个遍历器
	* 属性名 `Symbol.iterator` 是一个表达式，返回 `Symbol` 对象的 `iterator` 属性

```js
const obj = {
  [Symbol.iterator]: function () {
    return {
      next: function() {
        return {
          value: 1,
          done: true
        };
      }
    };
  }
};
```

* 原生具备 Iterator 接口的数据结构：
	* Array
	* Map
	* Set
	* String
	* TypedArray
	* 函数的 arguments 对象
	* NodeList 对象

> 对象（Object）之所以没有默认部署 Iterator 接口，是因为对象的哪个属性先遍历，哪个属性后遍历是不确定的，需要开发者手动指定。本质上，遍历器是一种线性处理，对于任何非线性的数据结构，部署遍历器接口，就等于部署一种线性转换。不过，严格地说，对象部署遍历器接口并不是很必要，因为这时对象实际上被当作 Map 结构使用，ES5 没有 Map 结构，而 ES6 原生提供了。

## 调用 Iterator 接口的场合

* 数组和 Set 结构进行解构赋值时，默认调用 `Symbol.iterator` 方法
* 扩展运算符 `...`
	* 部署了 Iterator 接口的数据结构都能用扩展运算符
* yield* 后面跟的是一个可遍历的解构，它会调用该结构的遍历器接口
* 数组的遍历会调用遍历器接口，所以任何接受数组作为参数的场合，都调用了遍历器
	* for ... of
	* Array.from()
	* Map(), Set(), WeakMap(), WeakSet()
	* Promise.all()
	* Promise.race()

## Iterator 接口与 Generator 函数

* `Symbol.iterator` 方法的最简单实现

```js
let myIterable = {
  [Symbol.iterator]: function* () {
    yield 1;
    yield 2;
    yield 3;
  }
}
[...myIterable] // [1, 2, 3]

// 或

let obj = {
  * [Symbol.iterator]() {
    yield 'hello';
    yield 'world';
  }
};

for (let x of obj) {
  console.log(x);
}
// "hello"
// "world"
```

## for...of 循环

* 数据结构只要部署了`Symbol.iterator`属性，就被视为具有 iterator 接口，就可以用`for...of`循环遍历它的成员
* 也就是说，`for...of`循环内部调用的是数据结构的`Symbol.iterator`方法
* `for...of`循环可以使用的范围
	* 数组
	* Set 
	* Map 
	* 某些类似数组的对象
		* `arguments`对象
		* DOM NodeList 对象
	* Generator 对象
	* 字符串

