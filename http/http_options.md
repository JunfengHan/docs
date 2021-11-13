## <span style="color:#ffcc99">1. HTTP Options 请求</span>

### <span style="color:#ffe6cc">1.1 什么是 OPTIONS 请求</span>

HTTP 定义了一组请求方法，以表明要对给定资源执行的操作。

方法有这些：

1. GET : 请求一个指定资源
2. HEAD : 请求一个与 GET 请求的响应相同的响应，无响应体
3. POST : 将实体提交到指定的资源
4. PUT : 请求有效载荷替换目标资源的所有当前表示
5. DELETE : 删除指定资源
6. CONNECT : 建立一个到由目标资源标识的服务器隧道
7. <span style="color:#ff661a">OPTIONS : 用于描述目标资源的通信选项 </span>
8. TRACE : 沿着到目标资源的路径执行一个消息环回测试
9. PATCH : 用于对资源应用部分修改

### <span style="color:#ffe6cc">1.2 预检(OPTIONS)请求使用示例</span>

##### 检测服务器所支持的请求方法，响应报文中包含 Allow 字段中

```
curl -X OPTIONS http://example.org -i

------------------------
HTTP/1.1 200 OK
Allow: OPTIONS, GET, HEAD, POST
Cache-Control: max-age=604800
Date: Thu, 13 Oct 2016 11:45:00 GMT
Expires: Thu, 20 Oct 2016 11:45:00 GMT
Server: EOS (lax004/2813)
x-ec-custom-error: 1
Content-Length: 0
--------------------------
```

### <span style="color:#ffe6cc">1.3 CORS 中的预检请求</span>

在 [CORS](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Access_control_CORS) 中，可以使用 OPTIONS 方法发起一个预检请求，以检测实际请求是否可以被服务器所接受。

Access-Control-Request-Method: POST； 该首部字段告知服务器实际使用的 HTTP 方法。

Access-Control-Request-Headers: X-PINGOTHER, Content-Type； 该首部字段告知服务器实际请求所携带的自定义首部字段。

## <span style="color:#ffcc99">2. 关于浏览器预检请求</span>

<span style="color:#ff661a">在非简单请求且跨域的情况下，浏览器会发起 options 预检请求。</span>

##### 什么是简单请求和非简单请求？

简单请求要满足两个条件：

- 请求方法是以下三种之一：HEAD、GET、POST

- HTTP 的头部信息不超出以下几种字段

  Accept

  Accept-Language

  Content-Language

  Last-Event-ID

  Content-Type: 只限于（aplication/x-www-form-urlencoded、multipart/form-data、text-plain）

复杂请求：

**非简单请求都是复杂请求。**

常见复杂请求：

- PUT 或 DELETE 请求
- Content-Type 字段为 application/json
- 添加额外的 Http header ,如： access_token
- 等

**<span style="color:#ff661a">在跨域的情况下，非简单请求会先发起一次空 body 的 OPTIONS 请求，称为"预检"请求，用于向服务器请求权限信息，等预检请求被成功响应后，才发起真正的 http 请求。</span>**
