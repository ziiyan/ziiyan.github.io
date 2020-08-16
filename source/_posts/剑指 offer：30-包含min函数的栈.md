---
title: 剑指 offer：30. 包含min函数的栈
date: 2020-05-27 23:14:18
tags: 刷题
---

# 题目

定义栈的数据结构，请在该类型中实现一个能够得到栈的最小元素的 min 函数在该栈中，调用 min、push 及 pop 的时间复杂度都是 O(1)。




提示：

各函数的调用总次数不超过 20000 次

来源：力扣（LeetCode）
链接：https://leetcode-cn.com/problems/bao-han-minhan-shu-de-zhan-lcof
著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。



# 解题思路

维护一个单调递减的栈 `minn`，保证当前 `stack` 中的最小值始终保持在 `minn` 的末尾（栈顶）

`stack `出栈的时候检查是否是最小值（即等于`minn` 栈顶元素）



# 代码

```js
/**
 * initialize your data structure here.
 */
var MinStack = function() {
    this.minn = [];
    this.stack = [];
};

/** 
 * @param {number} x
 * @return {void}
 */
MinStack.prototype.push = function(x) {
    this.stack.push(x);
    if (!this.minn.length) {
        this.minn.push(x);
    } else if (x <= this.minn[this.minn.length-1]) {
        this.minn.push(x);
    }
};

/**
 * @return {void}
 */
MinStack.prototype.pop = function() {
    let t = this.stack.pop();
    if (t === this.minn[this.minn.length-1]) {
        this.minn.pop();
    }
    return t;
};

/**
 * @return {number}
 */
MinStack.prototype.top = function() {
    return this.stack[this.stack.length-1];
};

/**
 * @return {number}
 */
MinStack.prototype.min = function() {
    return this.minn[this.minn.length-1];
};

/**
 * Your MinStack object will be instantiated and called as such:
 * var obj = new MinStack()
 * obj.push(x)
 * obj.pop()
 * var param_3 = obj.top()
 * var param_4 = obj.min()
 */
```



# 总结

联动 [队列的最大值](https://ziiyan.github.io/2020/05/27/59-队列的最大值/)