---
title: JS 之 单例模式
tags: [JS, 单例模式]
index_img: https://ss2.bdstatic.com/70cFvnSh_Q1YnxGkpoWK1HF6hhy/it/u=2050318681,1081448419&fm=26&gp=0.jpg
date: 2020-05-28 21:46:16
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