---
title: 变量提升你理解对了吗？
date: 2020-06-03 15:09:23
tags:
  - 变量提升
excerpt: 从概念上来说，"变量提升"意味着变量和函数的声明会在物理层面移动到代码的最前面，但这么说并不准确。实际上变量和函数声明在代码里的位置是不会动的，而是在编译阶段放入内存中。
---

我们知道在 JavaScript 中，变量和函数可以在声明之前是可以正常使用的，如下面这段代码：

```bash
console.log(myName)
showMyName()
var myName = 'yinchunxia';
function showMyName () {
  console.log('showMyName 函数被执行！')
}
```

这段代码是可以正常执行的，打印的结果为`undefinded`和`showMyName 函数被执行`。但原因是什么呢？为什么变量和函数可以在定义之前使用？为什么函数和变量打印出来的信息不同呢，函数提前执行可以打印出完整的结果，而变量打印出来的是`undefined`呢？

## 声明和初始化

在解决这个问题之前，我们先了解一下 JavaScript 中的声明和初始化操作。

**变量**

我们可以通过 `var` 声明语句声明一个变量，并可选地将其初始化为一个值，默认值为 `undefined`。如这条语句`var myName = 'yinchunxia';`，我们认为它是由变量声明`var myName;`和初始化`myName = 'yinchunxia'`两部分组成的，另外因为变量声明的默认值为 `undefined`，所以变量声明也可以写为`var myName = undefinded`。

**函数**

函数有两种创建方式，函数声明和函数表达式。

```bash
// 函数声明
function showMyName () { ... }

// 函数表达式
var showMyName = function() { ... }
```

对于函数声明，它被看做一个不可分割的整体，而函数表达式则不然，它的本质是变量的声明，会被看做由变量声明`var showMyName = undefined;`和`showMyName = function() {}`两部分组成。


了解了基本概念，我们来解释一下刚才的问题，这是因为在 JavaScript 中，JS 引擎会把变量声明和函数声明部分提升到代码开头的"行为"，变量被提升后，会给变量设置默认值`undefined`。所以你先使用的变量，再声明并初始化它，变量的值将是 `undefined`。另外，函数和变量相比，会被优先提升，这意味着函数会被提升到更靠前的位置。所以原代码提升后可以看做是这样执行的：

```bash
// 原始代码
console.log(myName)
showMyName()
var myName = 'yinchunxia';
function showMyName () {
  console.log('showMyName 函数被执行！')
}

// 提升之后
var myName = undefined;
function showMyName () {
  console.log('showMyName 函数被执行！')
}
console.log(myName)
showMyName()
myName = 'yinchunxia'
```

## 变量提升和编译

从概念上来说，"变量提升"意味着变量和函数的声明会在物理层面移动到代码的最前面，但这么说并不准确。实际上变量和函数声明在代码里的位置是不会动的，而是在编译阶段放入内存中。当一段代码被 JS 引擎执行的时候，包括两个阶段**编译**和**执行**，变量提升其实就是发生在编译过程。

接下来我们一行行分析：

```bash
console.log(myName)
showMyName()
var myName = 'yinchunxia';
function showMyName () {
  console.log('showMyName 函数被执行！')
}
```

**编译**

首先 JS 引擎会执行编译阶段，具体流程如下：

- 第1行和第2行，由于这两航不是声明操作，所以 JavaScript 引擎不会做任何处理；
- 第3行，由于这行是经过 `var` 声明的，因此 JS 引擎会在活动对象中创建一个名为 `myName` 的属性，并使用 undefined 对其初始化；
- 第4行，JS 引擎发现一个通过 `function` 定义的函数，所以它将函数定义存储到堆中，并在环境变量中创建一个 `showMyName` 的属性，然后将该属性指向堆中函数的位置。
- 预处理完所有的代码便生成了变量对象，接下来 JS 引擎会把声明之外的代码都编译为字节码。对于生成的字节码，比较晦涩难懂，暂时按源代码来理解执行过程。
  
```bash
console.log(myName)
showMyName()
myName = 'yinchunxia'
```

**执行**

代码编译完成之后，会由解释器逐行解释执行生成字节码。

- 首先打印`myName`信息， JS 引擎便在活动对象中查找该对象，由于活动对象中存在该变量，其值为`undefined`，所以输出`undefinded`；
- 接下来执行`showMyName`函数，JS 引擎还是去对象中查找该函数，活动对象中存在该函数的引用，所以 JS 引擎执行，并输出 "showMyName 函数被执行！"；
- 最后是一个赋值操作，将 `yinchunxia` 赋值给 `myName` 变量，赋值后活动对象中的 `myName` 属性值便修改为`yinchunxia`。

## 相同的变量或者函数

说了这么多，也基本上有了大体的认识，接下来看一段代码：

```bash
function Foo() {
  getName = function () {
    console.log(1);
  };
  return this;
}
Foo.getName = function () {
  console.log(2);
};
Foo.prototype.getName = function () {
  console.log(3);
};
var getName = function () {
  console.log(4);
};
function getName() {
  console.log(5);
}

Foo.getName();
getName();
Foo().getName();
getName();
```

我们来分析一下其完整的执行流程：
- **首先是编译阶段**。 
  - JS 引擎遇到了 `Foo` 函数声明，会将该函数体存放到活动对象中；
  - 接下来遇到 `getName` 的函数表达式，引擎会把变量 `getName` 也存进去，其值为undefined；
  - 然后遇到`getName`函数声明，到这里因为活动对象中已经存在了`getName`属性，按理说重复的声明会被忽略，但是函数声明优先变量声明，所以会覆盖掉之前声明的`var getName = undefined`；
  - 其他的非声明代码被 JS 引擎编译为字节码。
- **接下来是执行阶段**。
  - 首先为`Foo.getName`分配堆内存，然后在Foo函数上添加属性`getName`，并赋值为堆地址；
  - 接下同样的在`Foo`的原型上添加属性`getName`，并赋值为引用；
  - 然后执行`var getName = function(){...}`，因为在编译阶段已经执行了变量声明，所以这里只需要执行赋值操作，将该函数覆盖掉之前声明的 `getName` 函数；
  - 执行`Foo.getName`函数，输出`2`;
  - 执行`getName`函数，输出`4`;
  - 执行`Foo().getName()`，这一步会先执行`Foo`函数，在`Foo`函数中执行赋值操作`getName=function(){...}`，在 JavaScript 中，没有使用`var` 声明的变量，默认为全局对象，所以该操作会覆盖之前的`getName`函数。接下里返回`this`，`this`的指向主要是根据函数的调用方式决定的，所以这里的`this`指的是全局对象`window`，由于所有全局对象都是`window`的属性，所以输出`1`；
  - 因为上步操作中`getName`被重新赋值，所以输出`1`。

## 总结

对于变量提升这篇文章就到这里，下面做下总结：
1. 我们平时习惯将 `var myName = 'yinchunxia';` 看做是一个声明，其实 JS 引擎并不是这么认为的，它会将 `var myName` 和 `myName = 'yinchunxia'` 分开，一个是变量声明，是编译阶段的任务；一个是初始化，是执行阶段的任务；
2. 另外就是，我们应该避免重复声明，特别是当变量声明`var`和函数声明混合在一起的时候，会引起许多不必要的问题。