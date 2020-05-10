
---
title: Js 对象习题
categories: 
- js
---
本文主要介绍Js对象相关题目
<!--more-->



#### 1. 对象的key值

```
const bird = {
  size: 'small'
}

const mouse = {
  name: 'Mickey',
  small: true
}
```

- A: mouse.bird.size是无效的
- B: mouse[bird.size]是无效的
- C: mouse[bird["size"]]是无效的
- D: 以上三个选项都是有效的

> 答案：A


- 在 JavaScript 中，所有对象的 keys 都是字符串（除非对象是 Symbol）。尽管我们可能不会定义它们为字符串，但它们在底层总会被转换为字符串。

- 当我们使用括号语法时（[]），JavaScript 会解释（或者 unboxes）语句。它首先看到第一个开始括号 [ 并继续前进直到找到结束括号 ]。只有这样，它才会计算语句的值。
- mouse[bird.size]：首先计算 bird.size，这会得到 small。mouse["small"] 返回 true。
- 然后使用点语法的话，上面这一切都不会发生。mouse 没有 bird 这个 key，这也就意味着 mouse.bird 是 undefined。然后当我们使用点语法 mouse.bird.size 时，因为 mouse.bird 是 undefined，这也就变成了 undefined.size。这个行为是无效的，并且会抛出一个错误类似 Cannot read property "size" of undefined。

#### 2. 对象传递
```
let c = { greeting: 'Hey!' }
let d

d = c
c.greeting = 'Hello'
console.log(d.greeting)

```
>答案 Hello

- 在 JavaScript 中，当设置两个对象彼此相等时，它们会通过引用进行交互。
- 首先，变量 c 的值是一个对象。接下来，我们给 d 分配了一个和 c 对象相同的引用。
- 因此当我们改变其中一个对象时，其实是改变了所有的对象。


#### 3. 对象中的static关键字
```
class Chameleon {
  static colorChange(newColor) {
    this.newColor = newColor
    return this.newColor
  }

  constructor({ newColor = 'green' } = {}) {
    this.newColor = newColor
  }
}

const freddie = new Chameleon({ newColor: 'purple' })
freddie.colorChange('orange')

```
>答案 TypeError

- colorChange 是一个静态方法。静态方法被设计为只能被创建它们的构造器使用（也就是 Chameleon），并且不能传递给实例。因为 freddie 是一个实例，静态方法不能被实例使用，因此抛出了 TypeError 错误。


#### 4. 函数对象
```
function bark() {
  console.log('Woof!')
}

bark.animal = 'dog'

```

- A: 正常运行!
- B: SyntaxError. 你不能通过这种方式给函数增加属性。
- C: undefined
- D: ReferenceError
>答案 A
 这在 JavaScript 中是可以的，因为函数是对象！（除了基本类型之外其他都是对象）
 函数是一个特殊的对象。你写的这个代码其实不是一个实际的函数。函数是一个拥有属性的对象，并且属性也可被调用。

```
function Person(firstName, lastName) {
  this.firstName = firstName;
  this.lastName = lastName;
}

const member = new Person("Lydia", "Hallie");
Person.getFullName = function () {
  return `${this.firstName} ${this.lastName}`;
}

console.log(member.getFullName());
```
- A: TypeError
- B: SyntaxError
- C: Lydia Hallie
- D: undefined undefined
>答案：A
你不能像常规对象那样，给构造函数添加属性。如果你想一次性给所有实例添加特性，你应该使用原型。因此本例中，使用如下方式：
```
Person.prototype.getFullName = function () {
  return `${this.firstName} ${this.lastName}`;
}
```
>这才会使 member.getFullName() 起作用。为什么这么做有益的？假设我们将这个方法添加到构造函数本身里。也许不是每个 Person 实例都需要这个方法。这将浪费大量内存空间，因为它们仍然具有该属性，这将占用每个实例的内存空间。相反，如果我们只将它添加到原型中，那么它只存在于内存中的一个位置，但是所有实例都可以访问它！

#### 5.判断对象是否相等
```
function checkAge(data) {
  if (data === { age: 18 }) {
    console.log('You are an adult!')
  } else if (data == { age: 18 }) {
    console.log('You are still an adult.')
  } else {
    console.log(`Hmm.. You don't have an age I guess`)
  }
}

checkAge({ age: 18 })
```
- A: You are an adult!
- B: You are still an adult.
- C: Hmm.. You don't have an age I guess
> 答案： C
> 在测试相等性时，基本类型通过它们的值（value）进行比较，而对象通过它们的引用（reference）进行比较。JavaScript 检查对象是否具有对内存中相同位置的引用。
> 题目中我们正在比较的两个对象不是同一个引用：作为参数传递的对象引用的内存位置，与用于判断相等的对象所引用的内存位置并不同。
> 这也是 { age: 18 } === { age: 18 } 和 { age: 18 } == { age: 18 } 都返回 false 的原因。


#### 6.数组赋值
```
const numbers = [1, 2, 3]
numbers[10] = 11
console.log(numbers)
```
- A: [1, 2, 3, 7 x null, 11]
- B: [1, 2, 3, 11]
- C: [1, 2, 3, 7 x empty, 11]
- D: SyntaxError
> 答案：C
> 当你为数组设置超过数组长度的值的时候， JavaScript 会创建名为 "empty slots" 的东西。它们的值实际上是 undefined。你会看到以下场景：[1, 2, 3, 7 x empty, 11]这取决于你的运行环境（每个浏览器，以及 node 环境，都有可能不同）

#### 7.循环
```
const person = {
  name: "Lydia",
  age: 21
};

for (const item in person) {
  console.log(item);
}
```
- A: { name: "Lydia" }, { age: 21 }
- B: "name", "age"
- C: "Lydia", 21
- D: ["name", "Lydia"], ["age", 21]
> 答案 B
> 在for-in循环中,我们可以通过对象的key来进行迭代,也就是这里的name和age。在底层，对象的key都是字符串（如果他们不是Symbol的话）。在每次循环中，我们将item设定为当前遍历到的key.所以一开始，item是name，之后 item输出的则是age







