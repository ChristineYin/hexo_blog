---
title: JS 之 防抖和节流
tags: [JS, 防抖, 节流]
# index_img: https://ss2.bdstatic.com/70cFvnSh_Q1YnxGkpoWK1HF6hhy/it/u=2050318681,1081448419&fm=26&gp=0.jpg
date: 2020-05-28 21:46:16
---

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
