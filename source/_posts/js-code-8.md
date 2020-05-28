---
title: JS 之 继承
tags: [JS, 继承]
index_img: https://ss2.bdstatic.com/70cFvnSh_Q1YnxGkpoWK1HF6hhy/it/u=2050318681,1081448419&fm=26&gp=0.jpg
date: 2020-05-28 21:46:16
---

## 继承

**1. 原型链继承**

```bash
function Parent (){
  this.name = 'aaa';
}
Parent.prototype.getName = function (){
  console.log(this.name)
}
function Child (){}
Child.prototype = new Parent()
var child1 = new Child()
child1.getName()   // aaa
```

问题：1.引用类型的属性被所有实例共享 2.在创建 Child 实例时，不能向 Parent 传参

**2. 借用构造函数继承**

```bash
function Parent (name){
  this.name = name
}
function Child (name){
  Parent.call(this, name)
}
var child1 = new Child('aaa')
console.log(child1.name)   // aaa
var child2 = new Child('bbb')
console.log(child2.name)   // bbb
```

优点：1.避免了引用类型的属性被所有实例共享 2.可以在 Child 中向 Parent 传参
缺点：方法都在构造函数中定义，每次创建实例都会创建一遍方法

**3. 组合继承：原型链继承和借用构造函数继承**

```bash
function Parent (name){
  this.name = name;
  this.colors = ['red','blue','green']
}
Parent.prototype.getName = function (){
  console.log(this.name)
}
function Child (name, age){
  Parent.call(this, name)
  this.age = age
}
Child.prototype = new Parent()
Child.prototype.constructor = Child

var child1 = new Child('aaa', 18)
child1.colors.push('black')
console.log(child1.name) // aaa
console.log(child1.age) // 18
console.log(child1.colors) // ["red", "blue", "green", "black"]

var child2 = new Child('bbb', 20)
console.log(child2.name) // bbb
console.log(child2.age) // 20
console.log(child2.colors) // ["red", "blue", "green"]
```

优点：融合原型链继承和构造函数的优点，是最常用的继承模式

**4. 原型式继承**

```bash
function createObj (o){
  function F(){}
  F.prototype = o;
  return new F();
}
var person = {
  name: 'aaa',
  frineds: ['xiaohua', 'xiaoming']
}
var person1 = createObj(person)
var person2 = createObj(person)
person1.name = 'person1';
person1.friends.push('xiaowang')
console.log(person2.name) // aaa
console.log(person2.friends) // ['xiaohua', 'xiaoming', 'xiaowang']
```

就是 ES5 Object.create 的模拟实现，将传入的对象作为创建的对象的原型

**5. 寄生式继承**

```bash
function createObj (o){
  var clone = Object.create(o)
  clone.sayName = function(){
    console.log('hi')
  }
  return clone;
}
```

缺点：和借用构造函数模式一样，每次创建对象都会创建一遍方法

**6. 寄生组合式继承**

```bash
function Parent (name){
  this.name = name;
  this.colors = ['red', 'blue', 'green']
}
Parent.prototype.getName = function (){
  console.log(this.name)
}
function Child (name, age){
  Parent.call(this, name)
  this.age = age;
}
function object (o){
  function F (){}
  F.prototype = o
  return new F()
}
function prototype  (child, parent){
  var prototype = object(parent.prototype)
  prototype.constructor = child
  child.prototype = prototype
}
prototype(Child, Parent)
```

优点：只调用一次 Parent 构造函数，避免了在 Parent.prototype 上面创建不必要的、多余的属性。同时，原型链能保持不变；因此是引用类型最理想的继承范式

**7. ES6 Class**

```bash
class Parent {
  constructor(name,age){
    this.name = name;
    this.age = age;
  }
  speakSometing (){
    console.log('I can speak chinese')
  }
}
class Child extends Parent{
  constructor(name, age){
    super(name, age)
    this.job = 'IT'
  }
  coding (){
    console.log('coding javascript')
  }
  getJob (){
      return this.job;
  }
}
```