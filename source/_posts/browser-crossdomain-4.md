---
title: 浏览器为什么要设置跨域
date: 2020-06-11 15:20:16
tags:
  - 浏览器
  - 跨域
categories:
  - [跨域]
excerpt: 我们经常会遇到跨域的问题，归其原因是浏览器的同源策略导致的，但为什么浏览器要设置同源策略呢？假如页面中没有安全策略的话，Web 世界会是什么样子呢？
---

我们经常会遇到跨域的问题，归其原因是浏览器的同源策略导致的，但为什么浏览器要设置同源策略呢？假如页面中没有安全策略的话，Web 世界会是什么样子呢？

如果没有这些安全策略的话，那么 Web 世界将会是开放的，任何资源都可以接入其中，我们的网站可以加载并执行别人网站的脚本文件、图片、音频、视频等资源，甚至可以下载其他站点的可执行文件。页面行为将没有任何限制，这会造成无序或者混沌的局面，出现很多不可控的问题。比如你打开了一个银行站点，然后又不小心打开了一个恶意站点，如果没有安全措施，恶意站点就可以做很多事情：

1. 修改银行站点的 DOM、CSS 等信息；
2. 在银行站点内部插入 JavaScript 脚本；
3. 劫持用户登录的用户名和密码；
4. 读取银行站点的 Cookie、IndexDB 等数据；
5. 甚至还可以将这些信息上传至自己的服务器，这样就可以在你不知道情的情况下唉伪造一些转账请求等信息；

所以说，在没有安全保障的 Web 世界中，我们是没有隐私的，因此我们是需要安全策略来保障我们的隐私和数据安全。这就是页面中最基础、最核心的安全策略 --- 同源策略（Same-origin policy）。

## 同源策略

那么什么是同源策略呢？同源策略规定：不同域的客户端脚本在没有明确授权的情况下，不能读写对方的资源。

**同域**
同域或者说同源，要求两个地址 URL 具有相同的协议、域名、端口，我们举几个例子和 `http://www.foo.com` 比较看是否同域：

| 地址 URL                | 是否同域 | 原因                                      |
| ----------------------- | -------- | ----------------------------------------- |
| https://www.foo.com     | 不同域   | 协议不同，https 和 http 是不同的协议      |
| http://a.foo.com        | 不同域   | 域名不同，a 和 www 子域不同               |
| http://foo.com          | 不同域   | 域名不同，顶级域名和 www 子域不是一个概念 |
| http://www.foo.com:8080 | 不同域   | 端口不用，8080 和默认的 80 端口不同       |
| http://www.foo.com/a/   | 同域     | 满足协议、域名、端口相同                  |

**客户端脚本**
客户端脚本我们平时主要用的就是 JavaScript

**授权**
看到授权，我们一般会想到服务端对客户端访问的授权，客户端自己也存在授权现象。比如 HTML5 标准中关于 AJAX 跨域访问的情况，默认是不允许访问的，只有当目标站点（http://www.bar.com）明确返回 HTTP 响应头 `Access-Control-Allow-Orgin: http://www.foo.com`，那么 `http://www.foo.com` 站点上的 JS 脚本就有权通过 Ajax 技术对 `http://www.bar.com` 上数据进行读写操作。

**读写权限**
Web 上的资源有很多，有的只能可读，有的则可以读写。比如：HTTP 请求头中的 `Referer` 只可读，而 `document.cookie` 则既可读也可写。

同源策略实现的安全性主要表现在 DOM、Web 数据和网络这三个层面。

1. DOM 层面。同源限制了来自不同源的 JavaScript 脚本对当前 DOM 对象的读写操作。
2. 数据层面。同源限制了只能读取当前站点的 Cookie、IndexDB、LocalStorage 等数据。
3. 网络层面。同源限制了通过 XMLHttpRequest 等方式将站点的数据发送给不同源的站点。

<!--
浏览器通过同源策略实现了 Web 页面的安全性，不过安全性和便利性是相互对立的，让不同源之间绝对隔离无疑是最安全的措施，但这会使得 web 项目难以开发和使用。因此需要做出权衡，出让一些安全性来满足灵活性，出让安全性又会带来很多安全问题，最典型的就是 XSS 攻击和 CSRF 攻击。这个平衡点，也就是目前页面安全策略原型：
1. 页面中可以引用第三方资源，不过这也暴露了很多诸如 XSS 的安全问题，因此又在页面开放的基础上引入了 CSP 来限制其自由程度。
2. 使用 XMLHttpRequest 和 Fetch 都是无法直接进行跨域请求的，因此浏览器又在这话总严格策略的基础之上引入了跨域资源共享策略，让其可以安全的进行跨域操作。
3. 两个不同源的 DOM 是不能相互操作的，因此浏览器又实现了跨文档消息机制，让其可以比较安全地通信。 -->

## 跨域解决方式

