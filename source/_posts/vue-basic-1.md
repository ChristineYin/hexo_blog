---
title: [Vue 之 生命周期]
date: 2020-05-28 23:03:22
tags: [Vue, 生命周期]
# index_img: /img/vue-basic-1.jpg
---

## Vue 是什么

Vue 是一门渐进式的 javascript 框架。所谓的渐进式就是：从中心的视图层渲染开始向外扩散的构建工具层。这个过程会经历：视图层渲染-->组件机制-->路由状态-->状态管理-->构建工具，五个层级。

特点：易用、灵活、高效，入门门槛低。

## Vue 的特点、优势

特点：响应式编程、组件化。

优势：

1. 轻量级框架。只关注视图层，是一个构建数据的视图集合，大小只有几十 kb，vue 通过简洁的 API 提供高效的数据绑定和灵活的组件系统。
2. 简单易学。国人开发，中文文档，不存在语言障碍，易于理解和学习。
3. 双向数据绑定。通过 MVVM 思想实现数据的双向绑定，让开发者不再操作 DOM 对象，有更多的时间去思考业务逻辑。
4. 组件化。引入和组件化思想，把一个单页应用中的各种模板拆分到一个一个单独组件中。
5. 视图、数据、结构分离。使数据的更改更为简单，不需要进行逻辑代码的修改，只需要操作数据就能完成相关操作。
6. 虚拟 DOM。
7. 运行速度快。

## Vue 生命周期

### Vue 生命周期过程

Vue 的生命周期可以分为以下八个阶段：beforeCreate、created、beforeMount、mounted、beforeUpdate、updated、beforeDestory、destoryed。

1. 首先从 new Vue() 开始，创建一个 Vue 的实例对象；

2. 执行 init() 方法初始化 Vue 实例，初始化生命周期、初始化事件中心、触发 beforeCreate 钩子函数(这时候还没有 data、methods 中的数据还没初始化，不能使用)、执行 initState 函数，在这个函数中会初始化 props、data、watch、computed、methods，触发 created 钩子函数(如果需要操作 data 数据、或者调用 methods 方法，最早只能在 created 中操作)；

3. 初始化完成后，调用\$mount 方法对 Vue 实例进行挂载，挂载的核心过程包括模板编译、渲染、更新三个过程，如果没有在 Vue 实例上定义 render 方法而是定义了 template，那么需要经历编译阶段，转化成 render function 字符串； 编译完成后触发 beforeMount 钩子函数；然后会实例化一个渲染 Watcher，在它的回调函数中调用 updateComponent 方法(此方法调用 render 方法生成虚拟 Node，最终调用 update 方法更新 DOM)；首次渲染完成之后会触发 mounted 钩子函数；(至此组件已经脱离了创建阶段、进入运行阶段)；

4. 之后当数据发生改变的时候，会执行 diff 算法，比对改变是否需要触发 UI 更新，需要更新的话，会放到异步队列 flushScheleQueue, 在清空队列时先触发 beforeUpdate 钩子函数，(这时候页面显示的数据还是旧的)；然后执行渲染 Watcher 的 run()方法，通知所有依赖项更新 UI，执行完毕触发 updated 钩子函数，代表组件已更新；

5. 当一个组件销毁的时候，先触发 beforeDestory 钩子销毁开始，销毁过程会删除自身及所有子节点、清空所有依赖、解绑监听，操作完成之后，触发 destoryed 钩子；

6. actived 和 deactived(keep-alive)：不销毁、缓存，组件激活与失活。

### 在哪个声明周期内调用异步请求？

可以在钩子函数 created、beforeMount、mouted 中进行调用，因为在这三个钩子函数中，data 已经创建，可以将服务端返回的数据进行赋值，但是比较推荐在 created 钩子函数中调用异步请求，因为：

1. 能更快获取到服务端数据，减少页面 loading 的时间；
2. ssr 不支持 beforeMount、mounted 钩子函数，所以放在 created 中有助于一致性；

### 在什么阶段才能访问操作 DOM?

在钩子函数 mounted 被调用前，Vue 已经将编译好的模板挂载到页面上，所以在 mounted 中可以访问操作 DOM。

### Vue 的父组件和子组件生命周期钩子执行顺序是什么

**1. 加载渲染过程**
父 beforeCreate ---> 父 created ---> 父 beforeMount ---> 子 beforeCreate ---> 子 created ---> 子 beforeMount ---> 子 mounted ---> 父 mounted

**2. 子组件更新过程**
父 beforeUpdate ---> 子 beforeUpdate ---> 子 updated ---> 父 updated

**3. 父组件更新过程**
父 beforeUpdate ---> 父 updated

**4. 销毁过程**
父 beforeDestory ---> 子 beforeDestory ---> 子 destoryed ---> 父 destoryed

### keep-alive

keep-alive 是 Vue 内置的一个组件，可以使被包含的组件保留状态，避免重新渲染，有以下特性：

1. 一般结合路由和动态组件一起使用，用于缓存组件；
2. 提供 include 和 exclude 属性，两者都支持字符串或正则表达式，include 表示只有名称匹配的组件会被缓存，exclude 表示任何名称匹配的组件都不会被缓存，其中 exclude 的优先级比 include 高；
3. 对于两个钩子函数 activated 和 deactivated，当组件被激活时，触发钩子函数 activated，当组件被移出时，触发钩子函数 deactivated。
