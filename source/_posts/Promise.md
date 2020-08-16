---
title: Promise 学习笔记
date: 2020-03-25 12:33:36
tags: JavaScript
---

原文：[https://mengera88.github.io/2017/05/18/Promise%E5%8E%9F%E7%90%86%E8%A7%A3%E6%9E%90/](https://mengera88.github.io/2017/05/18/Promise原理解析/)

## 极简雏形

```js
function Promise(fn) {
  var value = null,
      callbacks = [];
  
  this.then = function(onFulfilled) {
    callbacks.push(onFulfilled);
  };
  
  function resolve(value) {
    callbacks.forEach(function (callback) {
      callback(value);
    });
  }
  
  fn(resolve);
}
```

* 代码逻辑
	1. 调用 `then` 方法，将想要在 `Promise` 异步操作成功时执行的回调放入 `callbacks` 队列
		* 就是注册回调函数，观察者模式
	2. 创建 `Promise` 实例时传入的函数 `fn` 会被赋予一个函数类型的参数，即 `resolve`
		* `resolve` 接收一个参数 value，代表异步操作的返回结果
	3. 当异步操作执行成功后，用户会调用 `resolve` 方法。真实操作是将 `callbacks` 队列中的回调一一执行
* 代码分析
	* `new Promise` 时，传给 `promise` 的函数发送异步请求
	* 接着调用 `promise` 对象的 `then` 属性，注册请求成功的回调函数
	* 当异步请求发送成功时，调用 `resolve(results.id)` 方法，该方法执行 `then` 方法注册的回调数组

### 支持链式调用

```js
function Promise(fn) {
  var value = null,
      callbacks = [];
  
  this.then = function(onFulfilled) {
    callbacks.push(onFulfilled);
==> return this;
  };
  
  function resolve(value) {
    callbacks.forEach(function (callback) {
      callback(value);
    });
  }
  
  fn(resolve);
}
```

### 加入延时时机

* 保证 `resolve` 执行之前，`then` 方法已经注册完所有的回调

```js
function Promise(fn) {
  var value = null,
      callbacks = [];
  
  this.then = function(onFulfilled) {
    callbacks.push(onFulfilled);
    return this;
  };
  
  function resolve(value) {
==> setTimeout(function() {
      callbacks.forEach(function (callback) {
        callback(value);
      });
==> }, 0)
	}
  
  fn(resolve);
}
```

* 代码逻辑
	* 利用 `setTimeout` 机制，将 `resolve` 中执行回调的逻辑放置到任务队列末尾，保证执行 `resolve` 中的代码时，`then` 方法的回调函数已经注册完成
* 问题
	* 在 `Promise` 异步操作成功之后才调用 `then` 注册的回调不会执行

### 加入状态

* 即加入 pending -> fulfilled
* 或 pending -> rejected

```js
function Promise(fn) {
  var value = null,
      callbacks = [],
==>   state = 'pending';
  
  this.then = function(onFulfilled) {
==> if (state === 'pending') {
      callbacks.push(onFulfilled);
      return this;
==> }
==> onFulfilled(value);
==> return this;
  };
  
  function resolve(newValue) {
==> value = newValue;
==> state = 'fulfilled';
    setTimeout(function() {
      callbacks.forEach(function (callback) {
        callback(value);
      });
    }, 0)
  }
  
  fn(resolve);
}
```

* 代码逻辑
	* `resolve` 执行时，将状态设置为 `fultilled`，之后调用`then` 添加的新回调都会立即执行，不需要加入 `callbacks` 队列

### 链式 Promise

* 如果 `then` 函数里面注册的仍是 `Promise`，只要在`then`方法里面`return`一个`promise`

```js
function Promise(fn) {
  var state = 'pending',
      value = null,
      callbacks = [];
  
  this.then = function(onFulfilled) {
>   return new Promise(function (resolve) {
>     handle({
>       onFulfilled: onFulfilled || null,
>       resolve: resolve
>     });
>   });
  };
  
> function handle(callback) {
    if (state === 'pending') {
      callbacks.push(callback);
      return;
    }
>   // 如果 then 中没有传递任何东西
>   if (!callback.onFultilled) {
>     callback.resolve(value);
>     return;
>   }
>   var ret = callback.onFulfilled(value);
>   callback.resolve(ret);
  }
  
  function resolve(newValue) {
>   if (newValue && (typeof newValue === 'object' || typeof newValue === 'function')) {
>     var then = newValue.then;
>     if (typeof then === 'function') {
>       then.call(newValue, resolve);
>       return;
>     }
>   }
    value = newValue;
    state = 'fulfilled';
    setTimeout(function() {
      callbacks.forEach(function (callback) {
        handle(callback);
      });
    }, 0)
  }
  
  fn(resolve);
}
```

* 代码逻辑
	1. `then` 方法中，创建并返回了新的 `Promise` 实例
		* 串行 `Promise` 的基础，并且支持链式调用
	2. `handle` 方法是 `Promise` 内部的方法，`then` 方法传入的行参 `onFulfilled` 以及创建新 `Promise` 实例时传入的 `resolve` 均被 `push` 到当前 `promise` 的 `callbacks` 队列中
		* 这是衔接当前 `Promise` 实例和后邻 `promise` 的关键

### 失败处理

```js
function Promise(fn) {
  var state = 'pending',
      value = null,
      callbacks = [];
  
  this.then = function(onFulfilled) {
    return new Promise(function (resolve) {
      handle({
        onFulfilled: onFulfilled || null,
>       onRejected: onRejected || null,
        resolve: resolve,
>       reject: reject
      });
    });
  };
  
  function handle(callback) {
    if (state === 'pending') {
      callbacks.push(callback);
      return;
    }
    // 如果 then 中没有传递任何东西
    if (!callback.onFultilled) {
      callback.resolve(value);
      return;
    }
>   var cb = state === 'fullfilled' ? callback.onFulfilled : callback.onRejected;
>   if (cb === null) {
>     cb = state === 'fulfilled' ? callback.resolve : callback.reject;
>     cb(value);
>     return;
>   }
    var ret = cb(value);
    callback.resolve(ret);
  }
  
  function resolve(newValue) {
    if (newValue && (typeof newValue === 'object' || typeof newValue === 'function')) {
      var then = newValue.then;
      if (typeof then === 'function') {
        then.call(newValue, resolve);
        return;
      }
    }
    state = 'fulfilled';
    value = newValue;
>   execute();
  }
  
> function reject(reason) {
>   state = 'rejected';
>   value = reason;
>   execute();
> }
    
  function execute() {
    setTimeout(function() {
      callbacks.forEach(function (callback) {
        handle(callback);
      });
    }, 0)
  }
  
  fn(resolve);
}
```

* 抽离 `resolve` 和 `reject` 公共部分作为 `execute` 

### 异常处理

```js
function Promise(fn) {
  var state = 'pending',
      value = null,
      callbacks = [];
  
  this.then = function(onFulfilled) {
    return new Promise(function (resolve) {
      handle({
        onFulfilled: onFulfilled || null,
        onRejected: onRejected || null,
        resolve: resolve,
        reject: reject
      });
    });
  };
  
  function handle(callback) {
    if (state === 'pending') {
      callbacks.push(callback);
      return;
    }
    // 如果 then 中没有传递任何东西
    if (!callback.onFultilled) {
      callback.resolve(value);
      return;
    }
    var cb = state === 'fullfilled' ? callback.onFulfilled : callback.onRejected;
    if (cb === null) {
      cb = state === 'fulfilled' ? callback.resolve : callback.reject;
      cb(value);
      return;
    }
>   try {
      var ret = cb(value);
      callback.resolve(ret);
>   } catch (e) {
>     callback.reject(e);
>   }
  }
  
  function resolve(newValue) {
    if (newValue && (typeof newValue === 'object' || typeof newValue === 'function')) {
      var then = newValue.then;
      if (typeof then === 'function') {
        then.call(newValue, resolve);
        return;
      }
    }
    state = 'fulfilled';
    value = newValue;
    execute();
  }
  
  function reject(reason) {
    state = 'rejected';
    value = reason;
    execute();
  }
    
  function execute() {
    setTimeout(function() {
      callbacks.forEach(function (callback) {
        handle(callback);
      });
    }, 0)
  }
  
  fn(resolve);
}
```

### 总结

* promise里面的then函数仅仅是注册了后续需要执行的代码
* 真正的执行是在resolve方法里面执行的

> Promise 主要使用了设计模式中的观察者模式：
>
> 1. 通过 Promise.prototype.then 和 Promise.prototype.catch 方法将观察者方法注册到被观察者 Promise 对象中，同时返回一个新的 Promise 对象，以便可以链式调用。
> 2. 被观察者管理内部 pending、fulfilled 和 rejected 的状态转变，同时通过构造函数中传递的 resolve 和 reject 方法以主动触发状态转变和通知观察者。