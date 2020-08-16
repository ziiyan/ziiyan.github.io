---
title: ES6：变量的解构赋值
date: 2020-03-15 21:28:01
tags: ECMAScript 6 入门
---


# 变量的解构赋值

* ES6 允许按照一定的模式，从数组和对象中提取值，对变量进行赋值，被称为解构

## 数组的解构赋值

### 基本用法

```js
let [a, b, c] = [1, 2, 3];
```

* 本质上，这种写法属于“模式匹配”，只要等号两边的模式相同，左边的变量就会被赋予对应的值

```js
// 嵌套的例子
let [head, ...detail] = [1, 2, 3, 4];
// head = 1, detail = [2, 3, 4]
let [x, y, ...z] = ['a'];
// x = 'a', y = undefined, z = []
```

* 如果解构不成功，变量的值就等于 `undefined`

```js
let [foo] = [];
let [bar, foo] = [1];
```

* 不完全解构，只匹配一部分

```js
let [x, y] = [1, 2, 3];
// x = 1, y = 2
let [a, [b], d] = [1, [2, 3], 4];
// a = 1, b = 2, d = 4
```

* 如果等号右边不是数组（严格的说，不是可遍历的结构，参见第 17 章 - Iterator），会报错

```js
// 转为对象后不具备 Iterator 接口
let [foo] = 1;
let [foo] = false;
let [foo] = NaN;
let [foo] = undefined;
let [foo] = null;
// 本身不具备 Iterator 接口
let [foo] = {};
```

* 只要某种数据结构具有 Iterator 接口，都可以采用数组形式的解构赋值

### 默认值

* 解构赋值允许指定默认值

```js
let [foo = true] = [];
// foo = true
let [x, y = 'b'] = ['a'];
// x = 'a', y = 'b'
```

* ES6 内部使用严格相等运算符判断一个位置是否有值，只有当一个数组成员严格等于 `undefined` 默认值才会生效

```js
let [x = 1] = [undefined];
// x = 1
let [x = 1] = [null];
// x = null
```

* 如果默认值是一个表达式，则是惰性求值，也就是只有在用到的时候才会求值。

```js
function f() {
  console.log('aaa');
}
let [x = f()] = [1];
// x = 1, 函数 f 不会执行
// 等价于
let x;
if ([1][0] === undefined) {
  x = f();
} else {
  x = [1][0];
}
// [1] 是只含元素 1 的数组
// [1][0] 表示取该数组第一位，也就是元素 1
// let array = [1];
// let x = array[0];
```

* 默认值可以引用解构赋值的其他变量，但该变量必须已经声明

```js
let [x = 1, y = x] = [];
// x = 1, y = 1
let [x = y, y = 1] = [];
// 报错
```

## 对象的解构赋值

### 基本用法

```js
let { foo, bar } = { foo: 'aaa', bar: 'bbb' };
// foo = 'aaa', bar = 'bbb'
```

* 数组的元素是按次序排列的，变量的取值由位置决定
* 对象的属性没有次序，变量必须与属性同名才能赋值

```js
let { baz } = { foo: 'aaa', bar: 'bbb' };
// baz = undefined
```

* 对象的解构赋值可以很方便的将现有对象的方法赋值到某个变量

```js
let { log, sin, cos } = Math;
const { log } = console;
log('hello');
```

* 如果变量名与属性名不一致，写法如下

```js
let { foo: baz } = { foo: 'aaa', bar: 'bbb'};
// baz = 'aaa'

let obj = { first: 'hello', last = 'world' };
let { first: f, last: l } = obj
// f = 'hello', l = 'world'
```

> 说明对象的解构赋值是下面形式的简写（参见第 10 章 - 对象的扩展）
>
> ```js
> let { foo: foo, bar: bar } = { foo: 'aaa', bar: 'bbb' };
> ```
>
> 也就是说，对象的解构赋值的内部机制，是先找到同名属性，然后再赋值给对应变量
>
> 真正被赋值的是后者，而不是前者
>
> 前者是匹配的模式，后者才是变量

