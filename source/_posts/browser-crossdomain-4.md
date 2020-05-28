---
title: 浏览器 之 跨域
tags: [浏览器, 跨域]
# index_img: /img/browser-crossdomain-4.jpeg
date: 2020-05-28 21:49:16
---

## 假如没有安全策略

假设页面中没有安全策略的话，Web 世界会是什么样子呢？

Web 世界会是开放的，任何资源都可以接入其中，我们的网站可以加载并执行别人网站的脚本文件、图片、音频、视频等资源，甚至可以下载其他站点的可执行文件。页面行为将没有任何限制，这会造成无序或者混沌的局面，出现很多不可控的问题。比如你打开了一个银行站点，然后又不小心打开了一个恶意站点，如果没有安全措施，恶意站点就可以做很多事情：

1. 修改银行站点的 DOM、CSS 等信息；
2. 在银行站点内部插入 JavaScript 脚本；
3. 劫持用户登录的用户名和密码；
4. 读取银行站点的 Cookie、IndexDB 等数据；
5. 甚至还可以将这些信息上传至自己的服务器，这样就可以在你不知道情的情况下唉伪造一些转账请求等信息；

所以说，在没有安全保障的 Web 世界中，我们是没有隐私的，因此需要安全策略来保障我们的隐私和数据安全。这就是页面中最基础、最核心的安全策略 ---- 同源策略(Same-origin policy)。

如果两个 URL 协议、域名和端口号都相同，我们就称这两个 URL 同源。浏览器默认两个相同的源之间是可以相互访问资源和操作 DOM 的；两个不同的源之间若想要相互访问资源或者操作 DOM，那么会有一套基础的安全策略的制约，我们把这称为同源策略。同源策略主要表现在 DOM、Web 数据和网络这三个层面。

1. DOM 层面。同源限制了来自不同源的 JavaScript 脚本对当前 DOM 对象的读写操作。
2. 数据层面。同源限制了只能读取当前站点的 Cookie、IndexDB、LocalStorage 等数据。
3. 网络层面。同源限制了通过 XMLHttpRequest 等方式将站点的数据发送给不同源的站点。

浏览器通过同源策略实现了 Web 页面的安全性，不过安全性和便利性是相互对立的，让不同源之间绝对隔离无疑是最安全的措施，但这会使得 web 项目难以开发和使用。因此需要做出权衡，出让一些安全性来满足灵活性，出让安全性又会带来很多安全问题，最典型的就是 XSS 攻击和 CSRF 攻击。这个平衡点，也就是目前页面安全策略原型：

1. 页面中可以引用第三方资源，不过这也暴露了很多诸如 XSS 的安全问题，因此又在页面开放的基础上引入了 CSP 来限制其自由程度。
2. 使用 XMLHttpRequest 和 Fetch 都是无法直接进行跨域请求的，因此浏览器又在这话总严格策略的基础之上引入了跨域资源共享策略，让其可以安全的进行跨域操作。
3. 两个不同源的 DOM 是不能相互操作的，因此浏览器又实现了跨文档消息机制，让其可以比较安全地通信。

## 跨域解决方式

### 1. Node 中间件代理

通过代理服务器 express 向目标服务器发送请求数据。实现原理：同源策略是浏览器需要遵循的标准，而如果是服务器向服务器请求就无需遵循同源策略。代理服务器，需要做以下几个步骤：

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

## JSONP

web 前端事先定义一个用于获取跨域响应数据的回调函数，并通过没有同源策略限制的 script 标签发起一个请求(将回调函数的名称放到这个请求的 query 参数里)，然后服务端返回这个回调函数的执行，并将需要响应的数据放到回调函数的参数里，前端的 script 标签请求到这个执行的回调函数后会立马执行，于是就拿到了执行的响应数据。

JSONP 和 CORS 的对比：

- JSONP 只支持 GET 请求，CORS 支持所有类型的 HTTP 请求
- JSONP 的优势在于支持老式浏览器，以及可以向不支持 CORS 的网站请求数据
