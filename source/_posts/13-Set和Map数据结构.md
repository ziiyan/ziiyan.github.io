---
title: Set和Map数据结构
date: 2020-03-22 13:34:01
tags: ECMAScript 6 入门
---

# Set 和 Map 数据结构

## Set

### 基本用法

* 类似于数组，但成员的值都是唯一的
	* 不会发生类型转换，所以 5 和 "5" 不同
	* 两个 NaN 是相等的，这和 `===` 不同
	* 两个对象总是不想等
* `Set` 构造函数可以接受一个数组（或具有 iterable 接口的其他数据结构）作为参数

```js
const s = new Set();
[2, 3, 5, 4, 5, 2, 2].forEach(x => s.add(x));
// 等价于
const s = new Set([2, 3, 5, 4, 5, 2, 2]);

for (let i of s) {
  console.log(i);
}
// 2 3 5 4
```

> Array.from 可以将 Set 结构转为数组

### Set 实例的属性和方法

* 属性

	* `Set.prototype.constructor` 构造函数
	* `Set.prototype.size` 返回 `Set` 实例的成员总数

* 方法

	* 操作方法

		* `Set.prototype.add(value)` 添加某个值，返回 Set 结构本身
		* `Set.prototype.delete(value)` 删除某个值，返回布尔表示是否成功
		* `Set.prototype.has(value)` 返回布尔值，表示是否为 `Set` 成员
		* `Set.prototype.clear(value)`清空，没有返回值

	* 遍历方法

		* `Set.prototype.keys()` 返回键名，遍历器对象
		* `Set.prototype.values()` 返回键值，遍历器对象

		> Set 结构没有键名，只有键值（或者说键名键值都是同一个值），所以 keys 方法和 values 方法一样

		* `Set.prototype.entries()` 返回键值对，遍历器对象
		* `Set.prototype.forEach()` 使用回调函数遍历每个成员

## WeakSet

* 类似 Set
* 成员只能是对象
	* 弱引用，垃圾回收机制不考虑 WeakSet 的引用，可能会随时被清空
* 不可遍历

## Map

### 基本用法

* 类似于对象，是键值对的集合
* 对象的键只能是字符串，Map 的键不限制类型
* `Map` 构造函数可接受数组，数组成员表示一个键值对（任何具有 Iterator 接口，且每个成员都是一个双元素的数组的数据结构，例如 Set 和 Map）

```js
const map = new Map([
  ['name', 'zhangsan'],
  ['title', 'Author']
])
// 实际上执行
const items = [
  ['name', 'zhangsan'],
  ['title', 'Author']
]
const map = new Map();
items.forEach(
	([key, value]) => map.set(key, value)
)
```

* 多次赋值一个键，会覆盖
* 读取一个未知的键，返回 `undefined`
* 对于对象，地址相同 Map 结构才视为同一个键
* 对于简单类型，值严格相等 `===` 就是一个键

```js
const map = new Map();
map.set(['a'], 555);
map.get(['a']) //	undefined
// ['a'] 是实例，这里两个实例 ['a'] 内存地址不同
```

### 实例的属性和操作方法

* 属性
	* `size`
* 方法
	* 操作方法
		* `Map.prototype.set(key, value)`
			* 返回当前 `Map` 对象，可以采用链式写法
		* `Map.prototype.get(key)`
		* `Map.prototype.has(key)`
		* `Map.prototype.delete(key)`
		* `Map.prototype.clear(key)`
	* 遍历方法
		* `Map.prototype.keys()`
		* `Map.prototype.values()`
		* `Map.prototype.entries()`
		* `Map.prototype.forEach()`
* Map 本身没有 `map` 和 `filter` 方法，需要结合数组

### 与其他数据结构的互相转换

* Map -> 数组
	* `[...map]`
* 数组 -> Map
	* `new Map(array)`
* Map -> 对象
* 对象 -> Map

* Map -> JSON
* JSON -> Map

## WeakMap

* 类似 Map
* 键名只能对象（除了 null）
* 指向的对象不计入垃圾回收机制