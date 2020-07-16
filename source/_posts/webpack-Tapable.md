---
title: Webpack 中 Tapable 的简单使用
date: 2020-07-16 21:30:28
tags:
  - Webpack
categories: [Webpack]
excerpt: 项目中使用 webpack 打包，应该是个普遍的事情，那么关于 webpack 打包是怎么实现的呢？接下来就简单认识下 Tapable。
---

## Tapable

`webpack` 可以将其理解为一种基于事件流的编程范例，一系列的插件运行。其核心对象均是继承自 `Tapable`。

```bash
// 核心对象 Compiler 继承 Tapable
class Compiler extends Tapable {...}

// 核心对象 Compilation 继承 Tapable
class Compilation extends Tapable {...}
```

## Tapable 是什么呢
`Tapable` 是一个类似于 `Node.js` 的 `EventEmitter` 的库，主要是控制钩子函数的发布与订阅，控制着 `webpack` 的插件系统。
`Tapable` 库暴露了很多 `Hook` 钩子类，为插件提供挂载的钩子。

```bash
const {
    SyncHook,                 //同步钩子
    SyncBailHook,             //同步熔断钩子
    SyncWaterfallHook,        //同步流水钩子
    SyncLoopHook,             //同步循环钩子
    AsyncParallelHook,        //异步并发钩子
    AsyncParallelBailHook,    //异步并发熔断钩子
    AsyncSeriesHook,          //异步串行钩子
   AsyncSeriesBailHook,       //异步串行熔断钩子
   AsyncSerierWaterfallHook   // 异步串行流水钩子
} = require("tapable");
```

`Tapable hooks` 的类型如下：
1. **Hook**：所有钩子的后缀
2. **Waterfall**：同步方法，但是它会传值给下一个函数
3. **Bail**：熔断，当函数有任何返回值，就会在当前执行函数停止
4. **Loop**：监听函数返回 true 表示继续循环，返回 undefined 表示结束循环
5. **Sync**：同步方法
6. **AsyncSeries**：异步串行钩子
7. **AsyncParallel**：异步并行执行钩子

## Tapable 的使用
`Tapable` 暴露出来的都是类方法，`new` 一个类方法获得我们需要的钩子。
`class` 接受数组参数 `options`，非必传。类方法会根据传参，接受同样数量的参数。

```bash
const hook1 = new SyncHook(['arg1','arg2','arg3'])
```

`Tapable` 提供了同步、异步绑定钩子的方法，并且他们都有绑定事件和执行事件对应的方法。
1. Async
   1. 绑定：tapAsync / tapPromise / tap
   2. 执行：callAsync / promise
2. Sync
   1. 绑定：tap
   2. 执行：call

**hook 基本用法示例**
```bash
// index.js
const { SyncHook } = require('tapable');

const hook = new SyncHook(['arg1','arg2','arg3']);

// 绑定事件到 webpack 事件流
hook.tap('hook1', (arg1, arg2, arg3) => {
    console.log(arg1, arg2, arg3)
})

// 执行绑定的事件
hook.call(1,2,3)
```

再写一个稍微复杂点的例子
```bash
// car.js
const { SyncHook, AsyncSeriesHook } = require('tapable');

class Car {
    constructor() {
        this.hooks = {
            accelerate: new SyncHook(['newspeed']),
            brake: new SyncHook(),
            calculateRoutes: new AsyncSeriesHook(['source', 'target', 'routesList'])
        }
    }
}

const myCar = new Car()

// 绑定同步钩子
myCar.hooks.brake.tap('WarningLampPlugin', () => { console.log('WarningLampPlugin') })

// 绑定同步钩子 并传参
myCar.hooks.accelerate.tap('LoggerPlugin', newSpeed => console.log(`Accelerating to ${newSpeed}`) )

// 绑定一个异步 Promise 钩子
myCar.hooks.calculateRoutes.tapPromise('calculateRoutes tapPromise', (source, target, routesList, callback) => {
    console.log('source', source);

    // return a promise
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            console.log(`tapPromise to ${source}${target}${routesList}`);
            resolve();
        }, 1000);
    })
})


myCar.hooks.brake.call();
myCar.hooks.accelerate.call(10);

console.time('cost');

myCar.hooks.calculateRoutes.promise('Async', 'hook', 'demo').then(() => {
    console.timeEnd('cost')
}, err => {
    console.error(err);
    console.timeEnd('cost')
})
```

