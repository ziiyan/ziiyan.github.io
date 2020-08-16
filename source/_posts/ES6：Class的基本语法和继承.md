---
title: ES6：Class的基本语法和继承
date: 2020-03-24 21:43:45
tags: ECMAScript 6 入门
---

# Class 的基本语法

## 简介

### 类的由来

* js 中生成实例对象的传统方法是通过构造函数

```js
function Point(x, y) {
  this.x = x;
  this.y = y;
}
Point.prototype.toString = function () {
  return '(' + this.x + ',' + this.y + ')';
};
var p = new Point(1, 2);
```

* Es6 为了接近传统语言，引入了 Class，作为对象的模版。
* 通过 `class` 关键字，可以定义类
* Es6 的 `clss` 看作一个语法糖，大部分功能 es5 都可以做到，只是让写法更像面向对象编程的语法

```js
class Point {
  constructor(x, y) {
    this.x = x;
    this.y = y;
  }
  
  toString() {
    return '(' + this.x + ',' + this.y + ')';
  }
}
```

* ES5 的构造函数 `Point`，对应 ES6 的 `Point` 类的构造方法 `constructor`

* 类的数据类型就是函数，类本身就指向构造函数

```js
class Point {
  // ...
}
typeof Point // "function"
Point === Point.prototype.constructor // true
```

* 使用的时候，直接用 `new` 命令，跟构造函数一样
* 类的所有方法都定义在类的`prototype`属性上面

```js
class Point {
  constructor() {
    // ...
  }
  toString() {
    // ...
  }
  toValue() {
    // ...
  }
}

// 等同于
Point.prototype = {
  constructor() {},
  toString() {},
  toValue() {},
};
```

* 在类的实例上调用方法，就是调用原型上的方法

```js
class B {}
let b = new B();

b.constructor === B.prototype.constructor // true
```

* 类的方法都是定义在 `prototype` 对象上面所以类的新方法可以添加在`prototype`对象上面。`Object.assign`方法可以很方便地一次向类添加多个方法

```js
class Point {
  constructor() {
    // ...
  }
}

Object.assign(Point.prototype, {
  toString() {},
  toValue() {}
});
```

* `prototype`对象的`constructor`属性，直接指向“类”的本身，这与 ES5 的行为是一致的

```js
Point.prototype.constructor === Point
```

* 类的内部所有定义的方法，都是不可枚举的（non-enumerable），这与 ES5 的行为不一致

```js
class Point {
  constructor(x, y) {
    // ...
  }

  toString() {
    // ...
  }
}

Object.keys(Point.prototype)
// []
Object.getOwnPropertyNames(Point.prototype)
// ["constructor","toString"]

// ES5 的写法可枚举
var Point = function (x, y) {
  // ...
};

Point.prototype.toString = function() {
  // ...
};

Object.keys(Point.prototype)
// ["toString"]
Object.getOwnPropertyNames(Point.prototype)
// ["constructor","toString"]
```

### constructor 方法

* `constructor` 方法都是类的默认方法，通过 `new` 命令生成对象实例时，自动调用该方法
* 一个类必须有 `constructor` 方法，如果没有显示定义，一个空的 `constructor` 方法会被默认添加
* `constructor` 方法默认返回实例对象（即 `this`），可以手动指定返回另外一个对象

```js
class Foo {
  constructor() {
    return Object.create(null);
  }
}

new Foo() instanceof Foo
// false
```

* 类必须使用`new`调用，否则会报错。这是它跟普通构造函数的一个主要区别，后者不用`new`也可以执行

### 类的实例

* 必须用 `new` 生成
* 与 ES5 一样，实例的属性除非显式定义在其本身（即定义在`this`对象上），否则都是定义在原型上（即定义在`class`上）

```js
//定义类
class Point {

  constructor(x, y) {
    this.x = x;
    this.y = y;
  }

  toString() {
    return '(' + this.x + ', ' + this.y + ')';
  }

}

var point = new Point(2, 3);

point.toString() // (2, 3)

point.hasOwnProperty('x') // true
point.hasOwnProperty('y') // true
point.hasOwnProperty('toString') // false
point.__proto__.hasOwnProperty('toString') // true
```

* 类的所有实例共享一个原型对象，可以通过实例的`__proto__`属性为“类”添加方法

> `__proto__` 并不是语言本身的特性，这是各大厂商具体实现时添加的私有属性，虽然目前很多现代浏览器的 JS 引擎中都提供了这个私有属性，但依旧不建议在生产中使用该属性，避免对环境产生依赖。生产环境中，我们可以使用 `Object.getPrototypeOf` 方法来获取实例对象的原型，然后再来为原型添加方法/属性。

### getter 和 setter

* 与 ES5 一样，在“类”的内部可以使用`get`和`set`关键字，对某个属性设置存值函数和取值函数，拦截该属性的存取行为

