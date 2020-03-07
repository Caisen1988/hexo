---
title: js重点概念讲解

categories: 
- js
tags:
- 变量声明
- this
- scope and 闭包
---
重点讲解了如原型、作用域、执行上下文、变量对象、this、闭包、按值传递、call、apply、bind、new、继承等 JS 语言中的比较难懂的概念。
<!--more-->

### 一、变量声明
#### 1.1、大部分编程语言都是先声明变量再使用变量，但JS中有时候并非如此。例如：

```
console.log(test); // undefined`
var test = 0;
```

我们可以看到上面的代码是一个合法的输出。输出值为"underfined",而不是报错“Uncaught ReferenceError: test is not defined”. 为何会产生这样的结果？


#### 1.2、先了解下几个js的变量声明规则：
- 变量声明，不管在哪里发生（声明），都会在任意代码执行前处理。（Variable declarations, wherever they occur, are processed before any code is executed. ）。
- 以var声明的变量的作用域就是当前执行上下文（execution context），即某个函数，或者全局作用域（声明在函数外）。
- 赋值给未声明的变量，当执行时会隐式创建全局变量（成为global的属性）。

#### 1.3、声明提升：
定义：var 声明的变量会在任意代码执行前处理，这意味着在任意地方声明变量都等同于在顶部声明——即声明提升，并且函数声明的优先级要高于变量声明。 
以上就能解释1.1中为何是正常输出而不是错误的原因，即变量的声明提升了。

### 二、this
#### 2.1、this关键词是JavaScript中最令人疑惑的机制之一。this是非常特殊的关键词标识符，在每个函数的作用域中被自动创建，但它到底指向什么（对象），很多人弄不清。
定义：当函数被调用，一个activation record（即 execution context）被创建。这个record包涵信息：函数在哪调用（call-stack），函数怎么调用的，参数等等。record的一个属性就是this，指向函数执行期间的this对象。

#### 2.2、用法：**this的指向在函数定义的时候是确定不了的，只有函数执行的时候才能确定this到底指向谁，实际上this的最终指向的是那个调用它的对象**，一下列出几个常见的使用情景。

#### 情况一：纯粹的函数调用
这是函数的最通常用法，属于全局性调用，因此this就代表全局对象。请看下面这段代码，它的运行结果是1。

```
var x = 1;
function test() {
   console.log(this.x);
}
test();  // 1
```

#### 情况二：作为对象方法的调用
函数还可以作为某个对象的方法调用，这时this就指这个上级对象。
```
function test() {
  console.log(this.x);
}
var obj = {};
obj.x = 1;
obj.m = test;
obj.m(); // 1
```

#### 情况三 作为构造函数调用
所谓构造函数，就是通过这个函数，可以生成一个新对象。这时，this就指这个新对象。
```
function test() {
　this.x = 1;
}
var obj = new test();
obj.x // 1
```
运行结果为1。为了表明这时this不是全局对象，我们对代码做一些改变：
```
var x = 2;
function test() {
  this.x = 1;
}
var obj = new test();
x  // 2
```
运行结果为2，表明全局变量x的值根本没变。

#### 情况四 apply 调用
apply()是函数的一个方法，作用是改变函数的调用对象。它的第一个参数就表示改变后的调用这个函数的对象。因此，这时this指的就是这第一个参数。
```
var x = 0;
function test() {
　console.log(this.x);
}
var obj = {};
obj.x = 1;
obj.m = test;
obj.m.apply() // 0
```
apply()的参数为空时，默认调用全局对象。因此，这时的运行结果为0，证明this指的是全局对象。
如果把最后一行代码修改为
`obj.m.apply(obj); //1`
运行结果就变成了1，证明了这时this代表的是对象obj。


### 三、作用域(Scope)和闭包(Closure)
3.1、作用域定义：，Scope即上下文，包含当前所有可见的变量。Scope是分层的，内层Scope可以访问外层Scope的变量，反之则不行。

3.2、闭包：Javascript语言的特殊之处，就在于函数内部可以直接读取全局变量；另一方面，在函数外部自然无法读取函数内的局部变量。但我们有时候需要得到函数内的局部变量，这时候就需要用到闭包。
```
function f1(){
　　　　var n=999;
　　　　function f2(){
　　　　　　alert(n);
　　　　}
　　　　return f2;
}
var result=f1();
result(); // 999
```
以上的例子中，我们成功的取得了函数f1的内部变量，而f2函数我们就称之为闭包。

**闭包的用途**：
闭包可以用在许多地方。它的最大用处有两个，**一个是前面提到的可以读取函数内部的变量，另一个就是让这些变量的值始终保持在内存中**。















