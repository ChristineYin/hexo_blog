---
title: JS 之 创建对象的多种方式以及优缺点
tags: [JS, 对象]
index_img: https://ss2.bdstatic.com/70cFvnSh_Q1YnxGkpoWK1HF6hhy/it/u=2050318681,1081448419&fm=26&gp=0.jpg
date: 2020-05-28 21:46:16
---

## 创建对象的多种方式以及优缺点

**1. 工厂模式**

```bash
function createPerson (name){
  var o = new Object()
  o.name = name;
  o.getName = function(){
    console.log(this.name)
  }
  return o;
}
var person1 = createPerson('kevin')
```

优点：解决了创建多个相似对象的问题
缺点：对象无法识别,，即怎样知道一个对象的类型

**2. 构造函数模式**

```bash
function Person (name){
  this.name = name;
  this.getName = function(){
    console.log(this.name)
  }
}
var person1 = new Person('aaa')
```

优点：实例可以识别为一个特定的类型
缺点：每次创建实例时，每个方法都要被创建一次

**3. 原型模式**

```bash
function Person (){}
Person.prototype.name = 'aaa';
Person.prototype.getName = function (){
  console.log(this.name)
}
var person1 = new Person()
```

优点：方法不会重新创建
缺点：1.所有属性和方法都共享 2.不能初始化参数

**4. 组合模式：构造函数模式与原型模式合并**

```bash
function Person (name){
  this.name = name;
}
Person.prototype.getName = function (){
  constructor: Person,
  getName: function (){
    console.log(this.name)
  }
}
var person1 = new Person()
```

优点：该共享的共享，该私有的私有，使用最广泛的方式
缺点：有的人希望全部都写在一起，即更好的封装性

**5. 动态原型模式**

```bash
function Person (name){
  this.name = name;
  if(typeof this.getName != 'function'){
    Person.prototype.getName = function (){
      console.log(this.name)
    }
  }
}
var person1 = new Person();
```

注意: 使用动态原型模式时，不能用对象字面量重写原型

**6. 寄生构造函数模式**

```bash
function Person (name){
  var o = new Object()
  o.name = name;
  o.getName = function (){
    console.log(this.name)
  }
  return o;
}
var person1 = new Person('kevin');
console.log(person1 instanceof Person)   // false
console.log(person1 instanceof Object)    // true
```

所谓的寄生构造函数模式就是比工厂模式在创建对象的时候，多使用了一个 new，实际上两者的结果是一样的。
打着构造函数的幌子，创建的实例使用 instanceif 都无法指向构造函数

**7. 稳妥构造函数模式**

```bash
function person (name){
  var o = new Object()
  o.sayName = function (){
    console.log(name)
  }
  return o;
}
var person1 = person('kevin');
person1.sayName();   // kevin
person1.name = "daisy";
person1.sayName();   // kevin
console.log(person1.name);   // daisy
```

所谓稳妥构造函数，指的是没有公共属性，而且其方法也不引用 this 的对象。与工厂模式一样，无法识别对象所属类型

与寄生构造函数模式不同的是：

1. 新创建的实例方法不引用 this
2. 不使用 new 操作符调用构造函数。