---
title: JS 之 call、apply、bind
date: 2020-05-28 21:46:16
tags:
  - JS
  - call
  - apply
  - bind
categories:
  - [JS]
excerpt: call、apply、bind
---

## 模拟实现 call 方法

**1. call 方法的基本使用方式**

```bash
var person = {
  fullName: function (city, country) {
    return this.firstName + " " + this.lastName + "," + city + "," + country;
  }
}
var person1 = {
  firstName:"Bill",
  lastName: "Gates"
}
person.fullName.call(person1, "Seattle", "USA");
```

**2. 模拟实现 call**

call 的实现有几个注意点：

- call 方法的调用者必须是一个函数
- 调用者使用的是 this，所以将调用者（方法）赋值到对象的方法上
- eval 函数接受的是字符串
- context.fn 函数的参数从 i=1 开始计数

```bash
Function.prototype.call = function (context) {
  context = context || window;
  context.fn = this;

  var args = [];
  for (var i = 1, len = arguments.length; i < len; i++) {
    args.push('arguments[' + i + ']')
  }

  var result = eval('context.fn(' + args + ')');

  delete context.fn;
  return result;
}
```

## 模拟实现 apply 方法

**1. apply 方法的基本使用方式**

```bash
var person = {
  fullName: function(city, country) {
    return this.firstName + " " + this.lastName + "," + city + "," + country;
  }
}
var person1 = {
  firstName:"John",
  lastName: "Doe"
}
person.fullName.apply(person1, ["Oslo", "Norway"]);
```

**2. 模拟实现 apply**

```bash
Function.prototype.apply = function (context, arr) {
  var context = context || window;
  context.fn = this;

  var result;
  if (!arr) {
    result = context.fn();
  } else {
    var args = [];
    for (var i = 0, len = arr.length; i < len; i++) {
      args.push('arr[' + i + ']');
    }
    result = eval('content.fn(' + args + ')');
  }

  delete context.fn;
  return result
}
```

## 模拟实现 bind 方法

**1. bind 方法的基本使用方式**

bind 方法创建一个新的函数，在 bind() 被调用时，这个新函数的 this 被指定为 bind() 的第一个参数，而其余参数作为新函数的参数，供调用时使用。

实例一：

- 返回一个函数
- 可以传参，bind 和执行新函数时均支持传参

```bash
var foo = {
  value: 1
}
function bar (name, age) {
  console.log(this.value)
  console.log(name)
  console.log(age)
}
var bindFoo = bar.bind(foo, 'aaa')
bindFoo('18')   // 1 aaa 18
```

一个绑定函数也能使用 new 操作符创建对象：这种行为就像把原函数当做构造函数。提供的 this 值被忽略，同时调用时的参数被提供给模拟函数。

实例二：

```bash
var value = 2;
var foo = {
    value: 1
};
function bar (name, age) {
    this.habit = 'shopping';
    console.log(this.value);
    console.log(name);
    console.log(age);
}
bar.prototype.friend = 'kevin';

var bindFoo = bar.bind(foo, 'daisy');
var obj = new bindFoo('18');   // undefined daisy 18
console.log(obj.habit);   // shopping
console.log(obj.friend);   // kevin
```

**2. 模拟实现 bind**

```bash
Function.prototype.bind = function (context) {
  if (typeof this !== 'function') {
    throw new Error('Function.prototype.bind - what is trying to be bound is not callable');
  }

  var self = this;
  var args = Array.prototype.slice.call(arguments, 1)

  var fNOP = function () {}

  var fBound = function () {
    var bindArgs = Array.prototype.slice.call(arguments);
    return self.apply(this instanceof fNOP ? this : context, args.concat(bindArgs))
  }

  fNOP.prototype = this.prototype;
  fBound.prototype = new fNOP()
  return fBound;
}
```
