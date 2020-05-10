
---
title: Js变量
categories: 
- js
---
本文主要介绍Js变量相关题目
<!--more-->

#### 1. 变量提升
```
function sayHi() {
  console.log(name)
  console.log(age)
  var name = 'Lydia'
  let age = 21
}

sayHi() //undefined 和 ReferenceError
```


- 在函数内部，我们首先通过 var 关键字声明了 name 变量。这意味着变量被提升了（内存空间在创建阶段就被设置好了），直到程序运行到定义变量位置之前默认值都是 undefined。因为当我们打印 name 变量时还没有执行到定义变量的位置，因此变量的值保持为 undefined。

- 通过 let 和 const 关键字声明的变量也会提升，但是和 var 不同，它们不会被初始化。在我们声明（初始化）之前是不能访问它们的。这个行为被称之为暂时性死区。当我们试图在声明之前访问它们时，JavaScript 将会抛出一个 ReferenceError 错误。

#### 2. 变量作用域
```
for (var i = 0; i < 3; i++) {
  setTimeout(() => console.log(i), 1)
}

for (let i = 0; i < 3; i++) {
  setTimeout(() => console.log(i), 1)
}
//执行结果将打印3 3 3 和 0 1 2
```

- 由于 JavaScript 的事件循环，setTimeout 回调会在遍历结束后才执行。因为在第一个遍历中遍历 i 是通过 var 关键字声明的，所以这个值是全局作用域下的。在遍历过程中，我们通过一元操作符 ++ 来每次递增 i 的值。当 setTimeout 回调执行的时候，i 的值等于 3。

- 在第二个遍历中，遍历 i 是通过 let 关键字声明的：通过 let 和 const 关键字声明的变量是拥有块级作用域（指的是任何在 {} 中的内容）。在每次的遍历过程中，i 都有一个新值，并且每个值都在循环内的作用域中。



```
let greeting
greetign = {} // Typo!
console.log(greetign)

```

- A: {}
- B: ReferenceError: greetign is not defined
- C: undefined
>答案：A

- 代码打印出了一个对象，这是因为我们在全局对象上创建了一个空对象！当我们将 greeting 写错成 greetign 时，JS 解释器实际在上浏览器中将它视为 global.greetign = {} （或者 window.greetign = {}）。
- 为了避免这个为题，我们可以使用 `"use strict"。这能确保当你声明变量

#### 3. 隐式类型转换
```
function sum(a, b) {
  return a + b
}

sum(1, '2')
```
- A: NaN
- B: TypeError
- C: "12"
- D: 3
> 答案：C

- JavaScript 是一种动态类型语言：我们不指定某些变量的类型。值可以在你不知道的情况下自动转换成另一种类型，这种类型称为隐式类型转换（implicit type coercion）。Coercion 是指将一种类型转换为另一种类型。
- 在本例中，JavaScript 将数字 1 转换为字符串，以便函数有意义并返回一个值。在数字类型（1）和字符串类型（'2'）相加时，该数字被视为字符串。我们可以连接字符串，比如 "Hello" + "World"，这里发生的是 "1" + "2"，它返回 "12"。

#### 4. use strict
```
function getAge() {
  'use strict'
  age = 21
  console.log(age)
}

getAge()
```
- A: 21
- B: undefined
- C: ReferenceError
- D: TypeError
>答案：C
>使用 "use strict"，你可以确保不会意外地声明全局变量。我们从来没有声明变量 age，因为我们使用 "use strict"，它将抛出一个引用错误。如果我们不使用 "use strict"，它就会工作，因为属性 age 会被添加到全局对象中了。
>ReferenceError就是在作用域中找不到、TypeError是在作用域中找到了但是 做了它不可能做的事情。


#### 5. 变量赋值
```
let person = { name: "Lydia" };
const members = [person];
person = null;

console.log(members);
```
- A: null
- B: [null]
- C: [{}]
- D: [{ name: "Lydia" }]
> 答案：D
> members[0]的指向还没有发生变化，还是指向{name: "Lydia"}, person的指向发生了变化指向了null
