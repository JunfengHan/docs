# RESTful API 对比 GraphQL

> 不同的设计模式适应不同的需求。

## 1. 获取资源

打开微信刷朋友圈，打开抖音刷漂亮小姐姐，打开电脑完网络游戏。

上网，就是客户端获取服务器的资源的过程。

todo: 获取资源的方式进化过程

## 2. RESTful 架构

### 2.1 什么是 RESTful

### 2.2 使用 RESTful 架构

## 3. GraphQL 架构

### 3.1 什么是 GraphQL

<code style="color: #708090; background-color: #F5F5F5;">GraphQL</code> 是一个用于 API 的<code style="color: #708090; background-color: #F5F5F5;">查询语言</code>，是一个使用基于<code style="color: #708090; background-color: #F5F5F5;">类型系统</code>来执行查询的服务端运行时（类型系统由你的数据定义）。

从官网的定义可以看出>

- <code style="color: #708090; background-color: #F5F5F5;">GraphQL</code>的<span style="color: #ff0000; font-size: 16px;">类型</span>很重要

- <code style="color: #708090; background-color: #F5F5F5;">GraphQL</code>是一个来执行查询的服务端运行时

一个 GraphQL 服务是通过定义类型和类型上的字段来创建的，然后给每个类型上的每个字段提供解析函数。

例如，一个 GraphQL 服务告诉我们当前登录用户是 <code style="color: #708090; background-color: #F5F5F5;">me</code>，这个用户的名称可能像这样：
_定义类型：_

```js
// Query是保留字段，这里是一个GraphQL API的入口点
type Query {
  me: User
}

// 定义User类型
type User {
  id: ID
  name: String
}
```

_还有每个类型上字段的解析函数：_

```js
// 定义字段 me 的解析函数
function Query_me(request) {
  return request.auth.user;
}

// 定义字段 User 的解析函数
function User_name(user) {
  return user.getName();
}
```

一旦一个 GraphQL 服务运行起来（通常在 web 服务的一个 URL 上,如： localhost:3000/graphql），它就能接收 GraphQL 查询，并验证和执行。

接收到的查询<span style="color: #ff0000; font-size: 16px;">首先会被检查确保它只引用了已定义的类型和字段，然后运行指定的解析函数来生成结果</span>。

_一个 GraphQL 查询：_

```js
// 查询 me 的 name 字段
{
  me {
    name
  }
}
```

会产生这样的 JSON 结果：

```js
{
  "me": {
    "name": "Luke Skywalker"
  }
}
```

**结论：**

---

- GraphQL 是查询语言
- GraphQL 使用 SDL 语言来定义它的 schema, 像 REST API 可能会使用 JSON 模式一样
- 接收到查询后会验证类型和字段，然后由指定的解析函数来生成结果
- GraphQL 又是服务端查询的运行时，所以有[各种语言的实现](https://graphql.cn/code/)

### 3.2 GraphQL 的 查询和变更

> 数据的 CRUD 可以概括为查询和变更。

### 3.3 GraphQL 的 Schema 和类型

因为一个 GraphQL 查询的结构和结果非常相似，因此即便不知道服务器的情况，你也能预测查询会返回什么结果。

那为何需要 schema?

> 因为一个关于我们所需要的数据的确切描述很有意义，我们能选择什么字段？服务器会返回哪种对象？这些对象下有哪些字段可用？这便是引入 schema 的原因。

每个数据图都使用一个 schema 来定义它所包含的数据类型。

我们将使用的编写 schema 的语言是 GraphQL 的 模式定义语言（Schema Definition Language,SDL）。

> 因为 schema 直接位于你的应用客户端和你的底层数据服务之间，所以前端和后端团队应该就其结构进行协作。当你开发自己的数据图时，请实践 schema 优先的开发，并在你开始实现你的 API 之前，就 schema 达成一致。

#### 对象类型 (Object types)

GraphQL schema 中大多数定义的都是对象类型。

每种 对象类型 应代表一个应用程序客户端可能需要与之交互的对象。

_定义一个 对象类型：_

```js
type Launch {
  id: ID!
  site: String
  mission: Mission
  rocket: Rocket
  isBooked: Boolean!
}
```

上面代码，定义了对象类型 Lunch，它又一个字段集合。

每个字段都有自己的类型，字段的值是另一个 <code style="color: #708090; background-color: #F5F5F5;">对象类型</code>，或<code style="color: #708090; background-color: #F5F5F5;">标量类型（scalar type）</code>.

默认的标量类型有 5 种：

- ID
- String
- Boolean
- Int
- Float

当然，你也可以 自定义标量类型。

> 字段后面的“!”代表该字段不能为 null,如果是数组后面的如 "[]!"代表数组不能是 null.

上面代码中的 Mission 和 Rocket 也都是对象类型。

_完整的代码可以是类似这样的：_

```js
type Rocket {
  id: ID!
  name: String
  type: String
}

type User {
  id: ID!
  email: String!
  trips: [Launch]!
  token: String
}

type Mission {
  name: String
  missionPatch(size: PatchSize): String
}

enum PatchSize {
  SMALL
  LARGE
}
```

PatchSize 是个 <code style="color: #708090; background-color: #F5F5F5;">enum type</code>，意味着类型为 PatchSize 的字段或参数只能是 SMALL 或 LARGE。

#### 查询类型 (Query type)

我们定义了数据图（data graph）中存在的对象，但是客户端无法请求到它。

因此，我们需要定义 查询对象<code style="color: #708090; background-color: #F5F5F5;">（Query type）</code>。

<span style="color: #ff0000; font-size: 16px;">要把数据图中支持查询的类型（如 Launch）定义为 查询类型的字段。</span>

如：

```js
type Query {
  launches: [Launch]!
  launch(id: ID!): Launch
  me: User
}
```

Query 是个特殊的 GraphQL 类型，我们称它为 根类型（root type）,因为它可以将操作进行映射，而操作代表了查询文档的根节点。

这里的操作指的是对数据的操作，如 SQL 的 CRUD，GraphQL 的操作只有 3 类：

- query: 查询
- mutation: 变更
- subscription: 订阅

### 3.3 一个 GraphQL 示例(NodeJs)

## 4. 如何选择

## 参考

[GraphQL --> GraphQL 官网](https://graphql.cn/learn/)

**Blog**

推荐 👍，浅显易懂 -- [RESTful API 编写指南](https://blog.igevin.info/posts/restful-api-get-started-to-write/)

[理解 RESTful 架构 --> 阮一峰](http://www.ruanyifeng.com/blog/2011/09/restful.html)

[RESTful API 设计指南 --> 阮一峰](https://www.ruanyifeng.com/blog/2014/05/restful_api.html)

推荐 👍，这是个系列文章 -- [What’s GraphQL | 豪翔天下](https://haofly.net/graphql-tutorial-1/)

[GraphQL vs. REST --> Apollo Blog](https://www.apollographql.com/blog/graphql-vs-rest-5d425123e34b/)

[GraphQL 搭配 Koa 最佳入门实践](https://blog.naice.me/article/5abbbf0dca213a7b6b266f06)

[GraphQL 入门介绍 --> 慕课网手记](https://www.imooc.com/article/31233)

[基于 NestJS 快速搭建 GraphQL 服务](https://juejin.cn/post/6854573212454518792#heading-18)