```js
class MyClass {
  constructor() {
    // ...
  }
  get prop() {
    return 'getter';
  }
  set prop(value) {
    console.log('setter: '+value);
  }
}

let inst = new MyClass();

inst.prop = 123;
// setter: 123

inst.prop
// 'getter'
```

* 存值函数和取值函数是定义在属性的描述对象上面，这与 ES5 完全一致

### 属性表达式

```js
let methodName = 'getArea';

class Square {
  constructor(length) {
    // ...
  }

  [methodName]() {
    // ...
  }
}

// Square类的方法名getArea，是从表达式得到的
```

### Class 表达式

* 与函数一样，类也可以使用表达式的形式定义

```js
const MyClass = class Me {
  getClassName() {
    return Me.name;
  }
};
// 类的名字是Me，但是Me只在 Class 的内部可用，指代当前类
// 在 Class 外部，这个类只能用 MyClass 引用
// 内部不用 Me 可以省略
const MyClass = class { /* ... */ };
```

* 立即执行的 Class

```js
let person = new class {
  constructor(name) {
    this.name = name;
  }

  sayName() {
    console.log(this.name);
  }
}('张三');

person.sayName(); // "张三"
```

### 注意点

* 严格模式
	* 类和模块的内部，默认就是严格模式，所以不需要使用`use strict`指定运行模式
* 类不存在变量提升
* name 属性的值是类名
* Generator 方法
	* 如果某个方法之前加上星号（`*`），就表示该方法是一个 Generator 函数
* this
	* 类的方法内部如果含有`this`，它默认指向类的实例

```js
class Logger {
  printName(name = 'there') {
    this.print(`Hello ${name}`);
  }

  print(text) {
    console.log(text);
  }
}

const logger = new Logger();
const { printName } = logger;
printName(); // TypeError: Cannot read property 'print' of undefined
// this会指向该方法运行时所在的环境（由于 class 内部是严格模式，所以 this 实际指向的是undefined），从而导致找不到print方法而报错
```

* 解决办法

```js
// 构造方法中绑定`this`，这样就不会找不到`print`方法了
class Logger {
  constructor() {
    this.printName = this.printName.bind(this);
  }
  // ...
}
```

```js
// 箭头函数
class Obj {
  constructor() {
    this.getThis = () => this;
  }
}

const myObj = new Obj();
myObj.getThis() === myObj // true
```

```js
// 使用Proxy，获取方法的时候，自动绑定this
function selfish (target) {
  const cache = new WeakMap();
  const handler = {
    get (target, key) {
      const value = Reflect.get(target, key);
      if (typeof value !== 'function') {
        return value;
      }
      if (!cache.has(value)) {
        cache.set(value, value.bind(target));
      }
      return cache.get(value);
    }
  };
  const proxy = new Proxy(target, handler);
  return proxy;
}

const logger = selfish(new Logger());
```

## 静态方法

* 类相当于实例的原型，所有在类中定义的方法，都会被实例继承
* 如果在一个方法前加上 `static` 关键字，表示该方法不会被实例继承，而是通过类来调用，称为“静态方法”
* 如果静态方法包含`this`关键字，这个`this`指的是类，而不是实例
* 静态方法可以与非静态方法重名

```js
class Foo {
  static bar() {
    this.baz();
  }
  static baz() {
    console.log('hello');
  }
  baz() {
    console.log('world');
  }
}

Foo.bar() // hello
```

* 父类的静态方法，可以被子类继承

```js
class Foo {
  static classMethod() {
    return 'hello';
  }
}

class Bar extends Foo {
}

Bar.classMethod() // 'hello'
```

* 静态方法也是可以从`super`对象上调用的

```js
class Foo {
  static classMethod() {
    return 'hello';
  }
}

class Bar extends Foo {
  static classMethod() {
    return super.classMethod() + ', too';
  }
}

Bar.classMethod() // "hello, too"
```

## 实例属性和新写法

* 实例属性除了定义在`constructor()`方法里面的`this`上面，也可以定义在类的最顶层

```js
class IncreasingCounter {
  constructor() {
    this._count = 0;
  }
}
// 等同于
class IncreasingCounter {
  _count = 0;
}
```

## 静态属性

* 静态属性指的是 Class 本身的属性，即`Class.propName`，而不是定义在实例对象（`this`）上的属性

```js
// 老写法
class Foo {
  // ...
}
Foo.prop = 1;

// 新写法（提案）
class Foo {
  static prop = 1;
}
```

## 私有方法和私有属性

* 私有方法和私有属性，是只能在类的内部访问的方法和属性，外部不能访问

* 这是常见需求，有利于代码的封装，但 ES6 不提供，只能通过变通方法模拟实现

### 现有的解决方案

* 命名上加以区别
	* 不保险，在类的外部还是可以调用

```js
class Widget {
  // 公有方法
  foo (baz) {
    this._bar(baz);
  }
  // 私有方法
  _bar(baz) {
    return this.snaf = baz;
  }
}
```

