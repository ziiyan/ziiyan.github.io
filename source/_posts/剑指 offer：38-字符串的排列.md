---
title: 剑指 offer：38.字符串的排列
date: 2020-05-25 23:42:18
tags: 刷题
---

# 题目

输入一个字符串，打印出该字符串中字符的所有排列。

你可以以任意顺序返回这个字符串数组，但里面不能有重复元素。




限制：

1 <= s 的长度 <= 8

来源：力扣（LeetCode）
链接：https://leetcode-cn.com/problems/zi-fu-chuan-de-pai-lie-lcof
著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。



# 解题思路

字符串 `"abc"` ，作为数组 `['a', 'b', 'c']`，对数组`['a', 'b', 'c']` 的元素进行全排列，去重。

`vis` 标记数组对应位置是否已经使用过。

递归，每次在数组中选择一个未使用的字符放入临时字符串。

递归出口：临时字符串长度 = 输入字符串长度。

采用 Set 去重。

# 代码

```js
/**
 * @param {string} s
 * @return {string[]}
 */
var permutation = function(s) {
    let len = s.length;
    let vis = new Array(len).fill(false);
    const dfs = function (ans, str) {
        if (str.length === len) {
            ans.add(str);
            return;
        }
        for (let i = 0; i < len; i++) {
            if (!vis[i]) {
                vis[i] = true;
                dfs(ans, str+s[i]);
                vis[i] = false;
            }
        }
    }
    let ans = new Set();
    dfs(ans, '');
    return [...ans];
};
```



# 总结

排列 🔗 回溯

set 用法

*  `add()` 添加元素
* 扩展运算符 `...`
	* [...Set] ===> Array