---
title: Promise 实现原理
tags: [ES6, Promise]
index_img: https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1590597668049&di=4ec4d98c9b746a51da7200fe309f4745&imgtype=0&src=http%3A%2F%2Fimage.fundebug.com%2F2018-11-29-promise.png
date: 2020-05-27 21:46:16
---

本篇文章主要用于记录学习 Promise 原理的过程，逐步实现一个 Promise。

<!-- more -->

## Promise 基本结构

```bash
new Promise((resolve, reject) => {
    setTimeout(() => {
        resolve('FULFILLED')
    }, 1000)
})
```

构造函数 Promise 必须接收一个函数作为参数，我们称该函数为 executor，executor 又包含 resolve 和 reject 两个参数，它们是两个函数。
