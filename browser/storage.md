# 浏览器本地存储

## 1. 浏览器存储的方式

- Cookie（本身是为了浏览器与服务器回话而设立的机制，但是被乱用为存储了）

## <span style="color:#ffcc99">1. Cookie 和 Session 是什么</span>

<span style="font-size:24px; color:#666666; background:#eee; padding: 0 4px">cookie:</span>

存储在客户端的一小段用户信息数据。

_Chrome 查看 localhost 域名保存的 Cookie（如下）：_

![chrome](file:///Users/han/Documents/blogImg/%E6%88%AA%E5%B1%8F2020-05-03%20%E4%B8%8B%E5%8D%884.19.27.png?lastModify=1588494153)

Cookie 字段的解释：

- Name：名称
- Value：值
- Domain: Cookie 所属的域名
- Path：所属域名路径
- Expires/Max-Age: 过期截止日期/有效时间长度
- Secure: 安全属性，只有在请求使用 SSL 和 HTTPS 协议时才会被发送到服务器

MDN：保密或敏感信息永远不要在 HTTP cookie 中存储或传输，因为整个机制从本质上来说都是不安全的，比如前述协议并不意味着所有的信息都是经过加密的。

<span style="font-size:24px; color:#666666; background:#eee; padding: 0 4px">Session:</span>

<span style="color:#ff661a"> Session 是一种保存在服务器上记录客户状态的机制。</span>

是存储在服务器的一个会话数据。

## <span style="color:#ffcc99">2. 为什么需要 Cookie 和 Session</span>

这个要从 HTTP 协议说起了：

Http 是无状态协议，它做的事情很简单，接收客户端请求 Http Request ，返回结果 Http Response,仅此而已。

为了记录用户是否登录、视频观看进度等，我们需要保存用户的登录状态。

保存客户端状态通常有两种形式：

- 在客户端记录：服务器返回登陆信息给客户端，客户端保存在本地，每次发送请求时带上这条记录，这就是所谓的 Cookie
- 在服务器记录：服务器返回登录信息给客户端，自己记录这个用户的登录状态，

## <span style="color:#ffcc99">3. session 原理</span>

原理是服务端为每一个 session 维护一份会话信息数据，客户端和服务端依靠一个全局唯一的标识来访问会话信息数据。

**_session 工作主要步骤：_**

1. 用户提交包含用户名和密码的 HTTP 请求
2. 服务器验证用户发过来的用户名和密码
3. 如果正确，则把当前用户对象存储到<span style=" color:#666666; background:#eee; padding: 0 4px"> redis </span> 中
4. redis 中生成 ID，这个 ID 就是全局唯一标识符 <span style=" color:#666666; background:#eee; padding: 0 4px">Session ID</span>,通过 Session ID 可以从 redis 中取出对应的用户对象，如敏感数据（authed=true）等
5. 开辟数据存储空间，一般在内存中创建相应的数据结构，但这种情况下，系统断电等会造成数据丢失。有时需要做持久化处理，把 sessin 信息写到数据库中。
6. 服务器设置 cookie 为 sessionId='XXX' || checksum 并发送 HTTP 响应， 将 session 的全局唯一标识符发给客户端
7. 客户端收到 HTTP 响应后，是看不到任何敏感数据的。此后的请求中，发送该 cookie 给服务器
8. 服务器收到此后的 HTTP 请求后，检查 cookie 中是否有 Session ID, 并进行篡改验证
9. 如果验证通过，根据 SessionID 从 redis 中取出对应的用户对象，查看用户状态并继续执行业务逻辑

### <span style="color:#ffe6cc">3.1 如何生成全局唯一的标识符</span>

### <span style="color:#ffe6cc">3.2 如何存放到内存中和持久化</span>

### <span style="color:#ffe6cc">3.3 如何发送给客户端</span>

​ 这个是问题的关键，如何把 <span style=" color:#666666; background:#eee; padding: 0 4px">sessionId</span> 发送到客户？

​ 根据 HTTP 的特性，无非只有两种办法：

    	1. 把sessionID 放在请求行、请求头 （cookie 方式）
    	2. 放在Body 里 （URL 重写）

[mnd: Set-Cookie](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Set-Cookie)

## <span style="color:#ffcc99">4. express-session 中间件  </span>

[express-session npm 地址](https://www.npmjs.com/package/express-session)

express-session 可以很好的解决 3 中的问题：

options:

```
1. name - cookie的名字（原属性名为 key）。（默认：’connect.sid’）
2. store - session存储实例
3. secret - 用它来对session cookie签名，防止篡改
4. cookie - session cookie设置 （默认：{ path: ‘/‘, httpOnly: true,secure: false, maxAge: null }）
5. genid - 生成新session ID的函数 （默认使用uid2库）
6. rolling - 在每次请求时强行设置cookie，这将重置cookie过期时间（默认：false）
7. resave - 强制保存session即使它并没有变化 （默认： true）
8. proxy - 当设置了secure cookies（通过”x-forwarded-proto” header ）时信任反向代理。当设定为true时，
”x-forwarded-proto” header 将被使用。当设定为false时，所有headers将被忽略。当该属性没有被设定时，将使用Express的trust proxy。
9. saveUninitialized - 强制将未初始化的session存储。当新建了一个session且未设定属性或值时，它就处于
未初始化状态。在设定一个cookie前，这对于登陆验证，减轻服务端存储压力，权限控制是有帮助的。（默认：true）
10. unset - 控制req.session是否取消（例如通过 delete，或者将它的值设置为null）。这可以使session保持存储
状态但忽略修改或删除的请求（默认：keep）
```

一个简单的 express-session 配置：

```javascript
app.use(
  session({
    name: "legendBlog",
    store: new RedisStore({
      client: new Redis({
        host: "127.0.0.1",
        port: 6379,
      }),
    }),
    secret: "legendBlog",
    resave: true,
    saveUninitialized: true, // 初始化session 时是否保存到存储
    cookie: {
      maxAge: 360000000,
      domain: "localhost",
    },
  })
);
```

服务器会在 http Response 中设置 Set-Cookie 字段，name 为 ‘legendBlog’, value 为 express-session 生成的一个 sessionID. 同时这个 sessionID 就是用户对象的对话的 ID，可以保存一些登录信息等到 redis, 方便判断用户是否登录。SetCookie 中还包含一些其他字段，如过期时间 Expries 等。

_上面的 express-session 返回的 Set-Cookie 信息：_![截屏2020-05-05 上午10.49.28](/Users/han/Documents/blogImg/截屏 2020-05-05 上午 10.49.28.png)

然而会有个问题，跨域导致 Set-Cookie 无法生效，我们设置的 cookie 不会保存到浏览器, 所以每次请求服务器时是不会发送 cookie 过去的。

_cookie 设置没有成功：_

![截屏2020-05-05 上午11.51.42](/Users/han/Documents/blogImg/截屏 2020-05-05 上午 11.51.42.png)

如何解决跨域导致 Set-Cookie 无效?

我发送 http 请求使用的是 asiox ,必须设置 ：withCredentials

```javascript
export const axiosInstance = axios.create({
  baseURL: "http://localhost:3001",
  timeout: 5000,
  headers: {
    "Access-Control-Allow-Credentials": true,
  },
  withCredentials: true,
});
```

_cookie 设置成功 :_

![截屏2020-05-05 上午11.54.02](/Users/han/Documents/blogImg/截屏 2020-05-05 上午 11.54.02.png)

参考：

[MDN:HTTP cookies](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Cookies)

[Express --- espress-session (八)](https://www.jianshu.com/p/5a0ccd1ee27e)

[cookie 和 session](https://wiki.jikexueyuan.com/project/node-lessons/cookie-session.html)

[HTTP session 原理及几个思维拓展](https://swiftcafe.io/2017/05/30/about-session/)

## 参考

[缓存总览：从性能优化的角度看缓存系列](https://github.com/amandakelake/blog/issues/43)

[一文带你看懂 cookie，面试前端不用愁](https://zhuanlan.zhihu.com/p/52091630)
