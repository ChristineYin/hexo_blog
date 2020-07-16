---
title: 模拟 Webpack plugins 功能
date: 2020-07-16 22:23:25
tags:
  - Webpack
categories: [Webpack]
excerpt: 今天我们就来实现一个简单的 webapck Plugins 功能。
---

```bash
// Compiler.js
const { SyncHook, AsyncSeriesHook } = require('tapable');

module.exports = class Compiler {
    constructor() {
        this.hooks = {
            accelerate: new SyncHook(['newspeed']),
            brake: new SyncHook(),
            calculateRoutes: new AsyncSeriesHook(['source', 'target', 'routesList'])
        }
    }

    run() {
        this.accelerate(10)
        this.brake()
        this.calculateRoutes('Async', 'hook', 'demo')
    }

    accelerate(speed) {
        this.hooks.accelerate.call(speed)
    }

    brake() {
        this.hooks.brake.call()
    }

    calculateRoutes() {
        this.hooks.calculateRoutes.promise(...arguments).then(() => {

        }, err => {
            console.error(err)
        })
    }
}
```

```bash
// my-plugin.js
const Compiler = require('./Compiler');

class MyPlugin {
    constructor() {}

    apply(compiler) {
        compiler.hooks.brake.tap('WarningLampPlugin', () => { console.log('WarningLampPlugin') });
        compiler.hooks.accelerate.tap('LoggerPlugin', newSpeed => console.log(`Accelerating to ${newSpeed}`) );
        compiler.hooks.calculateRoutes.tapPromise('calculateRoutes tapPromise', (source, target, routesList, callback) => {
            console.log('source', source);
        
            // return a promise
            return new Promise((resolve, reject) => {
                setTimeout(() => {
                    console.log(`tapPromise to ${source}${target}${routesList}`);
                    resolve();
                }, 1000);
            })
        })
    }
}


const myPlugin = new MyPlugin();

const options = {
    plugins: [myPlugin]
}

const compiler = new Compiler()

for(const plugin of options.plugins) {
    if (typeof plugin === 'function') {
        plugin.call(compiler, compiler);
    } else {
        plugin.apply(compiler)
    }
} 

compiler.run();
```
