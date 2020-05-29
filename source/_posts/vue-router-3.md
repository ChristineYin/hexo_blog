---
title: Vue Router
date: 2020-05-29 14:01:03
excerpt: 这是一段文章摘要，是通过 Front-Matter 的 excerpt 属性设置的。
---

## 基本分类

vue-router 提供的导航守卫(路由守卫)主要用来通过跳转或取消的方式守卫导航。有多种机会植入路由导航过程中：全局的、单个路由独享的、或者组件级的。

<!-- more -->

### 全局守卫
1. router.beforeEach，全局前置守卫，进入路由之前
2. router.beforeResolve，全局解析守卫，在beforeRouteEnter调用之后调用
3. router.afterEach，全局后置钩子，进入路由之后

### 路由独享守卫
1. beforeEnter，为某些路由单独配置守卫

### 路由组件内守卫
1. beforeRouteEnter，进入路由前。在渲染该组件的对应路由被confirm前被调用，不能获取组件实例this，因为当守卫执行前，组件实例还没被创建。
2. beforeRouterUpdate，路由复用同一个组件时。在当前路由改变，但是该组件被复用时调用，举例来说，对于一个带有动态参数的路径 /foo/:id，在/foo/1 和 /foo/2 之间跳转的时候，由于会渲染同样的Foo组件，因此组件实例会被复用。这个钩子函数就会被调用。
3. beforeRouterLeave，离开当前路由时。导航离开该组件的对应路由时被调用。

### 导航解析流程
1. 导航被触发。
2. 在失活的组件里调用离开守卫。
3. 调用全局的 beforeEach 守卫。
4. 在重用的组件里调用 beforeRouteUpdate 守卫。
5. 在路由配置里调用 beforeEnter。
6. 解析异步路由组件。
7. 在被激活的组件里调用 beforeRouteEnter。
8. 调用全局的 beforeResolve 守卫。
9. 导航被确认。
10. 调用全局的 afterEach 钩子。
11. 触发 DOM 更新。
12. 用创建好的实例调用 beforeRouteEnter 守卫中传给next的回调函数。


## Vue Router 路由模式

vue-router有3种路由模式：hash、historty、abstract，对应的源码如下：

```bash
switch (mode) {
  case 'history':
  this.history = new HTML5History(this, options.base)
  break
  case 'hash':
  this.history = new HashHistory(this, options.base, this.fallback)
  break
  case 'abstract':
  this.history = new AbstractHistory(this, options.base)
  break
  default:
  if (process.env.NODE_ENV !== 'production') {
    assert(false, `invalid mode: ${mode}`)
  }
}
```

1. hash：使用URL hash值来做路由。支持所有浏览器，包括不支持 HTML5 History Api 的浏览器；
2. history：依赖 HTML5 History Api 和服务器配置。
3. abstract：支持所有 JavaScript 运行环境，如 Node.js 服务端。如果发现没有浏览器的 API，路由会自动强制进入这个模式。

### hash模式

早期的前端路由的实现就是基于 lacation.hash 来实现的。其实现原理很简单，location.hash 的值就是 URL 中#后面的内容。hash 路由模式的实现主要是基于下面几个特性：

- URL中hash值只是客户端的一种状态，也就是说当向服务端发出请求时，hash 部分不会被发送；
- hash值的改变，会在浏览器的访问历史中增加一个记录。因此我们能通过浏览器的回退、前进按钮控制hash的切换；
- 可以通过 a 标签，并设置 href 属性，当用户点击这个标签后，URL 的 hash 值会发生改变；或者使用 JavaScript 来对location.hash 进行赋值，改变 URL 的 hash 值；
- 我们可以使用 hashchange 事件来监听 hash 值的变化，从而对页面进行跳转

### history模式

HTML5 提供了 History API 来实现URL的变化。其中最主要的API有两个：history.pushState() 和 history.replaceState()。这两个API可以在不进行刷新的情况下，操作浏览器的历史记录。唯一不同的时，前者是新增一个历史记录。后者是直接替换当前的历史记录。

window.history.pushState(null, null, path);
window.history.replaceState(null, null, path);