根据上面的内容，我们了解到造成跨域的原因主要是浏览器为了安全所设置的同源策略。那么我们主要来说说跨域请求，当浏览器向目标 URI 发送 Ajax 请求的时候，只要当前 URL 和目标 URL 不同源，便发生了跨域。但其实跨域的请求并不是没有发送出去，也不是没有响应，而是响应返回后被浏览器拦截了。因为当浏览器主进程检查到跨域，并且没有 CORS 响应头，就会将响应体全部丢掉，并不会让网络进程发给渲染进程，从而达到拦截数据的目的。

接下来我们就来说说解决跨域问题有那些方案。

### 1. Node 中间件代理

通过代理服务器 express 向目标服务器发送请求数据。实现原理是，同源策略是浏览器需要遵循的标准，而如果是服务器向服务器请求就无需遵循同源策略。通过代理服务器，需要做以下几个步骤：

1. 接受客户端请求
2. 将请求转发给服务器
3. 拿到服务器响应数据
4. 将响应转发给客户端

### 2. CORS（跨域资源共享）

CORS (跨域资源共享 Cross-origin resource sharing) 允许浏览器向跨域服务器发出 XMLHttpRequest 请求，从而克服跨域问题，但它需要浏览器和服务器的同时支持。整个 CORS 通信过程，都是浏览器自动完成的。浏览器一旦发现 AJAX 请求跨源，就会自动添加一些附加信息，有时还会多出一次附加请求，但用户不会有感觉。实现 CORS 通信的关键是服务器，只要服务器实现了就可以跨源通信。
浏览器将 CORS 请求分为两类：简单请求和非简单请求。

**简单请求**

简单请求的需要同时满足两大条件：

1. 请求方法是以下三种之一：HEAD、GET、POST；
2. HTTP 的头信息不超过以下几个字段：Accept、Accept-Language、Content-Language、Last-Event-ID、Content-Type：只限于三个值 application/x-www-form-urlencoded、multipart/form-data、text/plain；

请求流程：

1. 浏览器发现跨源自动在头信息中增加一个 Origin 字段；
2. 服务器根据 Origin 判断是否同意这次请求；
3. 不同意。服务器返回正常的 HTTP 回应，但是头信息中不包含 Access-Control-Allow-Origin 字段，然后浏览器就知道出错了，从而抛出一个错误，被 XMLHttpRequest 的 onerror 回调函数捕获。
4. 同意。在许可范围内，服务器返回的相应就会多出几个头信息字段：
   1. Access-Control-Allow-Origin：必须的。它的值要么是请求时 Origin 字段的值，要么是一个\*，表示接受任意域名的请求；
   2. Access-Control-Allow-Credentials：可选。值是布尔值，表示是否允许发送 Cookie；
   3. Access-Control-Expose-Headers：可选。CORS 请求时，XMLHttpRequest 对象的 getResponseHeader()方法如果想拿到 6 个基本字段之外的，就必须在这个字段中指定；

**非简单请求**

非简单请求是那种对服务器有特殊要求的请求，比如请求方法是 PUT 或 DELETE，或者 Content-Type 字段的类型是 application/json。非简单请求的 CORS 请求，会在正式通信前增加一次"预检"请求。浏览器先询问服务器，当前网页所在的域名是否在服务器的许可名单之中，以及可以使用哪些 HTTP 动词和头信息字段。只有得到肯定答复，浏览器才会发出正式的 XMLHttpRequest 请求，否则报错。

请求流程：

**1. 预检请求：**

1. 预检请求用的方法是 OPTINS，表明这个请求是来询问的。
2. 头信息里包含有：
3. Origin：必须。来自哪个源
4. Access-Control-Request-Method：必须。列出浏览器的 CORS 请求会用到哪些 HTTP 方法。
5. Access-Control-Request-Headers：指定会额外发送的头信息字段。
6. 服务器回应：
7. 不同意。同简单请求。
8. 同意。头信息包含字段有：
   1. Access-Control-Allow-Origin：必须。它的值要么是请求时 Origin 字段的值，要么是一个 \*，表示接受任意域名的请求；
   2. Access-Control-Allow-Methods：必须。表明支持的所有跨域请求的方法；
   3. Access-Control-Allow-Header：与 Access-Control-Request-Headers 对应，请求有回应必须有；
   4. Access-Control-Allow-Credentials：可选。值是布尔值，表示是否允许发送 Cookie；
   5. Access-Control-Max-Age：可选。指定本次预检请求的有效期。

**2. 正常请求：**

与简单请求一样。请求有 Origin 字段，回应有 Access-Control-Allow-Origin 字段。且都是必须的

### JSONP

虽然 `XMLHttpRequest` 对象遵循同源策略，但是 `script` 标签并不需要遵循，它可以通过 `src` 属性设置为目标地址从而达到 `GET` 请求的目的，实现跨域并获得响应数据。我们可以模拟实现：

