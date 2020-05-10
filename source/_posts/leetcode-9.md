---
title: leetcode-9 回文数
categories: 
- leetcode
---
#### 题目描述

<p>判断一个整数是否是回文数。回文数是指正序（从左向右）和倒序（从右向左）读都是一样的整数。</p>

<p><strong>示例 1:</strong></p>

<pre><strong>输入:</strong> 121
<strong>输出:</strong> true
</pre>
<!--more-->
<p><strong>示例&nbsp;2:</strong></p>

<pre><strong>输入:</strong> -121
<strong>输出:</strong> false
<strong>解释:</strong> 从左向右读, 为 -121 。 从右向左读, 为 121- 。因此它不是一个回文数。
</pre>

<p><strong>示例 3:</strong></p>

<pre><strong>输入:</strong> 10
<strong>输出:</strong> false
<strong>解释:</strong> 从右向左读, 为 01 。因此它不是一个回文数。
</pre>

<p><strong>进阶:</strong></p>

<p>你能不将整数转为字符串来解决这个问题吗？</p>
<div><div>Related Topics</div><div><li>数学</li></div></div>

#### 思路
映入脑海的第一个想法是将数字转换为字符串，并检查字符串是否为回文。但是，这需要额外的非常量空间来创建问题描述中所不允许的字符串。

第二个想法是将数字本身反转，然后将反转后的数字与原始数字进行比较，如果它们是相同的，那么这个数字就是回文。
但是，如果反转后的数字大于 \text{int.MAX}int.MAX，我们将遇到整数溢出问题。

按照第二个想法，为了避免数字反转可能导致的溢出问题，为什么不考虑只反转 \text{int}int 数字的一半？毕竟，如果该数字是回文，其后半部分反转后应该与原始数字的前半部分相同。

例如，输入 1221，我们可以将数字 “1221” 的后半部分从 “21” 反转为 “12”，并将其与前半部分 “12” 进行比较，因为二者相同，我们得知数字 1221 是回文。

让我们看看如何将这个想法转化为一个算法。


#### JavaScript解题思路
直接利用字符串吧

```
var isPalindrome = function (x) {
    if (x < 0) {
        return false;
    }
    return x.toString().split('').reverse().join('') == x

};
```