* 将私有方法移出模块，因为模块内部的所有方法都是对外可见的

```js
class Widget {
  foo (baz) {
    bar.call(this, baz);
  }
}
function bar(baz) {
  return this.snaf = baz;
}
```

* 利用`Symbol`值的唯一性，将私有方法的名字命名为一个`Symbol`值
	* `Reflect.ownKeys()` 可以拿到

```js
const bar = Symbol('bar');
const snaf = Symbol('snaf');
export default class myClass{
  // 公有方法
  foo(baz) {
    this[bar](baz);
  }
  // 私有方法
  [bar](baz) {
    return this[snaf] = baz;
  }
};
```

* 提案
	* 属性名之前，使用`#`表示

## new.target 属性

* 用在构造函数之中，返回`new`命令作用于的那个构造函数
* 如果构造函数不是通过`new`命令或`Reflect.construct()`调用的，`new.target`会返回`undefined`
* 因此这个属性可以用来确定构造函数是怎么调用的
* 子类继承父类时，`new.target`会返回子类
* 利用这个特点，可以写出不能独立使用、必须继承后才能使用的类
* 在函数外部，使用`new.target`会报错



# Class 的继承

## 简介

* 通过`extends`关键字实现继承
* ES5 通过修改原型链实现继承

```js
class Point {}
class ColorPoint extends Point {
  constructor(x, y, color) {
    super(x, y); // 调用父类的constructor(x, y)
    this.color = color;
  }

  toString() {
    return this.color + ' ' + super.toString(); // 调用父类的toString()
  }
}
```

* `super`关键字，它在这里表示父类的构造函数，用来新建父类的`this`对象

> 子类必须在`constructor`方法中调用`super`方法，否则新建实例时会报错。这是因为子类自己的`this`对象，必须先通过父类的构造函数完成塑造，得到与父类同样的实例属性和方法，然后再对其进行加工，加上子类自己的实例属性和方法。如果不调用`super`方法，子类就得不到`this`对象。

> ES5 的继承，实质是先创造子类的实例对象`this`，然后再将父类的方法添加到`this`上面（`Parent.apply(this)`）。ES6 的继承机制完全不同，实质是先将父类实例对象的属性和方法，加到`this`上面（所以必须先调用`super`方法），然后再用子类的构造函数修改`this`。

## Object.getPrototypeOf()

* `Object.getPrototypeOf`方法可以用来从子类上获取父类
* 可以使用这个方法判断，一个类是否继承了另一个类

```js
Object.getPrototypeOf(ColorPoint) === Point
// true
```

## super 关键字

* `super`作为函数调用时，代表父类的构造函数，只能用在子类的构造函数中
	* ES6 要求，子类的构造函数必须执行一次`super`函数

```js
class A {}

class B extends A {
  constructor() {
    super();
  }
}
// super虽然代表了父类A的构造函数，但是返回的是子类B的实例
// 即super内部的this指的是B的实例
// super()在这里相当于A.prototype.constructor.call(this)。
```

* `super`作为对象时
	* 在普通方法中，指向父类的原型对象
		* 定义在父类实例上的方法或属性无法通过 `super` 调用
		* ES6 规定，在子类普通方法中通过`super`调用父类的方法时，方法内部的`this`指向当前的子类实例
	* 在静态方法中，指向父类
		* 在子类的静态方法中通过`super`调用父类的方法时，方法内部的`this`指向当前的子类，而不是子类的实例
* 使用`super`的时候，必须显式指定是作为函数、还是作为对象使用，否则会报错

## 类的 prototype 属性和 \_\_proto\_\_ 属性

> 大多数浏览器的 ES5 实现之中，每一个对象都有`__proto__`属性，指向对应的构造函数的`prototype`属性。Class 作为构造函数的语法糖，同时有`prototype`属性和`__proto__`属性，因此同时存在两条继承链

* 子类的`__proto__`属性，表示构造函数的继承，总是指向父类
* 子类`prototype`属性的`__proto__`属性，表示方法的继承，总是指向父类的`prototype`属性

> 作为一个对象，子类（`B`）的原型（`__proto__`属性）是父类（`A`）
>
> 作为一个构造函数，子类（`B`）的原型对象（`prototype`属性）是父类的原型对象（`prototype`属性）的实例

### 实例的 \_\_proto\_\_ 属性

* 子类实例的 `__proto__` 属性的`__proto__` 属性，指向父类实例的 `__proto__` 属性。子类的原型，是父类的原型

## 原生构造函数的继承

- Boolean()
- Number()
- String()
- Array()
- Date()
- Function()
- RegExp()
- Error()
- Object()

## Mixin 模式的实现

* Mixin 指的是多个对象合成一个新的对象，新对象具有各个组成成员的接口

```js
const a = {
  a: 'a'
};
const b = {
  b: 'b'
};
const c = {...a, ...b};
```

