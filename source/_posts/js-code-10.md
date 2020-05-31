---
title: JS 之 单例模式
date: 2020-05-28 21:46:16
tags:
  - JS
  - 单例模式
categories:
  - [JS]
excerpt: 单例模式
---

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
