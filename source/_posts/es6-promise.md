---
title: ES6 之 Promise常见面试题
date: 2020-05-28 21:48:16
tags:
  - ES6
  - Promise
categories:
  - [WebSocket]
excerpt: 本篇主要记录一些常见的 Promise 面试题
---

## 问题 1：限制 Promise 并发数量

```bash
// 使用方式
// 一般请求
const request = require('./request')
request.get('http://baidu.com')
  .then(res => {})
  .catch(err => {})
```

```bash
// 使用限制请求
const request = require('./request')
const LimitPromise = require('./limit-promise')
const MAX = 10;
cosnt limitP = new LimitPromise(MAX);
function get (url, params) {
  return limitP.call(request.get, url, params)
}
function post (url, params) {
  return limitP.call(request.post, url, params)
}
module.exports = {get, post}
```

```bash
// 实现方式
function LimitPromise (max) {
  this._max = max;
  this._count = 0;
  this._taskQueue = [];
}

LimitPromise.prototype.call = function (caller, ...args) {
  return new Promise((resolve, reject) => {
    var task = this._createTask(caller, args, resolve, reject)
    this._count > this.max ? this._taskQueue.push(task) : task()
  })
}

LimitPromise.prototype._createTask = function (caller, args, resolve, reject) {
  return () => {
    caller(args)
    .then(resolve)
    .catch(reject)
    .finally(() => {
      this._count--;
      if (this._taskQueue.length > 0) {
        var task = this._taskQueue.shift()
        task()
        this._count++;
      }
    })
  })
}

module.exports = LimitPromise;

https://www.jianshu.com/p/cc706239c7ef
```

## 问题 2：async/await 代替 promise.all

```bash
// 使用方式
function doJob (x, sec) {
  if (!x) throw new Error('test error')
  return new Promise(resolve => {
    setTimeout(() => {
      resolve(x)
    }, sec * 100)
  })
}
```

```bash
(async function (){
  try{
    const all1 = await asyncAlls([doJob(1,1), doJob(2,1), doJob(0,1), doJob(4,1)])
    console.log('all1:', all1)
  }catch(error){
    console.log('1:', error)
  }
  try{
    const all2 = await asyncAlls([doJob(5,1), doJob(6,1), doJob(7,1), doJob(8,1)])
    console.log('all2:', all2)
  }catch(error){
    console.log('1:', error)
  }
})()

// 实现方式
async function asyncAlls (jobs) {
  try{
    let result = job.map(job => job);
    let res = []
    for(const result of results) {
      res.push(await result)
    }
    return res
  }catch(error){
    throw new Error(error)
  }
}

https://z-index.me/2019/08/17/Async-Await
```

## 问题 3：如何取消 Promise

**方法一：promise.race 方法**

```bash
function wrap (p) {
  let obj = {};
  let p1 = new Promise((resolve, reject) => {
    obj.resolve = resolve;
    obj.reject = reject;
  });
  obj.promise = Promise.race([p1, p]);
  return obj;
}

let promise = new Promise((resolve, reject) => {
  setTimeout(() => {
    resolve(123);
  }, 1000);
});

let obj = wrap(promise);

obj.promise.then(res => {
  console.log(res);
});

obj.resolve("请求被拦截了");

obj.reject("请求被拒绝了");
```

**方法二： 新包装一个可操控的 promise**

```bash
function wrap (p) {
  let res = null;
  let abort = null;

  let p1 = new Promise((resolve, reject) => {
    res = resolve;
    abort = reject;
  });

  p1.abort = abort;
  p.then(res, abort);

  return p1;
}

let promise = new Promise((resolve, reject) => {
  setTimeout(() => {
    resolve(123);
  }, 1000);
});

let obj = wrap(promise);

obj.then(res => {
  console.log(res);
});

obj.abort("请求被拦截");
```

## 问题 4：Promise 输出题 1

```bash
console.log(1)

setTimeout(()=>{console.log(2)},1000)

async function fn(){
    console.log(3)
    setTimeout(()=>{console.log(4)},20)
    return Promise.reject()
}

async function run(){
    console.log(5)
    await fn()
    console.log(6)
}

run()

//需要执行150ms左右
for(let i = 0; i < 90000000; i++){}

setTimeout(()=>{
    console.log(7)
    new Promise(resolve=>{
        console.log(8)
        resolve()
    }).then(()=>{console.log(9)})
},0)

console.log(10)
```

## 问题 4：Promise 输出题 2

```bash
console.log(1);

setTimeout(() => {
  console.log(2);
  new Promise((resolve,reject) => {
    console.log(3);
    resolve()
  }).then(res => {
    console.log(4);
  })
})

new Promise((resolve,reject) => {
  resolve()
}).then(res => {
  console.log(5);
}).then(res => {
  console.log(6);
})

new Promise((resolve,reject) => {
  console.log(7);
  resolve()
}).then(res => {
  console.log(8);
}).then(res => {
  console.log(9);
})

setTimeout(() => {
  console.log(10);
  new Promise((resolve,reject) => {
    console.log(11);
    resolve()
  }).then(res => {
    console.log(12);
  })
})

console.log(13);
```
