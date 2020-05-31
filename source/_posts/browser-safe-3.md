---
title: 浏览器 之 安全
date: 2020-05-28 21:49:16
tags:
  - 浏览器
  - 安全
  - XSS
  - CSRF
categories:
  - [安全]
excerpt: 常见的浏览器攻击可以分为 XSS 和 CSRF 攻击。
---

常见的浏览器攻击可以分为 XSS 和 CSRF 攻击。

## XSS 攻击（跨站脚本攻击）

XSS 全称 Cross Site Scripting，为了和 CSS 区分开来，故简称 XSS，翻译过来就是"跨站脚本"。XSS 攻击是指黑客往 HTML 文件中或者 DOM 中注入恶意脚本，从而在用户浏览页面时利用注入的恶意脚本对用户实施攻击的一种手段。当页面被注入了恶意 JavaScript 脚本时，浏览器无法区分这些脚本是被恶意注入的还是正常的页面内容，所以恶意注入 JavaScript 脚本也拥有所有的脚本权限。被恶意注入的脚本可以做的事情：

1. 窃取 Cookie 信息。恶意 JavaScript 可以通过 document.cookie 获取 cookie 信息，然后通过 XMLHttpRequest 或者 Fetch 加上 CORS 功能将数据发送给恶意服务器，恶意服务器拿到用户的 cookie 信息之后，就可以在其他电脑上模拟用户的登录，然后进行转账等操作。
2. 监听用户行为。恶意 JavaScript 可以使用 addEventListener 接口来监听键盘事件，比如获取用户输入的信用卡等信息，将其发送到恶意服务器，黑客掌握了这些信息，又可以做很多违法的事情。
3. 修改 DOM 伪造假的登录窗口，用来欺骗用户输入用户名和密码等信息。
4. 在页面内生成浮窗广告，这些广告会严重影响用户体验。

恶意脚本是如何注入的，通常情况下有，存储型 XSS 攻击、反射型 XSS 攻击、基于 DOM 型攻击三种方式来注入恶意脚本。

**1. 存储型**

存储型 XSS 会把用户输入的数据"存储"在服务端，当浏览器请求数据时，脚本从服务器上传回并执行。这种 XSS 攻击具有很强的稳定性。比较常见的一个场景是攻击者在社区或论坛写下一篇包含恶意的 JavaScript 代码的文章或评论，文章或评论发表后，所有访问该文章或评论的用户，都会在他们的浏览器中执行这段恶意 JavaScript 代码：

例如在评论中输入以下留言，如果请求这段留言的时候服务端不做转义处理，请求之后页面会执行这段恶意代码

```bash
<script>alert('xss 攻击')</script>
```

**2. 反射型**

反射型 XSS 只是简单地把用户输入的数据"反射"给浏览器，这种攻击方式往往需要攻击者诱使用户点击一个恶意链接(攻击者可以将恶意链接直接发送给受信任的用户，发送的方式有很多种，比如 email、网站的私信、评论等，攻击者可以购买存在漏洞网站的广告，将恶意链接插入在广告的链接中)，或者提交一个表单，或者进入一个恶意网站时，注入脚本进入被攻击者的网站。最简单的示例是访问一个链接，服务端返回一个可执行脚本。

```bash
const http = require('http');
function handleReequest(req, res) {
  res.setHeader('Access-Control-Allow-Origin', '*');
  res.writeHead(200, {'Content-Type': 'text/html; charset=UTF-8'});
  res.write('<script>alert("反射型 XSS 攻击")</script>');
  res.end();
}

const server = new http.Server();
server.listen(8001, '127.0.0.1');
server.on('request', handleReequest);
```

**3. 基于 DOM**

基于 DOM 的 XSS 攻击是指通过恶意脚本修改页面的 DOM 结构，是纯粹发生在客户端的攻击：

```bash
<h2>XSS: </h2>
<input type="text" id="input">
<button id="btn">Submit</button>
<div id="div"></div>
<script>
  const input = document.getElementById('input');
  const btn = document.getElementById('btn');
  const div = document.getElementById('div');

  let val;

  input.addEventListener('change', (e) => {
      val = e.target.value;
  }, false);

  btn.addEventListener('click', () => {
      div.innerHTML = `<a href=${val}>testLink</a>`
  }, false);
</script>
```

点击 Submit 按钮后，会在当前页面插入一个链接，其地址为用户的输入内容。如果用户在输入时构造了如下内容：

```bash
'' onclick=alert(/xss/)
```

用户提交之后，页面代码就变成了：

```bash
<a href onlick="alert(/xss/)">testLink</a>
```

此时，用户点击生成的链接，就会执行对应的脚本。

## XSS 攻击防范

无论何种类型的 XSS 攻击，他们都有一个共同点，那就是首先往浏览器中注入恶意脚本，然后再通过恶意脚本将用户信息发送至恶意服务器上。所以可以通过阻止恶意脚本的注入和恶意消息的发送来实现。

**1. 输入检查**

不要相信用户的任何输入。对于用户的任何输入要进行检查、过滤和转义。建立可信任的字符和 HTML 标签白名单，对于不在白名单之列的字符或者标签进行过滤或编码。在 XSS 防御中，输入检查一般是检查用户输入的数据中是否包含<，>等特殊字符，如果存在，则对特殊字符进行过滤或编码，这种方式也称为 XSS Filter。而在一些前端框架中，都会有一份 decodingMap，用于对用户输入所包含的特殊字符或标签进行编码或过滤，如<，>，script，防止 XSS 攻击：

vuejs 中的 decodingMap，在 vuejs 中，如果输入带 script 标签的内容，会直接过滤掉

