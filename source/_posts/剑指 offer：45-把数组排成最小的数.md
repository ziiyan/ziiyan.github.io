---
title: 剑指 offer：45.把数组排成最小的数
date: 2020-05-28 21:35:18
tags: 刷题
---

# 题目

输入一个非负整数数组，把数组里所有数字拼接起来排成一个数，打印能拼接出的所有数字中最小的一个。



提示:

0 < nums.length <= 100
说明:

输出结果可能非常大，所以你需要返回一个字符串而不是整数
拼接起来的数字可能会有前导 0，最后结果不需要去掉前导 0

来源：力扣（LeetCode）
链接：https://leetcode-cn.com/problems/ba-shu-zu-pai-cheng-zui-xiao-de-shu-lcof
著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。



# 解题思路

自定义排序

ab < ba 则 [a, b]

ab > ba 则 [b, a]

不知道规律大概想破脑袋也想不出来？



# 代码

```js
/**
 * @param {number[]} nums
 * @return {string}
 */
var minNumber = function(nums) {
    return nums.sort((a, b) => (a+''+b) - (b+''+a)).join('');
};
```



# 总结

*  JavaScript sort
  *  若 a 小于 b，在排序后的数组中 a 应该出现在 b 之前，则返回一个小于 0 的值。
  *  若 a 等于 b，则返回 0。
  *  若 a 大于 b，则返回一个大于 0 的值。
*  JavaScript join
	*  把数组中的所有元素放入一个字符串
	*  参数为要使用的分隔符。如果省略该参数，则使用逗号作为分隔符。