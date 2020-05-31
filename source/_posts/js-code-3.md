---
title: JS 之 new运算符
date: 2020-05-28 21:46:16
tags:
  - JS
  - new
categories:
  - [JS]
excerpt: new
---

## 模拟实现 new 运算符

**1. new 运算符的使用方式**

```bash
// 使用方式
function Otaku (name, age) {
  this.name = name;
  this.age = age;
  this.habit = 'Games'
}
Otaku.prototype.strength = 60;
Otaku.prototype.sayYourName = function() {
  console.log('I am ' + this.name)
}

var person = new Otaku('aaa', 18)
person.name // aaa
person.habit // Games
person.strength // 60
person.sayYourName() // I am aaa
```

由以上使用方式可以看出，person 实例可以：

- 访问到 Otaku 构造函数里的属性
- 访问到 Otaku.prototype 中的属性
- 返回一个对象
- 返回对象判断是否是基本类型

**2. 模拟实现 new 运算符**

模拟 new 运算符的一些步骤如下：

- 新生成一个对象
- 链接到原型：obj.**proto** = Con.prototype
- 绑定 this：apply
- 返回新对象（如果构造函数有自己 return 时，则返回该值）

```bash
function objectFactory() {
  var obj = new Object();
  var Construct = [].shift.call(arguments);
  obj.__proto__ = Construct.prototype;
  var ret = Construct.apply(obj, arguments);
  return ret === 'object' ? ret : obj;
}
```
