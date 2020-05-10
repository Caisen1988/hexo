---
title: Js this
categories: 
- js
---
本文主要介绍Js this 相关题目
<!--more-->
```
const shape = {
  radius: 10,
  diameter() {
    return this.radius * 2
  },
  perimeter: () => 2 * Math.PI * this.radius
}

shape.diameter()
shape.perimeter()

```
> 答案：20 and NaN


- 注意 diameter 的值是一个常规函数，但是 perimeter 的值是一个箭头函数。对于箭头函数，this 关键字指向的是它当前周围作用域（**简单来说是包含箭头函数的常规函数，如果没有常规函数的话就是全局对象**），这个行为和常规函数不同。
- 这意味着当我们调用 perimeter 时，this 不是指向 shape 对象，而是它的周围作用域（在例子中是 window）。在 window 中没有 radius 这个属性，因此返回 undefined。

```
function Person(firstName, lastName) {
  this.firstName = firstName
  this.lastName = lastName
}

const lydia = new Person('Lydia', 'Hallie')
const sarah = Person('Sarah', 'Smith')

console.log(lydia)
console.log(sarah)

```
- A: Person {firstName: "Lydia", lastName: "Hallie"} and undefined
- B: Person {firstName: "Lydia", lastName: "Hallie"} and Person {firstName: "Sarah", lastName: "Smith"}
- C: Person {firstName: "Lydia", lastName: "Hallie"} and {}
- D:Person {firstName: "Lydia", lastName: "Hallie"} and ReferenceError
>答案：A
>
对于 sarah，我们没有使用 new 关键字。当使用 new 时，this 引用我们创建的空对象。当未使用 new 时，this 引用的是全局对象（global object）。我们说 this.firstName 等于 "Sarah"，并且 this.lastName 等于 "Smith"。实际上我们做的是，定义了 global.firstName = 'Sarah' 和 global.lastName = 'Smith'。而 sarah 本身是 undefined。

