---
title: JS 是如和影响 DOM 的
date: 2020-06-09 17:32:28
tags:
  - JS
categories: [JS]
excerpt: JS 是如何影响 DOM 的构建过程的呢？
---

我们先来看一个简单的例子，如下：

```bash
<html>
<body>
    <div>1</div>
    <div>test</div>
</body>
</html>
```

我们知道 HTML 解析器经过词法分析生成一个 DOM 结构，然后通过语法分析构建出一个 DOM 树，因为这个例子中没有 JS 脚本，所以会顺序执行，没有什么特殊的地方，解析结果可以参考下图：

![t5RL6J.png](https://s1.ax1x.com/2020/06/09/t5RL6J.png)

那么如果在 HTML 文档中插入一段 JS 脚本，这个解析流程就会发生变化呢？

```bash
<html>
<body>
    <div>1</div>

    <script>
        let div1 = document.getElementsByTagName('div')[0]
        div1.innerText = 'yinchunxia'
    </script>

    <div>test</div>
</body>
</html>
```

在遇到 script 标签之前解析流程和之前还是一样的，但是解析到 script 标签时，渲染引擎判断这是一段脚本，HTML 解析器便会暂停 DOM 的解析，交由 JS 引擎处理执行该段脚本，这是因为 JS 可能会修改当前已经生成的 DOM 结构，所以先执行 JS，当 JS 引擎执行完 JS，又交还给渲染引擎，继续 HTML 解析器的工作，直到 DOM 树生成完成。

除了内联 JS 脚本外，我们通常会使用引入 JS 文件的方式，那么接下来我们看看它的解析过程又是怎样的。

```bash
// index.js
let div1 = document.getElementsByTagName('div')[0]
div1.innerText = 'yinchunxia'
```

```bash
<html>
<body>
    <div>1</div>

    <script type="text/javascript" src='index.js'></script>

    <div>test</div>
</body>
</html>
```

和上面那段代码不同的只是 js 的方式，在遇到 script 标签之前还是和之前相同没有变化，但是以 js 文件的方式的话，还需要先下载然后再执行代码。通常下载文件是非常耗时的，可能会受网络环境和文件大小等的影响，那么这时候生成 DOM 树的时间又会增长。

不过我们通常对于这方面也会做一些优化，比如使用 CDN 加速缩短 js 文件的下载时间，压缩 js 文件的体积或者拆分成更小的 js 文件。另外如果没有操作 DOM 相关代码的话，可以通过 async 或 defer 将 js 文件的下载设置为异步，不过这两者也是有区别的，使用 async 标记的 js 文件一旦加载完成就会立即执行；而使用了 defer 标记的文件，会在 DOMContentLloaded 事件之前执行。

```bash
<script async type="text/javascript" src='index.js'></script>
或
<script defer type="text/javascript" src='index.js'></script>
```

之前的几种情况中我们都没有涉及到 css 相关操作，下面我们再来看看引入了 css 文件的执行流程：

```bash
<html>
<head>
    <style src='theme.css'></style>
</head>

<body>
    <div>1</div>

    <script>
        let div1 = document.getElementsByTagName('div')[0]
        div1.innerText = 'time.geekbang' // 需要 DOM
        div1.style.color = 'red'  // 需要 CSSOM
    </script>

    <div>test</div>
</body>
</html>
```

在这个例子中，我们引入了 css 文件，并在 js 脚本中对 css 进行了修改，所以我们在执行 css 的修改之前，还需要先下载 css 文件，并解析生成 CSSOM 对象后，才能执行。但是 JS 引擎在解析 JS 之前，不能确定是否会对 css 进行修改，所以渲染引擎的统一处理方式就是，在遇到 js 脚本时不管有没有修改，都会先执行 css 文件的下载解析操作，再执行 js 脚本。所以我们由此可知，js 脚本还会依赖样式表。
