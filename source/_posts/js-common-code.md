---
title: 常见手写原生代码
date: 2020-05-27 22:15:17
tags:
  - JS
categories:
  - [JS]
excerpt: 常见手写原生代码
---

## 函数柯里化

函数柯里化是把接受多个参数的函数变换成接受一个单一参数（最初函数的第一个参数）的函数，并且返回接受余下的参数而且返回结果的新函数技术。

**1. 函数柯里化的主要作用:**

- 参数复用
- 提前返回 - 返回接受余下的参数且返回结果的新函数
- 延迟执行 - 返回新函数，等待执行

**2. 函数柯里化的使用**

```bash
function sum (a, b, c) {
    return a + b + c;
}
```

以上代码如果用函数柯里化的方式实现，就变成以下形式

```bash
function sum (a) {
  return function(b) {
      return function(c) {
          return a + b + c;
      }
  }
}
console.log(sum(1)(2)(3));   // 6
```

**3. 实现柯里化函数**

实现一个 curry 函数，可以将所有普通函数进行柯里化

```bash
function curry (fn, args = []) {
    return function (){
        let rest = [...args, ...arguments];
        if (rest.length < fn.length) {
            return curry.call(this,fn,rest);
        } else{
            return fn.apply(this,rest);
        }
    }
}
```

如果将上面的 sum 函数进行柯里化，便可以直接作为参数传入 curry 函数中

```bash
let sumFn = curry(sum);
console.log(sumFn(1)(2)(3));   // 6
console.log(sumFn(1)(2, 3));   // 6
```

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

## 实现 instanceof

**1. 使用方式**

```bash
a instance Object
```

**2. 实现方法**

```bash
function myInstanceof (target, origin) {
  const proto = target.__proto__;
  if (proto) {
    if (origin.prototype === proto) {
      return true;
    } else {
      return myInstanceof(proto, origin)
    }
  } else {
    return false;
  }
}
```

## 类型判断

```bash
var class2type = {}

// 生成映射
'Boolean Number String Function Array Date RegExp Object Error Null Undefined'
  .split(' ')
  .map(function (item, index) {
    class2type['[object ' + item + ']'] = item.toLowerCase();
  })

function type (obj) {
  if (obj == null) {
    return obj + '';
  }
  return typeof obj === 'object' || typeof obj === 'function'
    ? class2type[Object.prototype.toString.call(obj)] || 'object'
    : typeof obj;
}

function isFunction (obj) {
  return type(obj) === 'function';
}

var isArray =
  Array.isArray ||
  function (obj) {
    return type(obj) === 'array';
  }
```

## 防抖

**1. 原理**
如果在一个事件触发的 n 秒内又触发了这个事件，以新的事件时间为准，n 秒后才执行。总之，就是要等到触发完事件 n 秒内不再触发事件。

**2. 使用方式**

```bash
var count = 1;
var container = document.getElementById('container');
function getUserAction() {
  console.log(this)
  container.innerHTML = count++;
};
container.onmousemove = getUserAction;
```

**3. 实现方法**

```bash
function debounce (func, wait) {
  var timeout;

  return function () {
    var context = this;
    var args = arguments;

    clearTimeout(timeout);
    timeout = setTimeout(function () {
      func.apply(context, args)
    }, wait)
  }
}
```

值得注意几点：

- 返回一个函数
- this 指向问题
- event 事件获取

## 节流

**1. 原理**
如果持续触发事件，每隔一段时间，只执行一次事件。两种主流的节流的实现方式：时间戳，和定时器

**2. 时间戳实现**

```bash
function throttle (func, wait) {
  var context, args;
  var previous = 0;

  return function () {
    var now = +new Date();
    context = this;
    args = arguments;

    if (now - previous > wait) {
      func.apply(context, args);
      previous = now;
    }
  }
}
```

**3. 定时器实现**

```bash
function throttle1 (func, wait) {
  var context, args, timeout;
  var previous = 0;

  return function () {
    context = this;
    args = arguments;

    if (!timeout) {
      timeout = setTimeout (function () {
        timeout = null;
        func.apply(context, args);
      }, wait)
    }
  }
}
```

**4. 防抖和节流比较**

1. 时间戳事件会立即执行，定时器事件会在 n 秒后第一次执行
2. 时间戳事件停止触发后没有办法再执行事件，定时器事件停止触发后依然会再执行一次事件

**5. 应用场景**

防抖

- 每次 resize/scroll 触发统计事件
- 文本输入的验证（连续输入文字后发送 Ajax 请求进行验证，验证一次就好）

节流

- DOM 元素的拖拽功能实现 mousemove
- 射击游戏的 mousedown/keydown 事件，单位时间内只能发射一颗子弹
- 计算鼠标移动的距离，mousemove
- 搜索联想 keyup
- 监听滚动事件判断是否到页面底部自动加载更多：给 scroll 加了 debounce 后，只有用户停止滚动后，才会判断是否到了页面底部；如果 throttle 的话。只要页面滚动就会间隔一段事件判断一次

## 浅拷贝 和 深拷贝

**浅拷贝**

1. 手动实现

```bash
const shallowClone = (target) => {
  if (typeof target === 'object' && target !== null) {
    const cloneTarget = Array.isArray(target) ? []: {};
    for (let prop in target) {
      if (target.hasOwnProperty(prop)) {
          cloneTarget[prop] = target[prop];
      }
    }
    return cloneTarget;
  } else {
    return target;
  }
}
```

