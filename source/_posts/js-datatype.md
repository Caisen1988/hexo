
---
title: Js数据类型
categories: 
- js
---
本文主要介绍Js数据类型相关题目
<!--more-->
#### 1. 数据类型转换
```
+true;
!"Lydia";
```
> 答案：1 and false

一元操作符加号尝试将 bool 转为 number。true 转换为 number 的话为 1，false 为 0。字符串 'Lydia' 是一个真值，真值取反那么就返回 false。

```
let a = 3
let b = new Number(3)
let c = 3

console.log(a == b)
console.log(a === b)
console.log(b === c)

```
>答案：true false false


- new Number() 是一个内建的函数构造器。虽然它看着像是一个 number，但它实际上并不是一个真实的 number：它有一堆额外的功能并且它是一个对象。
- 当我们使用 == 操作符时，它只会检查两者是否拥有相同的值。因为它们的值都是 3，因此返回 true。
- 然后，当我们使用 === 操作符时，两者的值以及类型都应该是相同的。new Number() 是一个对象而不是 number，因此返回 false

