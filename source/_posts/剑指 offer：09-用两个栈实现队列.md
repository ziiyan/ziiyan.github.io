---
title: 剑指 offer：09. 用两个栈实现队列
date: 2020-05-27 23:06:18
tags: 刷题
---

# 题目

用两个栈实现一个队列。队列的声明如下，请实现它的两个函数 appendTail 和 deleteHead ，分别完成在队列尾部插入整数和在队列头部删除整数的功能。(若队列中没有元素，deleteHead 操作返回 -1 )



提示：

1 <= values <= 10000
最多会对 appendTail、deleteHead 进行 10000 次调用

来源：力扣（LeetCode）
链接：https://leetcode-cn.com/problems/yong-liang-ge-zhan-shi-xian-dui-lie-lcof
著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。



# 解题思路

`stack1` 负责入队，保证入队顺序。要么为空，要么是正确的队列顺序。

`stack2` 负责出队，保证出队顺序。要么为空，要么是逆序的队列顺序。



# 代码

```js
var CQueue = function() {
    this.stack1 = [];
    this.stack2 = [];
};

/** 
 * @param {number} value
 * @return {void}
 */
CQueue.prototype.appendTail = function(value) {
    while (this.stack2.length) {
        this.stack1.push(this.stack2.pop())
    }
    this.stack1.push(value)
    return null;
};

/**
 * @return {number}
 */
CQueue.prototype.deleteHead = function() {
    while (this.stack1.length) {
        this.stack2.push(this.stack1.pop())
    }
    return this.stack2.length ? this.stack2.pop() : -1
};

/**
 * Your CQueue object will be instantiated and called as such:
 * var obj = new CQueue()
 * obj.appendTail(value)
 * var param_2 = obj.deleteHead()
 */
```



# 总结

* 水题