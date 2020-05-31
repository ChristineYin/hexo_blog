---
title: 浏览器 之 WebSocket
date: 2020-05-28 22:36:42
tags:
  - WebSocket
  - Web Worker
  - Servise Worker
categories:
  - [WebSocket]
excerpt: 由于 Http 存在一个明显的缺点(半双工通信)，消息只能由客户端推送到服务端，而不能服务端主打推送到客户端。如果服务器有连续的变化，为了实现这种推送技术只能使用轮询，而轮询效率过低，并不适合。于是 WebSocket 被发明出来，来完善双工通信的更好实现方式。它是属于应用层的协议、基于 TCP 传输、并复用 HTTP 的握手通道。
---

## WebSocket

由于 Http 存在一个明显的缺点(半双工通信)，消息只能由客户端推送到服务端，而不能服务端主打推送到客户端。如果服务器有连续的变化，为了实现这种推送技术只能使用轮询，而轮询效率过低，并不适合。于是 WebSocket 被发明出来，来完善双工通信的更好实现方式。它是属于应用层的协议、基于 TCP 传输、并复用 HTTP 的握手通道。

相比于 HTTP 具有以下不同：

1. 支持双向通信，实时性更强；
2. 更好的二进制支持；
3. 较少的控制开销。连接创建后，ws 客户端、服务端进行数据交换时，协议控制的数据包头部较小。在不包含头部的情况下，服务端到客户端的包头只有 2~10 字节(取决于数据包的长度)，客户端到服务端的化，需要加上额外的 4 字节掩码，而 HTTP 协议每次通信都需要携带完整的头部；
4. 支持扩展。ws 协议定义了扩展，用户可以扩展协议，或者实现自定义的子协议（比如支持自定义压缩算法等）

```bash
var ws = new WebSocket("wss://echo.websocket.org");

ws.onopen = function(evt) {
  console.log("Connection open ...");
  ws.send("Hello WebSockets!");
};

ws.onmessage = function(evt) {
  console.log( "Received Message: " + evt.data);
  ws.close();
};

ws.onclose = function(evt) {
  console.log("Connection closed.");
};
```

## Web Worker

JS 语言采用的是单线程模型，所有任务只能在一个线程上完成，一次只能做一件事。前面的任务没做完，后面的任务只能等着。随着电脑计算能力的增强，尤其是多核 CPU 的出现，单线程无法发挥计算机的计算能力。

Web Worker 的作用，就是为 JS 创造多线程环境，允许主线程创建 Worker 线程，将一些任务分配给后者运行。在主线程运行的同时，Worker 线程在后台运行，两者互不打扰。等到 Worker 线程完成计算任务，再把结果返回给主线程。这样的好处是，一些计算密集型或高延迟的任务，被 Worker 线程负担了，主线程(通常负责 UI 交互)就会很顺畅，不会被阻塞。

Worker 线程一旦新建成功，就会始终运行，不会被主线程上的活动(比如用户点击按钮、提交表单)打断。这样有利于随时响应主线程的通信。这也造成了 Worker 比较耗费资源，不应该过度使用，而且一旦使用完毕，应该关闭。Web Worker 有以下注意点：

1. 同源策略。分配给 Worker 线程运行的脚本文件，必须与主线程的脚本文件同源。
2. DOM 限制。Worker 线程无法读取主线程所在网页的 DOM 对象，但是可以使用 navigator 和 location 对象。
3. 通信联系。Worker 线程和主线程不在同一个上下文环境，他们不能直接通信，必须通过消息完成。
4. 脚本限制。Worker 不能执行 alert、confirm 方法，但可以使用 XMLHttpRequest 对象发出 Ajax 请求。
5. 文件限制。Worker 线程无法读取本地文件，它所加载的脚本必须来自网络。

## Servive Worker

Service workers 本质上充当 Web 应用程序与浏览器之间的代理服务器，也可以在网络可用时作为浏览器和网络间的代理，它们旨在能够创建有效的离线体验。SW 是由事件驱动的，具有生命周期，可以拦截处理页面的所有网络请求，可以访问 cache 和 indexDB，支持推送，并且可以让开发者自己控制管理缓存的内容以及版本，未离线弱环境下的 web 的运行提供了可能，让 web 的体验更贴近 native。
