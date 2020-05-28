---
title: Vue 之 基本原理
date: 2020-05-28 23:16:52
tags: [Vue, 响应式原理, Computed原理, Watch原理]
# index_img: /img/vue-principle-2.jpg
---

## 响应式原理

Vue 响应式原理的核心的采用数据劫持与发布订阅模式相结合的方式。
在 Vue 响应式的实现机制中与三个类有关：Observer，Dep，Watcher。以渲染 Watcher 为例：

Observer，Dep：当创建一个 Vue 实例时，会执行初始化操作，其中就会遍历传入 data 的所有属性，并调用 defineReactive 方法，通过 Object.defineProperty 为每个 data 属性都添加 getter、setter，使其编程可响应式对象。另外还会为每个属性生成一个闭包的 Dep 实例，主要作用是在 getter 时用于依赖收集，在 setter 时用于派发更新。

Watcher，Dep：当执行 Vue mount 方法挂载实例时，会实例化一个渲染 Watcher，并将组件更新方法 updateComponent 作为参数传入。在实例化过程中会执行 Watcher 实例的 get 方法，首先将 Dep.target 赋值为当前渲染 Watcher 并压栈，然后会执行它的 getter 方法，其实就是作为参数传入的 update 方法，在 update 执行前会先执行 render 方法生成 VNode，在这个过程中会对 vm 上的数据访问，就会触发数据对象的 getter 方法，将当前渲染 Watcher 作为依赖 push 到该属性所持有的 dep 的 subs 中，并且递归访问 value，触发它所有子项的 getter，最后渲染 Watcher 出栈。

当数据修改时，就会触发 setter 逻辑调用 dep.notify 方法，就是遍历所有的 subs，调用每个 watcher 的 update 方法。对于 Watcher 的不同状态会执行不同的逻辑，在渲染 Wacher 下会调用 queueWatcher()，这里是为了保证并不是每次数据更新就会立即执行 watcher 回调，而是把这些 watcher 先添加到一个队列里，然后在 nextTick 执行。当下一个 tick 执行时就会触发 watcher 的回到，触发组件的重新渲染。

## Computed 原理

computed 是 vue 的计算属性，主要功能是当依赖数据发生改变时重新计算。

计算属性的初始化是发生在 Vue 实例初始化阶段的 initState 函数中，在计算属性的初始化阶段会对 computed 对象做遍历，为每一个函数创建一个 computed watcher，然后判断是否和 data 或者 props 重复，没有重复的话就会为计算属性的 key 值添加 getter、settter。当 render 函数访问到计算属性的时候，就会触发它的 getter，对计算属性进行依赖收集，此时的 Dep.target 时渲染 watcher，所以这里相当于渲染 watcher 订阅了 computed watcher 的变化。然后会执行 watcher.evaluate()去求值，在求值的过程中会触发依赖项的 getter，这时候会把正在计算的 computed watcher 添加到依赖项自身持有的 dep 中，另外这里会有一个 dirty 值，它会根据依赖项是否变化，来控制是否需要重新求值。

当计算属性的依赖数据发生变化，会触发 setter 过程，通知所有订阅它变化的 watcher 更新，执行 watcher.update 方法。这里有两种情况，当没有 watcher 订阅这个 computed watcher 的变化，则会把 dirty 置为 true，当下次访问这个属性的时候再重新求值；当有 watcher 订阅变化时，会先判断一下新旧值是否一样，只有不一样的情况下才会重新渲染。

## Watch 原理

watch 是 vue 的侦听属性，主要功能是当数据变化时执行异步或开销较大的操作。

侦听属性的初始化发生在 Vue 实例初始化阶段的 initState 函数中，在 computed 初始化之后执行。侦听属性的初始化就是对 watch 对象做遍历，调用 vm.\$watch 函数(这个函数时 vue 原型上的方法)，为其创建一个 user watcher。在 user wathcer 初始化的过程中，会将 Dep.target 赋值为当前 user watcher，然后执行 get()，会访问到 key 值，触发它对应的 getter，将该 user watcher 添加到依赖订阅中。

当依赖项发生改变时，会触发 setter 过程，通知所有订阅者，user watcher 会判断值是否一样，不一样的话便执行其回调函数。如果不指定 sync 参数，默认会在下一个 tick 中执行。

## computed 和 watch 的区别及运用场景？

computed：是计算属性，依赖于其它属性值，并且 computed 的值有缓存，只有它依赖的属性值发生改变，下一次获取 computed 的值才会重新计算值；
watch：更多的是观察的作用，类似于某些数据的监听回调，每当监听的数据变化时都会执行回调进行后续操作；

运用场景：

1. 当我们需要进行数值计算，并且依赖于其他数据时，应该使用 computed，因为可以利用 computed 的缓存特性，避免每次获取值时，都要重新计算。
2. 当我们需要在数据变化时执行异步或开销较大的操作时，应该使用 watch，使用 watch 选项允许我们执行异步操作，限制我们我们执行该操作的频率，并在我们得到最终结果前，设置中间状态。这些都是计算属性无法做到的。
