# 前端性能优化

## 1.性能优化方案汇总

> 性能优化的第一要义是：让用户更快得到想要的内容。

前端性能优化大概有这几个方面：

- 1. 减少（合并） HTTP 请求

     - 接口设计时减少、合并某些接口
     - 服务端渲染

     - 利用缓存

- 2. 减少传递内容大小和 HTTP 头信息大小（HTTP2）
     - code spliting + 懒加载
     - 图片压缩、base64替代 + 图片懒加载
     - HTTP2
  3. 加快网速
     - CDN加速
  4. 浏览器加载优化
     - JS资源放在HTML最下面
     - 减少重排、重绘
     - 图片加载优化
     - 减少内存消耗
  5. 欺骗用户的眼睛
     - 过渡动画
     - 框架屏

## 2. 减少 HTTP 请求数量

_HTTP 请求各阶段耗时_:

![HTTP请求各阶段耗时](../_media/synthesize_performance_http.png)

名词解释：

- Queueing: 在请求队列中的时间。
- Stalled: 从 TCP 连接建立完成，到真正可以传输数据之间的时间差，此时间包括代理协商时间。
- Proxy negotiation: 与代理服务器连接进行协商所花费的时间。
- DNS Lookup: 执行 DNS 查找所花费的时间，页面上的每个不同的域都需要进行 DNS 查找。
- Initial Connection / Connecting: 建立连接所花费的时间，包括 TCP 握手/重试和协商 SSL。
- SSL: 完成 SSL 握手所花费的时间。
- Request sent: 发出网络请求所花费的时间，通常为一毫秒的时间。
- Waiting(TFFB): TFFB 是发出页面请求到接收到应答数据第一个字节的时间。
- Content Download: 接收响应数据所花费的时间。

### 2.1 减少没必要的HTTP请求数量

如：获取省市信息，比较固定，不必哪里使用都去获取一次，应该请求一次后存入storage等。

### 2.2 善用缓存

某些更新频率比较低的文件，如：logo，可以设置缓存，减少请求。

### 2.3 使用服务端渲染

客户端渲染: 获取 HTML 文件，根据需要下载 JavaScript 文件，运行文件，生成 DOM，再渲染。

服务端渲染：服务端返回 HTML 文件，客户端只需解析 HTML。

- 优点：首屏渲染快，SEO 好。
- 缺点：配置麻烦，增加了服务器的计算压力。

## 3. 减少传输内容大小

### 3.1 使用 HTTP2--减小HTTP请求头大小

**HTTP2**有如下优势:

- **解析速度快**：(HTTP2 是基于帧的协议,HTTP 1.1 基于文本)
- **多路复用**：（一个 TCP 连接可以**同时**发送多次 HTTP 请求，HTTP1.1 无法做的**同时**）
- **首部压缩**：（首次 HTTP 请求会建立一个*表*，然后再次请求时遇到相同 Header 字段只需要发送索引即可）
- **优先级**：HTTP2 可以对比较紧急的请求设置一个较高的优先级
- **服务端推送**：例如当浏览器请求一个网站时，除了返回 HTML 页面外，服务器还可以根据 HTML 页面中的资源的 URL，来提前推送资源。

### 3.2. 压缩能压缩的一切--减小HTTP传输内容体积

如：

- 图片压缩

- 压缩字体文件
- JavaScript：UglifyPlugin
- CSS ：MiniCssExtractPlugin
- HTML：HtmlWebpackPlugin

### 3.3 代码分割（Code spliting）+ 懒加载

- webpack等打包工具可以实现“只要想要的代码”，而不是把整个SPA页面的代码全部返回

## 4. 静态资源使用 CDN



## 5. 浏览器加载优化

### 5.1 优化资源加载顺序

> JavaScript 文件放在底部，将 CSS 放在文件头部

- JS 文件放在底部是因为 JS 加载时会阻塞页面渲染
- CSS 放在首部是为了不让用户看到没有样式的 HTML

### 5.2. 图片优化

- 图片延迟加载

  在页面中，先不给图片设置路径，只有当图片**出现在浏览器的可视区域时**，才去加载真正的图片，这就是**延迟加载**。

- 尽可能利用 CSS3 效果代替图片

- 响应式图片

  响应式图片的优点是浏览器能够根据屏幕大小自动加载合适的图片。

  _通过 picture 实现_：

  ```html
  <picture>
    <source srcset="banner_w1000.jpg" media="(min-width: 801px)" />
    <source srcset="banner_w800.jpg" media="(max-width: 800px)" />
    <img src="banner_w800.jpg" alt="" />
  </picture>
  ```

  _通过 @media 实现_：

  ```css
  @media (min-width: 769px) {
    .bg {
      background-image: url(bg1080.jpg);
    }
  }
  @media (max-width: 768px) {
    .bg {
      background-image: url(bg768.jpg);
    }
  }
  ```


### 5.3 尽量减少内存消耗

1. 使用事件委托

​	使用**事件委托**可以减少内存消耗。

## 参考

总结很到位，👍 值得好好看，并且用到项目中[前端性能优化 24 条建议（2020）](https://segmentfault.com/a/1190000022205291)