* 嵌套

```js
const node = {
  loc: {
    start: {
      line: 1,
      column: 5
    }
  }
};

let { loc, loc: { start }, loc: { start: { line }} } = node;
// line = 1
// start = Object {line: 1, column: 5}
// loc = Object { start: Object }
```

* 对象的解构赋值可以取到继承的属性

```js
const obj1 = {};
const obj2 = { foo: 'bar' };
Object.setPrototypeOf(obj1, obj2);

const { foo } = obj1;
// foo = 'bar'
```

### 默认值

* 生效条件是对象的属性值严格等于 `undefined`

### 注意点

* 如果要将一个已经声明的变量用于解构赋值

```js
// 错误的写法
let x;
{x} = {x: 1};
// js 引擎会将 {x} 理解为一个代码块，从而发生语法错误

// 正确的写法
let x;
({x} = {x: 1});
// 放在圆括号里即可正确执行
```

* 解构赋值允许等号左边的模式中不放任何变量名

```js
({} = [true, false]);
({} = 'abc');
({} = []);
```

* 由于数组本质是特殊的对象，因此可以对数组进行对象属性的解构

```js
let arr = [1, 2, 3];
let {0: first, [arr.length = 1]: last} = arr;
// first = 1, last = 3
```

## 字符串的解构赋值

* 字符串在解构赋值时被转换成一个类似数组的对象

```js
const [a, b, c, d, e] = 'hello';
// a = 'h', b = 'e', C = 'c', d = 'l', e = 'o'

let { length: len } = 'hello';
// len = 5
```

## 数值和布尔值的解构赋值

* 数值和布尔值在解构赋值时被转为对象

```js
let { toString: s } = 123;
// s === Number.prototype.toString => true

let { toString: s } = true;
// s === Boolean.prototype.toString => true
```

* 数组和布尔值的包装对象都有 `toString` 属性，因此变量 `s` 都能取到值
* 解构赋值的规则：只要等号右边的值不是对象或数组，就先将其转为对象
* 由于 `undefined` 和 `null` 无法转为对象，所以它们无法进行解构赋值，会报错

##函数参数的解构赋值

```js
function add([x, y]) {
  return x + y;
}
add([1, 2]);
// 3

[[1, 2], [3, 4]].map(([a, b]) => a + b);
// [3, 7]

function move({x = 0, y = 0} = {}) {
  return [x, y];
}
move({x: 3, y: 8});
// [3, 8]
move({});
// [0, 0]
move();
// [0, 0]

function move({x, y} = { x: 0, y: 0 }) {
  return [x, y];
}
move({x: 3, y: 8}); // [3, 8]
move({}); // [undefined, undefined]
move(); // [0, 0]
```

## 圆括号问题

* 对于编译器来说，一个式子到底是模式还是表达式，只有在解析到（或解析不到）等号才能知道
* 如果模式中出现圆括号怎么处理？
	* 只要有可能导致解构的歧义，就不得使用圆括号
	* 尽量不要在模式中放置圆括号

### 不能使用圆括号的情况

* 变量声明语句
* 函数参数
* 赋值语句的模式

### 可以使用圆括号的情况

* 赋值语句的非模式部分

## 用途

* 交换变量的值

```js
let x = 1;
let y = 2;

[x, y] = [y, x];
```

* 从函数返回多个值

* 函数参数的定义
* 提取 JSON 数据
* 函数参数的默认值
* 遍历 Map 解构

```js
con s t map = new Map();
map.set('first', 'hello');
map.set('second', 'world');

for (let [key, value] of map) {
  console.log(key + ' is ' + value);
}

for (let [key] of map) {
  // ...
}

for (let [, value] of map) {
  // ...
}
```

* 输入模块的指定方法

```js
const { SourceMapCounsumer, SourceNode } = require("source-map");
```