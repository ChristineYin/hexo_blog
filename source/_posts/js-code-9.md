---
title: JS 之 EventEmitter
tags: [JS, EventEmitter]
index_img: https://ss2.bdstatic.com/70cFvnSh_Q1YnxGkpoWK1HF6hhy/it/u=2050318681,1081448419&fm=26&gp=0.jpg
date: 2020-05-28 21:46:16
---

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