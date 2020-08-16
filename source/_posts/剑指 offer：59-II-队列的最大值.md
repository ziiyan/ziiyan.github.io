---
title: 剑指 offer：59-II.队列的最大值
date: 2020-05-27 23:42:18
tags: 刷题
---

# 题目

请定义一个队列并实现函数 max_value 得到队列里的最大值，要求函数max_value、push_back 和 pop_front 的均摊时间复杂度都是O(1)。

若队列为空，pop_front 和 max_value 需要返回 -1




限制：

1 <= push_back,pop_front,max_value的总操作数 <= 10000
1 <= value <= 10^5

来源：力扣（LeetCode）
链接：https://leetcode-cn.com/problems/dui-lie-de-zui-da-zhi-lcof
著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。



# 解题思路

维护一个单调递减的队列 `maxx`。

`value` 入队时，检查 `maxx` 尾部，小于当前 `value` 的元素全部出栈。

> 入队 4
>
> queue 4
>
> maxx 4
>
> ---
>
> 入队 2
>
> queue 4 2
>
> maxx 4 2
>
> ---
>
> 入队 3
>
> queue 4 2 3
>
> maxx 4 3
>
> ---
>
> 出队
>
> queue 2 3
>
> maxx 3

可以保证 `maxx` 队首的始终是 `queue` 中的最大值



# 代码

```js
var MaxQueue = function() {
    this.queue = [];
    this.maxx = [];
};

/**
 * @return {number}
 */
MaxQueue.prototype.max_value = function() {
    if (this.maxx.length) {
        return this.maxx[0];
    }
    return -1;
};

/** 
 * @param {number} value
 * @return {void}
 */
MaxQueue.prototype.push_back = function(value) {
    this.queue.push(value);
    while (this.maxx.length && this.maxx[this.maxx.length-1] < value) {
        this.maxx.pop();
    }
    this.maxx.push(value);
};

/**
 * @return {number}
 */
MaxQueue.prototype.pop_front = function() {
    if (this.queue.length) {
        let cur = this.queue.shift();
        if (cur === this.maxx[0]) {
            this.maxx.shift();
        }
        return cur;
    }
    return -1;
};

/**
 * Your MaxQueue object will be instantiated and called as such:
 * var obj = new MaxQueue()
 * var param_1 = obj.max_value()
 * obj.push_back(value)
 * var param_3 = obj.pop_front()
 */
```



# 总结

联动 [包含min函数的栈](https://ziiyan.github.io/2020/05/27/30-包含min函数的栈)