2. Object.assign 浅拷贝

```bash
let obj = { name: 'sy', age: 18 };
const obj2 = Object.assign({}, obj, {name: 'sss'});
console.log(obj2);//{ name: 'sss', age: 18 }
```

3. concat 浅拷贝

```bash
let arr = [1, 2, 3];
let newArr = arr.concat();
newArr[1] = 100;
console.log(arr);//[ 1, 2, 3 ]
```

4. slice 浅拷贝

```bash
let arr = [1, 2, {val: 4}];
let newArr = arr.slice();
newArr[2].val = 1000;
```

5. ...展开运算符

```bash
let arr = [1, 2, 3];
let newArr = [...arr];   //跟arr.slice()是一样的效果
```

**深拷贝**

```bash
JSON.parse(JSON.stringify());
```

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

## EventEmitter

Node.js 的 EventEmitter 是观察者模式的典型实现，Nodejs 的 events 模块只提供了一个对象：event.EventEmitter。它的核心就是事件触发与事件监听器功能的封装。

```bash
function EventEmitter() {
  this._maxListeners = 10;
  this._events = Object.create(null);
}

// 向事件队列添加事件
// prepend为true表示向事件队列头部添加事件
EventEmitter.prototype.on = function (type, listener, prepend) { //addListener
  if (!this._events) { // 保证存在实例属性
    this._events = Object.create(null);
  }
  if (this._events[type]) {
    if (prepend) { // 从头部插入
      this._events[type].unshift(listener);
    } else {
      this._events[type].push(listener);
    }
  } else {
    this._events[type] = [listener];
  }
};

// 移除某个事件
EventEmitter.prototype.off = function (type, listener) { // removeListener
  if (Array.isArray(this._events[type])) {
    if (!listener) {
      delete this._events[type]
    } else {
      // 过滤掉退订的方法，从数组中移除
      this._events[type] = this._events[type].filter(e => e !== listener && e.origin !== listener)
    }
  }
};

// 向事件队列添加事件，只执行一次
EventEmitter.prototype.once = function (type, listener) {
  // 中间函数，在调用完之后立即删除订阅
  const only = (...args) => {
    listener.apply(this, args);
    this.off(type, listener);
  }
  only.origin = listener;
  this.on(type, only);
};

// 执行某类事件
EventEmitter.prototype.emit = function (type, ...args) {
  if (Array.isArray(this._events[type])) {
    this._events[type].forEach(fn => {
      fn.apply(this, args);
    });
  }
};

// 设置最大事件监听个数
EventEmitter.prototype.setMaxListeners = function (count) {
  this.maxListeners = count;
};
```

## 单例模式

```bash
// 单例模式抽象
var getSingle = function(fn) {
  var result;
  return function() {
    return result || (result = fn.apply(this, arguments))
  }
}


// 单例模式实例
var Singleton = function (name){
  this.name = name;
}
Singleton.prototype.getName = function (){
  alert(this.name)
}
Singleton.getInstance = (function(name){
  var instance;
  return function (name){
    if(!instance){
      instance = new Singleton(name)
    }
    return instance;
  }
})()
var a = Singleton.getInstance('ConardLi1')
var b = Singleton.getInstance('ConardLi2')
```

## 图片懒加载

```bash
var img = document.getElementsByTagName("img");
var n = 0; //存储图片加载到的位置，避免每次都从第一张图片开始遍历
lazyload(); //页面载入完毕加载可是区域内的图片

// 节流函数，保证每200ms触发一次
function throttle(event, time) {
  let timer = null;
  return function (...args) {
    if (!timer) {
      timer = setTimeout(() => {
        timer = null;
        event.apply(this, args);
      }, time);
    }
  }
}

window.addEventListener('scroll', throttle(lazyload, 200))

function lazyload() { //监听页面滚动事件
  var seeHeight = window.innerHeight; //可见区域高度
  var scrollTop = document.documentElement.scrollTop || document.body.scrollTop; //滚动条距离顶部高度
  for (var i = n; i < img.length; i++) {
    console.log(img[i].offsetTop, seeHeight, scrollTop);
    if (img[i].offsetTop < seeHeight + scrollTop) {
      if (img[i].getAttribute("src") == "loading.gif") {
        img[i].src = img[i].getAttribute("data-src");
      }
      n = i + 1;
    }
  }
}
```

## 实现 JSONP

```bash
(function (window,document) {
    "use strict";
    var jsonp = function (url,data,callback) {

        // 1.将传入的data数据转化为url字符串形式
        // {id:1,name:'jack'} => id=1&name=jack
        var dataString = url.indexof('?') == -1? '?': '&';
        for(var key in data){
            dataString += key + '=' + data[key] + '&';
        };

        // 2 处理url中的回调函数
        // cbFuncName回调函数的名字 ：my_json_cb_名字的前缀 + 随机数（把小数点去掉）
        var cbFuncName = 'my_json_cb_' + Math.random().toString().replace('.','');
        dataString += 'callback=' + cbFuncName;

        // 3.创建一个script标签并插入到页面中
        var scriptEle = document.createElement('script');
        scriptEle.src = url + dataString;

        // 4.挂载回调函数
        window[cbFuncName] = function (data) {
            callback(data);
            // 处理完回调函数的数据之后，删除jsonp的script标签
            document.body.removeChild(scriptEle);
        }

        document.body.appendChild(scriptEle);
    }

    window.$jsonp = jsonp;

})(window,document)
```