```bash
const decodingMap = {
  '&lt;': '<',
  '&gt;': '>',
  '&quot;': '"',
  '&amp;': '&',
  '&#10;': '\n'
}
```

**2. 输出检查**

用户的输入会存在问题，服务端的输出也会存在问题。一般来说，除富文本的输出外，在变量输出到 HTML 页面时，可以使用编码或转义的方式来防御 XSS 攻击。例如利用 sanitize-html 对输出内容进行有规则的过滤之后再输出到页面中。

**3. 利用 CSP**

Content Security Policy，即浏览器中的内容安全策略，它的核心思想就是服务器决定浏览器加载哪些资源，具体来说：

1. 限制加载其他域下的资源文件，这样即使黑客插入了一个 JavaScript 文件，这个文件也是无法被加载的；
2. 禁止向第三方域提交数据，这样用户数据也不会外泄；
3. 禁止执行内联脚本和未授权的脚本；
4. 提供上报机制，能帮助我们及时发现 XSS 攻击；
5. 因此利用好 CSP 能够有效降低 XSS 攻击的概率。

```bash
<meta http-equiv="Content-Security-Policy" content="default-src 'self'; img-src https://*; child-src 'none';">
```

**4. HttpOnly 防止截取 Cookie**

由于很多 XSS 攻击都是来盗用 Cookie 的，因此可以通过使用 HttpOnly 属性来保护 Cookie 的安全。浏览器将禁止页面的 JavaScript 访问带有 HttpOnly 属性的 Cookie。严格来说，HttpOnly 并非阻止 XSS 攻击，而是能阻止 XSS 攻击后的 Cookie 劫持攻击。

## CSRF/XSRF（跨站请求伪造）

CSRF，即 Cross Site Request Forgery，中译是跨站请求伪造。是指黑客引诱用户打开黑客的网站，在黑客的网站中，利用用户的登录状态发起的跨站请求。简单来讲，CSRF 攻击就是黑客利用了用户的登录状态，并通过第三方的站点来做一些坏事。

在这个攻击过程中，攻击者借助受害者的 Cookie 骗取服务器的信任，但并不能拿到 Cookie，也看不到 Cookie 的内容。而对于服务器返回的结果，由于浏览器同源策略的限制，攻击者也无法进行解析。（攻击者的网站虽然是跨域的，但是他构造的链接是源网站的，跟源网站是同源的，所以能够携带 cookie 发起访问）。攻击者无法从返回的结果中得到任何东西，他所能做的就是给服务器发送请求，以执行请求中所描述的命令，在服务器端直接改变数据的值，而非窃取服务器中的数据。例如删除数据、修改数据，新增数据等，无法获取数据。实现 csrf 攻击的方式大概有以下三种：

1. 自发 GET 请求：某论坛点击了黑客精心准备的小姐姐图片，他设置 `bash<img src="https://xxx.com/info?user=hhh&count=100">` ，进入页面后自动发送 get 请求，这时会自动带上关于 xxx.com 的 cookie 信息，假设你已经登录过。假设服务器没有相应的验证机制，它可能认为是一个正常的用户，然后进行相应的各种操作，可以是转账汇款以及其他恶意操作。
2. 诱导点击 GET 请求：在黑客的网站可能放上一个链接，驱使你点击 <a href="https://xxx/info?user=hhh&count=100" taget="_blank"> 点击进入修仙世界 </a>，点击后自动发送 GET 请求，同上。
3. 自动发送 POST 请求：黑客自己填了一个表单，写了一段自动提交的脚本，同时携带相应的用户 cookie 信息，让服务器以为是正常用户操作，然后进行恶意操作。

## CSRF 攻击防范

csrf 攻击不会往页面注入恶意脚本，因此黑客是无法获取用户页面数据的，最关键的是要能找到服务器的漏洞，因此对于 csrf 攻击主要的防护手段是提升服务器的安全性。

**1. 利用 Cookie 的 SameSite 属性**

CSRF 攻击最重要的就是自动发送目标站点下的 Cookie，因此可以对请求的 Cookie 的携带作一些限制，这个字段就是 SameSite。

1. Strict 模式下，浏览器完全禁止第三方请求携带 Cookie，比如请求 xxx.com 网站只能在 xxx.com 域名请求才能携带，其他都不行。
2. Lax 模式下，宽松一点，只能在 get 方法提交表单或者 a 标签发送 get 请求下携带，其他均不能。
3. None 模式，默认模式，请求会自动携带。

**2. 验证码**

验证码被认为是对抗 CSRF 攻击最简洁而有效的防御方法。从上述示例中可以看出，CSRF 攻击往往是在用户不知情的情况下构造了网络请求。而验证码会强制用户必须与应用进行交互，才能完成最终请求。因为通常情况下，验证码能够很好地遏制 CSRF 攻击。但验证码并不是万能的，因为出于用户考虑，不能给网站所有的操作都加上验证码。因此，验证码只能作为防御 CSRF 的一种辅助手段，而不能作为最主要的解决方案。

**3. Referer 和 Origin 属性**

根据 HTTP 协议，在 HTTP 头中有一个字段叫 Referer，它记录了该 HTTP 请求的来源地址。通过 Referer Check，可以检查请求是否来自合法的"源"。

**4. 添加 token 验证**

要抵御 CSRF，关键在于在请求中放入攻击者所不能伪造的信息，并且该信息不存在于 Cookie 之中。可以在 HTTP 请求中以参数的形式加入一个随机产生的 token，并在服务器端建立一个拦截器来验证这个 token，如果请求中没有 token 或者 token 内容不正确，则认为可能是 CSRF 攻击而拒绝该请求。
