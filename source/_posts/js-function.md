
---
title: Js运算符/函数习题
categories: 
- js
---
本文主要介绍Js运算符/函数相关题目
<!--more-->

#### 1. 扩展运算符
```
function getAge(...args) {
  console.log(typeof args)
}

getAge(21)
```
- A: "number"
- B: "array"
- C: "object"
- D: "NaN"
> 答案：C
>扩展运算符（...args）会返回实参组成的数组。而数组是对象，因此 typeof args 返回 "object"。

```
[...'Lydia']
```
- A: ["L", "y", "d", "i", "a"]
- B: ["Lydia"]
- C: [[], "Lydia"]
- D: [["L", "y", "d", "i", "a"]]
> 答案：A
> string 类型是可迭代的。扩展运算符将迭代的每个字符映射成一个元素。

#### 2. parseInt函数
```
const num = parseInt("7*6", 10);
```
- A: 42 
- B: "42"
- C: 7
- D: NaN
> 答案：C
> 只返回了字符串中第一个字母. 设定了 进制 后 (也就是第二个参数，指定需要解析的数字是什么进制: 十进制、十六机制、八进制、二进制等等……),parseInt 检查字符串中的字符是否合法. 一旦遇到一个在指定进制中不合法的字符后，立即停止解析并且忽略后面所有的字符。*就是不合法的数字字符。所以只解析到"7"，并将其解析为十进制的7. num的值即为7.

#### 3. map函数
```
[1, 2, 3].map(num => {
  if (typeof num === "number") return;
  return num * 2;
});
```
- A: []
- B: [null, null, null]
- C: [undefined, undefined, undefined]
- D: [ 3 x empty ]
>答案：C
>对数组进行映射的时候,num就是当前循环到的元素. 在这个例子中，所有的映射都是number类型，所以if中的判断typeof num === "number"结果都是true.map函数创建了新数组并且将函数的返回值插入数组。但是，没有任何值返回。当函数没有返回任何值时，即默认返回undefined.对数组中的每一个元素来说，函数块都得到了这个返回值，所以结果中每一个元素都是undefined.


#### 4. throw
```
function greeting() {
  throw "Hello world!";
}

function sayHi() {
  try {
    const data = greeting();
    console.log("It worked!", data);
  } catch (e) {
    console.log("Oh no an error!", e);
  }
}

sayHi();
```
- A: "It worked! Hello world!"
- B: "Oh no an error: undefined
- C: SyntaxError: can only throw Error objects
- D: "Oh no an error: Hello world!
> 答案: D
> 通过throw语句，我么可以创建自定义错误。 而通过它，我们可以抛出异常。异常可以是一个字符串, 一个 数字, 一个 布尔类型 或者是一个 对象。在本例中，我们的异常是字符串'Hello world'.通过 catch语句，我们可以设定当try语句块中抛出异常后应该做什么处理。在本例中抛出的异常是字符串'Hello world'. e就是这个字符串，因此被输出。最终结果就是'Oh an error: Hello world'.



