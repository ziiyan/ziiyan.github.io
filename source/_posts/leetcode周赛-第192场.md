---
title: leetcode周赛-第192场
date: 2020-06-07 19:25:04
tags: 刷题
---

# T1

## 代码

```js
/**
 * @param {number[]} nums
 * @param {number} n
 * @return {number[]}
 */
var shuffle = function(nums, n) {
    let x = nums.slice(0, n);
  	// x: [x1, x2, ..., xn]
    let y = nums.slice(n, 2 * n);
  	// y: [y1, y2, ..., yn]
    let ans = [], t = 0;
    while (t < n) {
        ans.push(x[t]);
        ans.push(y[t]);
      	// ans: [x1, y1, ..., xn, yn]
        t++;
    }
    return ans;
};
```

## 总结

暴力模拟

### 别人的代码：

```js
var shuffle = function(nums, n) {
    let xArr = nums.slice(0, n);
    let yArr = nums.slice(n, nums.length);
    let result = [];
    xArr.forEach((item, index) => {
        result.push(item);
        result.push(yArr[index]);
    });
    return result;
};
```

思想其实差不多，主要是这个 `forEach`，速度好像比较快



### Array.prototype.forEach()

* 方法对数组的每个元素执行一次给定的函数。

#### 语法

```js
arr.forEach(callback(currentValue [, index [, array]])[, thisArg])
```

- `callback`

	为数组中每个元素执行的函数，该函数接收一至三个参数：

	* `currentValue` 数组当前项的值
	* `index` 数组当前项的索引
	* `array` 数组对象本身

- `thisArg` 可选

	* 如果 `thisArg` 参数有值，则每次 `callback` 函数被调用时，`this` 都会指向 `thisArg` 参数

	* 如果省略了 `thisArg` 参数，或者其值为 `null` 或 `undefined`，`this` 则指向全局对象。

- 返回值：undefined

>  `forEach` 不会直接改变调用它的对象，但是那个对象可能会被 `callback` 函数改变
>
> 除了抛出异常以外，没有办法中止或跳出 `forEach()` 循环



### Array.prototype.map()

* `map()` 方法创建一个新数组，其结果是该数组中的每个元素都调用一次提供的函数后的返回值。

#### 语法

```js
var new_array = arr.map(function callback(currentValue[, index[, array]]) {
 // Return element for new_array 
}[, thisArg])
```

- `callback`

	为数组中每个元素执行的函数，该函数接收一至三个参数：

	* `currentValue` 数组当前项的值
	* `index` 数组当前项的索引
	* `array` 数组对象本身

- `thisArg` 可选

	* 如果 `thisArg` 参数有值，则每次 `callback` 函数被调用时，`this` 都会指向 `thisArg` 参数

	* 如果省略了 `thisArg` 参数，或者其值为 `null` 或 `undefined`，`this` 则指向全局对象。

- 返回值：一个由原数组每个元素执行回调函数的结果组成的新数组

> `map `不修改调用它的原数组本身（当然可以在 `callback` 执行时改变原数组）——这个与 forEach 是一样的
>
> 不该使用`map`：
>
> * 不打算使用返回的新数组
> * 没有从回调函数中返回值



### forEach 和 map 区别

`map` 生成一个新数组，`forEach` 则没有

二者均不修改数组，但在回调函数里的修改会同步到原数组

所以区别只有是否返回新的数组

如果同时需要原数组&新数组——map

如果不需要原数组——forEach



# T2

## 代码

