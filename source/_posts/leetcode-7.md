---
title: leetcode-7 整数反转
categories: 
- leetcode
---

#### 题目描述
<p>给出一个 32 位的有符号整数，你需要将这个整数中每位上的数字进行反转。</p>
<!--more-->

<p><strong>示例&nbsp;1:</strong></p>

<pre><strong>输入:</strong> 123
<strong>输出:</strong> 321
</pre>

<p><strong>&nbsp;示例 2:</strong></p>

<pre><strong>输入:</strong> -123
<strong>输出:</strong> -321
</pre>

<p><strong>示例 3:</strong></p>

<pre><strong>输入:</strong> 120
<strong>输出:</strong> 21
</pre>

<p><strong>注意:</strong></p>

<p>假设我们的环境只能存储得下 32 位的有符号整数，则其数值范围为&nbsp;[&minus;2<sup>31</sup>,&nbsp; 2<sup>31&nbsp;</sup>&minus; 1]。请根据这个假设，如果反转后整数溢出那么就返回 0。</p>
<div><div>Related Topics</div><div><li>数学</li></div></div>

#### 官方思路

我们可以一次构建反转整数的一位数字。在这样做的时候，我们可以预先检查向原整数附加另一位数字是否会导致溢出。

#### JavaScript解题思路:

1. 存储符号 Math.sign(x)
2. 反转数字
    - 转换为string x.toString()
    - 拆分为char数组 x.toString().split("")
    - 反转数组并拼接 x.toString().split("").reverse().join()
    - 转换为number parseInt(x.toString().split("").reverse().join())
    - 反转后数字溢出过滤 - Math.pow(2, 31) Math.pow(2, 31) -1
3. 符号与数字相乘并返回

```
var reverse = function(x) {
    var sign = Math.sign(x);
    var rX = parseInt(x.toString().split("").reverse().join(""));
    var result = sign * rX;
    var min = - Math.pow(2,31);
    var max = Math.pow(2, 31) - 1;
    if(rX < min || rX > max){
        return 0;
    }
    return result;
};
```

