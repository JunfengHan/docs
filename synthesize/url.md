# 一次搞懂前端 URL

## 1. 什么是 URL

<code style="color: #708090; background-color: #F5F5F5; font-size: 18px">统一资源定位符</code>(Uniform Resource Locator，缩写：URL)，俗称**网址**。

_url 组成：_

![url组成](../_media/synthesize_url.png)

## 2. 如何获取 URL

_获取当前页面的 URL 对象:_

```js
window.location;

document.location;

window.location === document.location;
```

**window.location 就是当前页面的 URL 对象：**

- href

  当前页面的“URL”字符串

- protocol

  协议，如（"http:"）

- host

  当前页面的“域名”+ “端口”

- origin

  页面来源的域名的标准形式

- port:

  **端口**

- hash

  **哈希**（如：“#/js/deep”）

- search

  URL**查询**（如：“?name=han&age=12”）,请求参数**name** 和 **age** 使用 & 分隔

_获取当前页面的 URL:_

```js
window.location.href;
```

## 2. 创建一个 URL 对象

```js
let url = new URL(
  "https://zh.wikipedia.org:443/w/index.php??title=Special&name=Han&age=18#1234"
);

console.log(url);
```

## 3. 如何获取一个 URL 中的请求参数

_方法 1_: **使用原生 API**

```js
// 创建一个URL实例对象

function getParams(url) {
  // 生成 URL实例
  url = new URL(url);
  // 获取 url 的 search(包含？)
  let urlSearch = url.search;

  // 生成一个 URLSearchParams 对象
  let searchParams = new URLSearchParams(urlSearch);

  let params = {};

  // 遍历 searchParams
  for (let item of searchParams) {
    params[item[0]] = item[1];
  }

  return params;
}

// let url ="https://zh.wikipedia.org:443/w/index.php?title=Special&name=Han&age=18#1234";
// getParams(url);
```

> 缺点：IE 不支持

_方法 2_: **字符串截取**

```js
function getParams(url) {
  let params = {};
  // 生成 URL实例
  url = new URL(url);
  // 获取 url 的 search(包含？)
  let urlSearch = url.search;
  // 分割 urlSearch
  var vars = urlSearch.split("&");
  for (var i = 0; i < vars.length; i++) {
    var pair = vars[i].split("=");
    params[pair[0]] = pair[1];
  }

  return params;
}
```

## 参考
