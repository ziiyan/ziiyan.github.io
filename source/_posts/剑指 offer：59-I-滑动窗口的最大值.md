---
title: 剑指 offer：59-I.滑动窗口的最大值
date: 2020-05-27 23:52:18
tags: 刷题
---

# 题目

给定一个数组 nums 和滑动窗口的大小 k，请找出所有滑动窗口里的最大值。




提示：

你可以假设 k 总是有效的，在输入数组不为空的情况下，1 ≤ k ≤ 输入数组的大小。

来源：力扣（LeetCode）
链接：https://leetcode-cn.com/problems/hua-dong-chuang-kou-de-zui-da-zhi-lcof
著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。



# 解题思路

写法有点暴力……效率比较差

`Array.slice(i, j) `截取子数组 `Array[i~j]`，`Math.max(...Array)` 取最大



# 代码

```js
/**
 * @param {number[]} nums
 * @param {number} k
 * @return {number[]}
 */
var maxSlidingWindow = function(nums, k) {
    if (nums.length === 0) return [];
    let ans = [];
    for (let i = 0; i <= nums.length - k; i++) {
        let tmp = nums.slice(i, i+k);
        ans.push(Math.max(...tmp));
    }
    return ans;
};
```



# 总结

* Array.slice(i, j)

*  扩展运算符 `...`