```js
/**
 * @param {number[]} arr
 * @param {number} k
 * @return {number[]}
 */
var getStrongest = function(arr, k) {
  	arr.sort((a, b) => a - b);						// 将数组 arr 从小到大排序
    let n = arr.length;
    let m = arr[parseInt((n-1)/2)];				// m: 数组中位数
    let tmp = [];													// tmp: 临时数组，用于统计 每个元素与中位数的距离，即 |arr[i] - m|
    for (let i = 0; i < n; i++) {
        tmp.push({
            val: arr[i],									// val: 数组元素
            dis: Math.abs(arr[i] - m)			// dis: 当前元素与中位数的距离
        });
    }
    tmp.sort((a, b) => {									// 将 tmp 排序（根据题目的值强弱排序）
        if (a.dis === b.dis) {						// 若 dis 相等
            return b.val - a.val;					// 根据 val 从大到小排序
        }
        return b.dis - a.dis;							// 否则根据 dis 从大到小排序
    });
    let ans = tmp.map(obj => {						// 将 tmp 中的 val 抽离出来，赋值到 ans（抛弃 dis）
        return obj.val;
    })
    return ans.slice(0, k);								// 取前 k 位
};
```

## 总结

暴力模拟，排序

### 别人的代码：

```js
/**
 * @param {number[]} arr
 * @param {number} k
 * @return {number[]}
 */

var getStrongest = function(arr, k) {
  let len = arr.length,
      midIndex = Math.floor((len - 1) / 2);
  let other = [...arr].sort((a, b) => a - b);
  let m = other[midIndex];
  arr.sort((a, b) => {
    if (
      Math.abs(a - m) > Math.abs(b - m) ||
      (Math.abs(a - m) == Math.abs(b - m) && a > b)
    ) { // a 比 b 强
      return -1;
    } else {
      return 1;
    }
  });
  return arr.slice(0, k);
};

作者：ignore_express
链接：https://leetcode-cn.com/problems/the-k-strongest-values-in-an-array/solution/js-by-ignore_express-4/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

感觉大家的思路也都差不多……但是别人的写法就是效率比较高（摔

### Array.prototype.sort()

* 用[原地算法](https://en.wikipedia.org/wiki/In-place_algorithm)对数组的元素进行排序，并返回数组
* 默认排序顺序是在**将元素转换为字符串**，然后比较它们的UTF-16代码单元值序列时构建的
	* 数字：11 < 2
	* 大小写：'Banana' < 'cherry'

#### 语法

```
arr.sort([compareFunction(a, b)])
```

`compareFunction` 可选

* 用来指定按某种顺序进行排列的函数。如果省略，元素按照转换为的字符串的各个字符的Unicode位点进行排序。
	* 如果 `compareFunction(a, b)` 小于 0 ，那么 a 会被排列到 b 之前；
	* 如果 `compareFunction(a, b)` 等于 0 ， a 和 b 的相对位置不变。备注： ECMAScript 标准并不保证这一行为
	* 如果 `compareFunction(a, b)` 大于 0 ， b 会被排列到 a 之前。

* 返回值：排序后的数组。请注意：数组已原地排序



# T3

## 代码

```js
/**
 * @param {string} homepage
 */
var BrowserHistory = function(homepage) {
    this._stack = [homepage];
    this._cur = 0;
};

/** 
 * @param {string} url
 * @return {void}
 */
BrowserHistory.prototype.visit = function(url) {
    this._stack = this._stack.slice(0, this._cur + 1);
    this._stack.push(url);
    this._cur += 1;
};

/** 
 * @param {number} steps
 * @return {string}
 */
BrowserHistory.prototype.back = function(steps) {
    this._cur = Math.max(this._cur - steps, 0);
    return this._stack[this._cur];
};

/** 
 * @param {number} steps
 * @return {string}
 */
BrowserHistory.prototype.forward = function(steps) {
    this._cur = Math.min(this._cur + steps, this._stack.length - 1);
    return this._stack[this._cur];
};

/**
 * Your BrowserHistory object will be instantiated and called as such:
 * var obj = new BrowserHistory(homepage)
 * obj.visit(url)
 * var param_2 = obj.back(steps)
 * var param_3 = obj.forward(steps)
 */
```

## 总结

模拟，这种题目没找到好办法，面向测试用例编程



# T4

