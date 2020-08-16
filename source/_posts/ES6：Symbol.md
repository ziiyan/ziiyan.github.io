---
title: ES6：Symbol
date: 2020-03-18 21:15:15
tags: ECMAScript 6 入门
---



# Symbol

## 概述

* ES5 的对象属性名都是字符串，容易造成属性名冲突
* 原始数据类型 `Symbol` 表示独一无二的值
* 通过 `Symbol` 函数生成
	* 不能使用 `new` 命令
	* 可以接受参数，作为变量的描述，用来区分
		* 如果参数是对象，先调用该对象的 `toString` 方法

```js
let s = Symbol();
typeof s	// "symbol"
```

* 现在，对象的属性名可以有两种类型
	* 字符串
	* Symbol 类型

* Symbol 值不能与其他类型的值进行运算，会报错
* 可以转为字符串和布尔值(true)，但不能转为数值

## Symbol.prototype.description

* 创建 Symbol 的时候，可以添加一个描述
* 读取描述需要将 Symbol 显式转为字符串
* ES2019 提供  `description ` 属性，返回 Symbol 描述

## 作为属性名的 Symbol

```js
let mySymbol = Symbol();

// 第一种写法
let a = {};
a[mySymbol] = 'Hello!';

// 第二种写法
let a = {
  [mySymbol]: 'Hello!'
};

// 第三种写法
let a = {};
Object.defineProperty(a, mySymbol, { value: 'Hello!' });

// 以上写法都得到同样结果
a[mySymbol] // "Hello!"
```

* Symbol 值作为对象属性名时，不能用点运算符，因为点运算符后面总是字符串
	* **要放在方括号中定义**

```js
const mySymbol = Symbol();
const a = {};
a.mySymbol = 'Hello';
a[mySymbol]	// undefined
a['mySymbol'] // 'Hello'
```

* Symbol 值作为属性名时，该属性是公开属性，不是私有属性

## 实例：消除魔术字符串

> 魔术字符串指的是，在代码之中多次出现、与代码形成强耦合的某一个具体的字符串或者数值。风格良好的代码，应该尽量消除魔术字符串，改由含义清晰的变量代替。

* 等于哪个值并不重要，只要确保不会跟其他属性的值冲突即可，就很适合改用 Symbol 值

## 属性名的遍历

* Symbol 作为属性名，遍历对象的时候不会出现在`for...in`、`for...of`循环中
	* 可以利用这个特性，为对象定义非私有、只希望内部使用的方法
* 不会被`Object.keys()`、`Object.getOwnPropertyNames()`、`JSON.stringify()` 返回

* `Object.getOwnPropertySymbols()`方法，可以获取指定对象的所有 Symbol 属性名
	* 该方法返回一个数组，成员是当前对象的所有用作属性名的 Symbol 值
* `Reflect.ownKeys(object)`方法可以返回所有类型的键名，包括常规键名和 Symbol 键名

## Symbol.for()，Symbol.keyFor()

* `Symbol.for('string')` 检查是否有以 `string` 作为名称的 Symbol 值，有则返回，没有则新建，并注册到全局
* `Symbol()`写法没有登记机制
* `Symbol.keyFor(变量名)`方法返回一个已登记的 Symbol 类型值的`key`

## 实例：模块的 Singleton 模式

* Singleton 模式（单例模式）指的是调用一个类，任何时候返回到都是同一个实例

## 内置的 Symbol 值

* Symbol.hasInstance
	* 指向一个内部方法
	* 当其他对象使用`instanceof`运算符，会调用这个方法
	* 比如，`foo instanceof Foo`在语言内部，实际调用的是`Foo[Symbol.hasInstance](foo)`
* Symbol.isConcatSpreadable
	* 等于一个布尔值
	* 表示该对象用于`Array.prototype.concat()`时，是否可以展开
* Symbol.species
	* 指向一个构造函数，创建衍生对象时使用该属性
* Symbol.match
	* 指向一个函数
	* 如果该属性存在，调用它并返回方法的返回值
* Symbol.replace
	* 指向一个方法
	* 当该对象被`String.prototype.replace`方法调用时，会返回该方法的返回值
* Symbol.search
	* 指向一个方法
	* 当该对象被`String.prototype.search`方法调用时，会返回该方法的返回值
* Symbol.split
	* 指向一个方法
	* 当该对象被`String.prototype.split`方法调用时，会返回该方法的返回值
* Symbol.iterator
* Symbol.toPrimitive
* Symbol.toStringTag
* SYmbol.unscopables





