- pushState 和 replaceState 两个API来操作实现URL的变化；
- 我们可以使用 popstate 事件来监听 url 的变化，从而对页面进行跳转
- history.pushState 或 history.replaceState 不会触发 popstate 事件，这时我们需要手动触发页面跳转

## Vue 路由懒加载

路由组件的懒加载是结合 Vue 的异步组件和 Webpack 的代码分割功能实现的。Vue 的异步组件返回一个 Promise 的工厂函数，第一次的时候会渲染成一个注释节点，等到路径匹配，真正渲染的时候触发回调函数，ajax 请求组件内容，因为数据没有改变，所以请求完之后会执行 forceRender 强制执行一遍渲染。异步组件的本质就是两次渲染。

1. 首先，可以将异步组件定义为返回一个Promise的工厂函数（该函数返回的Promise应该resolve组件本身）;

2. 第二，在Webpack 2中，我们可以使用动态import语法来定义代码分块点（split point）;

```bash
// 第一步
const Foo = () => Promise.resolve({ /* 组件定义对象 */ })

// 第二步
import('./Foo.vue') // 返回 Promise

// 结合两者，定义一个能够被Webpack自动代码分割的异步组件
const Foo = () => import('./Foo.vue')
```

3. 可以把某个路由下的所有组件都打包在同一个异步块(chunk)中。只需要使用命名 chunk，一个特殊的注释语法来提供 chunk name(webpack > 2.4); 

```bash
const Foo = () => import(/* webpackChunkName: "group-foo" */ './Foo.vue')
const Bar = () => import(/* webpackChunkName: "group-foo" */ './Bar.vue')
const Baz = () => import(/* webpackChunkName: "group-foo" */ './Baz.vue')
```

webpack会将任何一个异步模块与相同的块名称组合到相同的异步块中。


## 动态组件和异步组件

### 动态组件

动态组件的实现是通过 Vue 的 <component> 元素加一个特殊的 is attribute 来实现。 currentTabComponent 可以包括已注册组件的名字或一个组件的选项对象。

```bash
<!-- 组件会在 `currentTabComponent` 改变时改变 -->
<component v-bind:is="currentTabComponent"></component>
```

当在这些组件之间切换的时候，有时可能需要保持这些组件的状态，以避免反复重渲染导致的性能问题。这时候就可以使用 <keep-alive> 元素将其动态组件包裹起来，使得组件实例能在它们第一次被创建的时候缓存下来。

### 异步组件

异步组件实现的本质是2次渲染，除了0delay的高级异步组件第一次直接渲染成loading组件外，其它都是第一次渲染生成一个注释节点，当异步获取组件成功后，再通过 forceRender 强制重新渲染，这样就能正确渲染出我们异步加载的组件了。

在大型应用中，我们可能需要将应用分割成小一些的代码块，并且只在需要的时候才从服务器加载一个模块。为了简化，vue允许你以一个工厂函数的方式定义你的组件，这个工厂函数会异步解析你的组件定义。vue只有在这个组件需要被渲染的时候才会触发该工厂函数，且会把结果缓存起来供未来重渲染。

```bash
Vue.component('async-example', function (resolve, reject) {
  setTimeout(function () {
    // 向 `resolve` 回调传递组件定义
    resolve({
      template: '<div>I am async!</div>'
    })
  }, 1000)
})
```

这个工厂函数会收到一个resolve回调，这个回调函数会在你从服务器得到组件定义的时候被调用。也可以在工厂函数中返回一个Promise，配合webpack和ES2015语法使用。例如

```bash
// 全局注册
Vue.component(
  'async-webpack-example',
  // 这个 `import` 函数会返回一个 `Promise` 对象。
  () => import('./my-async-component')
)

// 局部注册
new Vue({
  // ...
  components: {
    'my-component': () => import('./my-async-component')
  }
})

// 路由懒加载
{path: '/recordsList', component: () => import('views/recordsList'), name: 'recordsList'}
```

**全局注册和局部注册**

```bash
// 全局注册
Vue.component('my-component-name', { /* ... */ })

// 局部注册
import ComponentA from './ComponentA.vue'

export default {
  components: {
    ComponentA
  }
}

```