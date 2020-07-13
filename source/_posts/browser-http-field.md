---
title: 说说 HTTP 的首部那些字段
date: 2020-06-12 15:48:29
tags:
  - HTTP
excerpt: 当我们想要了解一个请求和响应的具体信息的时候，通常会打开浏览器的开发者模式 Network 一栏查看相应请求的内容，比如打开百度的首页，我们会看到 General、Response Headers、Request Headers 等，接下来就来详细说说这些字段的作用及使用。
---

当我们想要了解一个请求和响应的具体信息的时候，通常会打开浏览器的开发者模式 Network 一栏查看相应请求的内容，比如打开百度的首页，我们会看到 General、Response Headers、Request Headers 等，接下来就来详细说说这些字段的作用及使用。

![tOwFiV.jpg](https://s1.ax1x.com/2020/06/12/tOwFiV.jpg)

## HTTP 报文

在用 HTTP 通信的过程中，会发送请求消息和响应消息，这些消息就被称为 HTTP 报文，客户端发送的 HTTP 报文叫做请求报文，服务端的叫响应报文。HTTP 报文大致可分为报文首部和报文主体两块，是由空行（CR+LF）来分割的，在一些情况下可以不需要传输报文主体。我们用图来描述一下报文的结构。

![tOs8rF.png](https://s1.ax1x.com/2020/06/12/tOs8rF.png)

- 请求行：主要包含用于请求的方法，请求 URI 和 HTTP 版本。例如 `GET /home HTTP/1.1`；
- 状态行：主要包含表明响应结果的状态码，原因短语和 HTTP 版本。 例如 `HTTP/1.1 200 OK`；
- 首部字段：HTTP 首部字段根据用途可以划分为 4 种，分别是通用首部、请求首部、响应首部和实体首部；
  - 通用首部字段：请求报文和响应报文双方都会使用的首部；
  - 请求首部字段：从客户端向服务器发送的请求报文时使用的首部，包含了请求的附加内容、客户端信息、响应内容优先级等等；
  - 响应首部字段：从服务端向客户端返回响应报文时使用的首部，包含了响应的附加内容等；
  - 实体首部字段：针对请求报文和响应报文的实体部分使用的首部，补充了资源内容更新时间等与实体相关的信息。
- 其他。

## HTTP 首部

在上面几个组成部分中，首部字段是比较多的，也比较重要，通过它可以在客户端和服务端协商一些重要的信息，下面就一一来看看。

HTTP 首部字段以 `key-value` 的形式表示字段名和字段值的，中间用冒号 `:` 分隔，例如用来表示报文主体类型的字段 `Content-Type: text/html`。

> 当 HTTP 报文首部字段重复了该如何处理，这种情况并没有在规范中明确，所以各个浏览器的处理逻辑也不同，有的浏览器优先处理第一次出现的首部字段，有的则会优先处理最后出现的。

我们列出一些经常会用到的字段。

### 通用首部字段

| 首部字段名        | 说明                       |
| ----------------- | -------------------------- |
| Cache-Control     | 控制缓存的行为             |
| Connection        | 逐跳首部、连接的管理       |
| Date              | 创建报文的日期时间         |
| Pragma            | 报文指令                   |
| Transfer-Encoding | 指定报文主体的传输编码方式 |
| Via               | 代理服务器的相关信息       |
| Warning           | 错误通知                   |

**Cache-Control**
`Cache-Control` 是与缓存相关的字段。

- pulic：表示其他用户也可以利用缓存；
- private：与 public 相反，缓存服务器会对特定的用户提供缓存服务，对于其他用户发送来的请求，代理服务器则不会返回缓存；
- no-cache：客户端发送的请求中如果包含该指令，则表示客户端不会接收缓存的响应，需要缓存服务器把客户端的请求转发给源服务器。如果服务器返回的响应中包含该指令，则表示缓存服务器不能缓存该资源；
- no-store：该指令规定了不能在本地存储请求或响应的任一部分；
- max-age：当客户端发送的请求中包含该指令时，如果判定缓存资源的缓存时间比指定时间小，那么客户端就接收缓存的资源。当服务器设置该指令时，表示这个资源可以保存的最长时间，在此之前都可以使用缓存。

**Connection**
`Connection` 字段是用来管理持久连接的。在 HTTP/1.1 版本中默认是开启的，如果双方想断开连接，则指定 Connection 为 Close 值，即 `Connection: Close`。当你想重新开启的时候，就发送 `Connection: Keep-Alive`，服务器端会返回同样的信息，而且会设置连接时间。

```bash
// 客户端发送
GET / HTTP/1.1
Connection: Keep-Alive

// 服务器端
HTTP/1.1 200 OK
...
Keep-Alive: timeout=10，max=500
Connection: Keep-Alive
...
```

**Date**
`Date` 字段是用来表明创建 HTTP 报文的日期和时间。比如`Date: Tue, 03-Jul-12 17:39:49 GMT`。

**Pragma**
`Pragma` 是 HTTP/1.1 之前版本的遗留字段，作为与 HTTP/1.0 的向后兼容而定义。该字段虽然属于通用首部，但只用在客户端发送的请求中，也只有一个取值`Pragma: no-cache`。

**Transfer-Encoding**
`Transfer-Encoding` 规定了传输报文主体时采用的编码方式，但仅对分块传输编码有效。

**Via**
使用首部字段 Via 是为了追踪客户端和服务器之间的请求和响应报文的传输路劲。报文经过代理或网关时，会先在首部字段 Via 中附加该服务器的信息，然后再转发。

### 请求首部字段

| 首部字段名          | 说明                                          |
| ------------------- | --------------------------------------------- |
| Accept              | 用户代理可处理的媒体类型                      |
| Accept-Charset      | 优先的字符集                                  |
| Accept-Encoding     | 优先的内容编码                                |
| Accept-Language     | 优先的语言类型                                |
| Authorization       | Web 认证信息                                  |
| Host                | 请求资源所在的服务器                          |
| If-Match            | 比较实体标记 Etag                             |
| If-Modified-Since   | 比较资源的更新时间                            |
| If-None-Match       | 比较实体标记，与 If-Match 相反                |
| If-Unmodified-Since | 比较资源的更新时间，与 If-Modified-Since 相反 |
| Range               | 实体的字节范围请求                            |
| Referer             | 对请求中 URI 的原始获取方                     |
| User-Agent          | HTTP 客户端程序的信息                         |
| Cookie              | 服务器接收到的 Cookie 信息                    |

- **Accept** 字段可通知服务器，用户代理能够处理的媒体类型及媒体类型的优先级。使用 `type/subtype` 的形式，一次可指定多种媒体类型。
- **Accept-Charset** 字段用来通知服务器用户代理支持的字符集及字符集的优先顺序，一次也可以指定多种字符集。
- **Accept-Encoding** 字段用来告诉服务器用户代理支持的内容编码及内容编码的优先级顺序，可一次指定多种内容编码。比如：gzip、compress、deflate
- **Accept-Language** 字段用来告诉服务器能够处理的自然语言集以及相对优先级，可一次指定多种。
- **Authorization** 字段用来告诉服务器，用户代理的认证信息。通常，想要通过服务器认证的用户代理会在接收到返回的 401 状态码响应后，把首部字段 `Authorization` 加入请求中。
- **Host** 字段会告诉服务器，请求的资源所处的互联网主机名和端口号。`Host` 是 HTTP/1.1 规范中唯一一个必须被包含在请求内的首部字段。
- **If-Match**，形如 `If-xxx` 的请求首部字段都可以称为条件请求。服务器接受到附带条件的请求后，只有判断指定条件为真时，才会执行请求。
- **If-Modified-Since** 字段属于附带条件之一，它会告诉服务器如果 `If-Modified-Since` 字段值之后的时间资源修改过，则返回修改的资源；如果没有修改，则返回状态码 304 Not Modified 响应。`If-Modified-Since` 主要用于确认代理或客户端拥有的本地资源的有效性。
- **Range** 字段可用于设置只请求部分资源的情况，比如 `Range: bytes=5001-10000`，表示只请求从 5001 到 10000 字节的资源。当服务器处理这种情况时，会返回状态码为 206 Partial Content 的响应，无法处理时，则会返回状态码 200 OK 及全部的资源。
- **Referer** 字段会告诉服务器请求的原始资源 URI。
- **User-Agent** 字段会把请求的浏览器和用代理名称等信息发送给服务器。

### 响应首部字段

| 首部字段名       | 说明                             |
| ---------------- | -------------------------------- |
| Accept-Ranges    | 是否接受字节范围请求             |
| ETag             | 资源的匹配信息                   |
| Location         | 令客户端重定向至指定的 URI       |
| Server           | HTTP 服务器的安装信息            |
| Vary             | 代理服务器缓存的管理信息         |
| WWW-Authenticate | 服务器对客户端的认证信息         |
| Set-Cookie       | 开始状态管理所使用的 Cookie 信息 |

- **Accept-Ranges** 字段用来告诉客户端服务器是否能够处理范围请求，以指定获取服务器端的某一部分的资源。取值只有两种，可处理部分请求时值为 `bytes`，反之则是 `none`。
- **ETag** 字段是以字符串的形式给资源添加唯一的标识，当资源更新时，`ETag` 值也会更新。ETag 有强弱之分，强 ETag 值，无论实体发生多么细微的变化都会改变其值；而弱 ETag 值只用于提示资源是否相同，发生变化时才会改变，这时会在字段值的开始处附加 W/，比如`ETag: W/"usagi-1234"`。
- **Location** 字段将响应接收方引导至某个与请求 URI 不同的位置，但几乎所有的浏览器都会强制地对重定向资源进行访问。
- **Server** 字段用来告诉客户端当前服务器上安装的 HTTP 服务器应用程序的信息。比如`Server: Apache/2.2.6 (Unix) PHP/5.25`。
- **WWW-Authenticate** 字段会告诉客户端适用于访问请求 URI 所指定资源的认证方案等。在状态码为 401 的响应中，肯定会带有该字段。
- **Set-Cookie** 字段主要用于服务器对于客户端状态的管理。`Cookie` 的 `HttpObly` 属性主要用于防止 XSS 攻击对于 Cookie 的窃取。通过设置`Set-Cookie: name=value; HttpObly`，在 Web 页面内可以对 Cookie 正常读取，但使用 JS 的 `document.cookie` 就不能获取。

### 实体首部字段

| 首部字段名       | 说明                   |
| ---------------- | ---------------------- |
| Allow            | 资源可支持的 HTTP 方法 |
| Content-Encoding | 实体主体适用的编码方式 |
| Content-Language | 实体主体的自然语言     |
| Content-Length   | 实体主体的大小         |
| Content-Lacation | 替代对应资源的 URI     |
| Content-Range    | 实体主体的位置范围     |
| Content-Type     | 实体主体的媒体类型     |
| Expires          | 实体主体过期的日期     |
| Last-Modified    | 资源的最后修改日期     |

- **Allow** 字段用于通知客户端能够支持的请求资源的所有 HTTP 方法。当服务器接收到不支持的 HTTP 方法时，会以状态码 405 Method Not Allowed 作为响应返回，同时把支持的所有方法写入字段 `Allow` 中。
- **Content-Encoding** 字段告诉客户端服务器对实体的主体部分选用的内容编码。主要有 gzip、compress 等；
- **Content-Language** 字段告诉客户端实体主体使用的自然语言。
- **Content-Length** 字段表明了实体主体部分的大小。当进行内容编码传输时，便不再使用。
- **Content-Lacation** 字段给出与报文主体部分相对应的 URI。
- **Content-Range** 字段当客户端进行范围请求时，告诉客户端返回的符合范围请求的响应实体的具体范围。比如客户端请求 0-10000 的范围，服务器先返回 0-5000，`Content-Range: bytes 0-5000/10000, Content-Length: 5000`；
- **Content-Type** 字段说明了实体主体内对象的媒体类型。
- **Expires** 字段会将资源失效的日期告诉客户端。当在指定时间内会使用缓存，否则重新发送请求新资源。当首部字段 `Cache-Control` 有指定 `max-age` 指令时，会优先处理 `max-age`。
- **Last-Modified** 字段标明了资源的最后修改时间。

## HTTP 状态码

RFC 规定 HTTP 的状态码为三位数，被分为五类:

- 1xx: 表示目前是协议处理的中间状态，还需要后续操作。
- 2xx: 表示成功状态。
- 3xx: 重定向状态，资源位置发生变动，需要重新请求。
- 4xx: 请求报文有误。
- 5xx: 服务器端发生错误。

### 1xx

**101 Switching Protocols。**在 HTTP 升级为 WebSocket 的时候，如果服务器同意变更，就会发送状态码 101。

### 2xx

**200 OK** 是见得最多的成功状态码。通常在响应体中放有数据。
**204 No Content** 含义与 200 相同，但响应头后没有 body 数据。
**206 Partial Content** 顾名思义，表示部分内容，它的使用场景为 HTTP 分块下载和断点续传，当然也会带上相应的响应头字段 Content-Range。

### 3xx

**301 Moved Permanently** 即永久重定向，对应着 302 Found，即临时重定向。
比如你的网站从 HTTP 升级到了 HTTPS 了，以前的站点再也不用了，应当返回 301，这个时候浏览器默认会做缓存优化，在第二次访问的时候自动访问重定向的那个地址。
而如果只是暂时不可用，那么直接返回 302 即可，和 301 不同的是，浏览器并不会做缓存优化。
**304 Not Modified:** 当协商缓存命中时会返回这个状态码。

### 4xx

**400 Bad Request**: 开发者经常看到一头雾水，只是笼统地提示了一下错误，并不知道哪里出错了。
**403 Forbidden**: 这实际上并不是请求报文出错，而是服务器禁止访问，原因有很多，比如法律禁止、信息敏感。
**404 Not Found**: 资源未找到，表示没在服务器上找到相应的资源。
**405 Method Not Allowed**: 请求方法不被服务器端允许。
**406 Not Acceptable**: 资源无法满足客户端的条件。
**408 Request Timeout**: 服务器等待了太长时间。
**409 Conflict**: 多个请求发生了冲突。
**413 Request Entity Too Large**: 请求体的数据过大。
**414 Request-URI Too Long**: 请求行里的 URI 太大。
**429 Too Many Request**: 客户端发送的请求过多。
**431 Request Header Fields Too Large**: 请求头的字段内容太大。

### 5xx

**500 Internal Server Error**: 仅仅告诉你服务器出错了，出了啥错咱也不知道。
**501 Not Implemented**: 表示客户端请求的功能还不支持。
**502 Bad Gateway**: 服务器自身是正常的，但访问的时候出错了，啥错误咱也不知道。
**503 Service Unavailable**: 表示服务器当前很忙，暂时无法响应服务。

参考：《图解 HTTP》