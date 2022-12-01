# HTTP 与网络篇

## 1. HTTP 问答

问 ❓：HTTP 与 HTTPS 区别？

> 答：
> <1> HTTPS 更安全：HTTP 协议的数据传输是**明文**的，是不安全的，HTTPS 使用了 SSL/TLS 协议进行了加密处理
>
> HTTPS = HTTP + 加密 + 认证 + 完整性保护;
>
> <2> HTTPS 协议需要到 ca 申请证书，一般要花钱
>
> <3> 默认端口不同：HTTP 默认是**80**端口，HTTPS 默认是**443**
>
> <4> HTTPS 协议握手阶段比较费时，因为要进行 SSL 协议的连接

链接：[HTTPS 工作流程](https:shushuo.me/#/http/http?id=_43-https-如何工作)

问 ❓：GET 与 POST 区别？

> 答：
>
> <1>**数据大小限制**：GET 请求通过 URL 参数发送数据，有**大小限制**（2048 字节）,POST 请求没有限制
>
> <2>**数据类型限制**：GET 只允许 ASCII 字符,POST 没有限制，可以用**二进制**
>
> <3>**安全性**：GET 通过 URL 参数明文发送数据，POST 传输的内容不会被浏览器记录或保存在服务器日志中
>
> <4>**是否会被记录**：GET 请求会被浏览器历史记录，可以回退，POST 不行

[GET 与 POST 请求区别](https://www.w3school.com.cn/tags/html_ref_httpmethods.asp)

问 ❓：每次 HTTP 请求都会重新建立 TCP 连接吗？

> HTTP/1.0 中 Connection 默认是 close 的，即每次请求都会重新建立和断开 TCP 连接；
>
> HTTP/1.1 中 Connection 默认是 keep-alive 的，即 tcp 连接可以复用，不用每次都要重新建立和断开 TCP 连接。
>
> 一般情况下*复用的 TCP 连接*在等待设置的**超时**时间之后还没有被任何连接使用的话，TCP 连接就会主动断开。

```js
// HTTP 1.1 的一个响应头
HTTP/1.1 200 OK

// 使用 Keep-Alive 连接状态
Connection: Keep-Alive
// timeout：保持 5s TCP连接；（注意时间不可以超过TCP层的时间限制）
// 设置最大 HTTP请求数为 1000
Keep-Alive: timeout=5, max=1000
```

问 ❓：一个 TCP 连接中 HTTP 请求发送可以一起发送么（比如一起发三个请求，再三个响应一起接收）？

> 答：HTTP/1.1 存在一个问题，单个 TCP 连接在*同一时刻只能处理一个请求*;
>
> HTTP2 提供了 Multiplexing **多路复用**特性，_可以在一个 TCP 连接中同时完成多个 HTTP 请求_;

问 ❓：HTTP 2.0 相比 HTTP1.1 有哪些改进？

> 答：主要改进了：
>
> 1.**传输内容格式**：为*二进制格式*（Binary Format），健壮性更好，HTTP1.x 的解析是基于文本；
>
> 2.**多路复用**（MultiPlexing）：使用 Multiplexing 实现一个 TCP 连接<span style="color: #ff0000; font-size: 16px;">同时</span>处理多个 HTTP 请求；
>
> 3.**header 压缩**：HTTP2.0 使用 encoder 来减少需要传输的 header 大小，通讯双方各自 cache 一份 header fields 表，既避免了重复 header 的传输，又减小了需要传输的大小;
>
> 4.**服务端推送**（server push）: HTTP2.0 也具有 server push 功能;

问 ❓：了解 TCP 的三次握手与四次挥手吗？

>

问 ❓：知道哪些常见的状态码？

> [答案在此](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Status)

## 2. Ajax 和 Fetch

问 ❓：Ajax和Fetch有何区别？

>

问 ❓：Ajax发送的JSON数据和form-data发送数据有啥区别？

>1、Ajax发送数据时，浏览器不知道数据来自哪里，默认设置成 HTTP的请求体（即：payload body），此时设置 Content-Type: application/json;
>
>请求体如下：
>
>```
>POST /some-path HTTP/1.1
>Content-Type: application/json
>
>{ "foo" : "bar", "name" : "John" }
>```
>
>2、Html的表单POST方法，且设置了 Content-Type: application/x-www-form-urlencoded 或 multipart/form-data，请求体如下：
>
>```
>POST /some-path HTTP/1.1
>Content-Type: application/x-www-form-urlencoded
>
>foo=bar&name=John
>```
>
>两者区别：
>
>- multipart/form-data：既可以上传文件等二进制数据，也可以上传表单键值对，只是最后会转化为一条信息；
>- application/x-www-form-urlencoded：只能上传键值对，并且键值对都是间隔分开的；
>
>注意⚠️，不同的**Content-Type**<span style="color: #ff6600; font-size: 14px;">后端解析时使用的方法不同</span>,很容易出现获取不到数据的情况。

## 