```bash
const jsonp = ({ url, params, callbackName }) => {
  const generateURL = () => {
    let dataStr = '';
    for(let key in params) {
      dataStr += `${key}=${params[key]}&`;
    }
    dataStr += `callback=${callbackName}`;
    return `${url}?${dataStr}`;
  };
  return new Promise((resolve, reject) => {
    // 初始化回调函数名称
    callbackName = callbackName || Math.random().toString.replace(',', '');
    // 创建 script 元素并加入到当前文档中
    let scriptEle = document.createElement('script');
    scriptEle.src = generateURL();
    document.body.appendChild(scriptEle);
    // 绑定到 window 上，为了后面调用
    window[callbackName] = (data) => {
      resolve(data);
      // script 执行完了，成为无用元素，需要清除
      document.body.removeChild(scriptEle);
    }
  });
}
```

同时，在服务端也增加响应的操作：

```bash
let express = require('express')
let app = express()
app.get('/', function(req, res) {
  let { a, b, callback } = req.query
  console.log(a); // 1
  console.log(b); // 2
  // 注意哦，返回给script标签，浏览器直接把这部分字符串执行
  res.end(`${callback}('数据包')`);
})
app.listen(3000)
```

然后就可以这样简单的使用：

```bash
jsonp({
  url: 'http://localhost:3000',
  params: {
    a: 1,
    b: 2
  }
}).then(data => {
  // 拿到数据进行处理
  console.log(data); // 数据包
})
```

JSONP 和 CORS 的对比：

- JSONP 只支持 GET 请求，CORS 支持所有类型的 HTTP 请求
- JSONP 的优势在于支持老式浏览器，以及可以向不支持 CORS 的网站请求数据

<!-- web 前端事先定义一个用于获取跨域响应数据的回调函数，并通过没有同源策略限制的 script 标签发起一个请求(将回调函数的名称放到这个请求的 query 参数里)，然后服务端返回这个回调函数的执行，并将需要响应的数据放到回调函数的参数里，前端的 script 标签请求到这个执行的回调函数后会立马执行，于是就拿到了执行的响应数据。 -->

### Nginx

`Nginx` 是一个高性能的 HTTP 和反向代理的服务器。

**反向代理和正向代理**

正向代理，就类似一个跳板机，代理访问外部资源。比如在国内想要访问谷歌，直接访问是访问不到的，这时我们就可以通过一个代理服务器去请求，代理服务器拿到数据后再返回给我们。这个代理服务器就是正向代理服务器。

![tXqhgf.png](https://s1.ax1x.com/2020/06/13/tXqhgf.png)

正向代理服务器通常可以访问原来无法访问的资源或者做缓存、加速资源的访问等等。

反向代理，其实是以代理服务器接收客户端的请求，然后将请求转发给实际要请求的服务器，并将服务器返回的响应转发给客户端。此时的代理服务器就是反向代理服务器。

![tXqIKS.png](https://s1.ax1x.com/2020/06/13/tXqIKS.png)

反向代理通常用来保证内网的的安全、阻止 Web 攻击，也可以实现负载均衡来优化服务器的负载。

一句话总结就是，正向代理即代理客户端，反向代理即代理服务端。

了解了反向代理，我们就来看看 Nginx 是怎么通过它解决跨域问题的。假如客户端域名 `client.com`，服务端域名 `server.com`，客户端直接向服务端发送 Ajax 请求肯定会跨域，但是通过配置 Ngnix：

```bash
server {
  listen  80;
  server_name  client.com;
  location /api {
    proxy_pass server.com;
  }
}
```

这样就可以实现请求了，因为配置后相当于 Nginx 的域名是 `client.com`，发送请求时首先让客户端访问 `client.com/api`，这肯定没有跨域，然后 Ngnix 服务器再把消息转发给 `serevr.com`，这也不会有跨域。服务器返回响应时，再将数据转发给客户端，就完成了跨域请求的过程。

### WebSocket

还有一种方式就是通过 WebSocket 实现，作为了解简单说下原理，WebSocket 是 HTML5 的一个持久化的协议，利用 webSocket 的 API，可以直接 new 一个 socket 实例，然后通过 open 方法内 send 要传输到后台的值，也可以利用 message 方法接收后台传来的数据。后台是通过 new WebSocket.Server({ port: 3000 })实例，利用 message 接收数据，利用 send 向客户端发送数据。

```bash
<!DOCTYPE html>
 <html>
 <head>
  <title></title>
 </head>
 <body>
    # 高级api  不兼容  但是有一个socket.io这个库，是兼容的(一般用这个)

   <script type="text/javascript">
    let socket = new WebSocket("ws://localhost:3000");//ws协议是webSocket自己创造的
    socket.onopen = function(){
        socket.send("hello world");
    }
    socket.onmessage = function(e){
        console.log(e.data); // hello world
    }
   </script>
 </body>
 </html>
```

然后，再起一个服务端

```bash
/*
  要使用ws协议，那么就要装一个ws的包
 */
let express = require("express");
let app = express();
let WebSocket = require("ws");
let wss = new WebSocket.Server({port:3000});

wss.on("connection",function (ws){ // 先连接
ws.on("message",function (data){ // 用message来监听客户端发来的消息
    console.log(data); // hello world
        ws.send(data);
    })
})
```
