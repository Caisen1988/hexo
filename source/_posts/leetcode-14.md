---
title: leetcode-14 最长公共前缀
categories: 
- leetcode
---


#### 题目描述
<p>编写一个函数来查找字符串数组中的最长公共前缀。</p>

<p>如果不存在公共前缀，返回空字符串&nbsp;<code>&quot;&quot;</code>。</p>
<!--more-->
<p><strong>示例&nbsp;1:</strong></p>

<pre><strong>输入: </strong>[&quot;flower&quot;,&quot;flow&quot;,&quot;flight&quot;]
<strong>输出:</strong> &quot;fl&quot;
</pre>

<p><strong>示例&nbsp;2:</strong></p>

<pre><strong>输入: </strong>[&quot;dog&quot;,&quot;racecar&quot;,&quot;car&quot;]
<strong>输出:</strong> &quot;&quot;
<strong>解释:</strong> 输入不存在公共前缀。
</pre>

<p><strong>说明:</strong></p>

<p>所有输入只包含小写字母&nbsp;<code>a-z</code>&nbsp;。</p>
<div><div>Related Topics</div><div><li>字符串</li></div></div>

#### javascript解题思路
- 当字符串数组长度为 0 时则公共前缀为空，直接返回
- 令最长公共前缀 ans 的值为第一个字符串，进行初始化
- 遍历后面的字符串，依次将其与 ans 进行比较，两两找出公共前缀，最终结果即为最长公共前缀
- 如果查找过程中出现了 ans 为空的情况，则公共前缀不存在直接返回
- 时间复杂度：O(s)O(s)，s 为所有字符串的长度之和
```
var longestCommonPrefix = function (strs) {
    if (strs.length == 0)
        return "";
    let ans = strs[0];
    for (let i = 1; i < strs.length; i++) {
        for (let j = 0; j < ans.length && j < strs[i].length; j++) {
            if (ans[j] != strs[i][j])
                break;
        }
        ans = ans.substr(0, j);
        if (ans === "")
            return ans;
    }
    return ans;
};
```