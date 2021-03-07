# mongoDB 入门

## 1. 本地安装 mongoDB

## 2. 启动 mongo 服务

## 3. mongo Shell 操作 mongo 服务

> mongo shell 是 MongoDB 的交互式 JavaScript 接口,我们可以通过 mongo Shell 操作 Mongo 数据库。

### 3.1 连接 本地 mongo 服务

```shell
mongo
```

mongo 命令默认会连接本地的 27017 端口。

```shell
mongo --port 29999
```

连接非默认端口 29999.

### 3.2 连接 远端 mongo 服务

#### 无需身份验证的 MongoDB 实例

```shell
mongo "mongodb://mongodb0.example.com:28015"
```

```shell
mongo --host mongodb0.example.com:28015
```

```shell
mongo --host mongodb0.example.com --port 28015
```

上面 👆3 个命令效果相同。

#### 具有身份验证的 MongoDB 实例

```shell
mongo --username alice --password 123456 --authenticationDatabase admin --host mongodb0.examples.com --port 28015
```

#### 连接到 MongoDB 副本集

```shell
mongo "mongodb://mongodb0.example.com.local:27017,mongodb1.example.com.local:27017,mongodb2.example.com.local:27017/?replicaSet=replA"
```

#### TLS/SSL 连接

指明 <code style="color: #708090; background-color: #F5F5F5;">ssl=true</code> 选项

```shell
mongo "mongodb://mongodb0.example.com.local:27017,mongodb1.example.com.local:27017,mongodb2.example.com.local:27017/?replicaSet=replA&ssl=true"
```

### 3.3 使用 mongo Shell

#### 查看数据库

要列出用户可用的数据库，键入：

```shell
show dbs
```

显示当前使用的数据库，键入 db:

```shell
db
```

默认数据库是 test.

切换数据库，键入 use<db>:

```shell
use <database>
```

#### 新增数据库

```shell
use myNewDatabase
db.myCollection.insertOne( { x: 1 } );
```

use 命令可以切换到一个不存在的数据库；

db.myCollection 是集合（collection）的名称,集合相当于关系数据库中的 表（table）.

db.myCollection.insertOne 会往集合中插入数据，同时会新建集合“myCollection”.

#### 操作数据库

<span style="color: #ff0000; font-size: 16px;">数据库操作主要就是 CRUD（Create 增、Delete 删、Read 查、Update 改）</span>。

[官方详细地址](https://docs.mongodb.com/v4.2/crud/)

[集合操作方法列表](https://docs.mongodb.com/v4.2/reference/method/js-collection/)

**增(C)：**

- db.collection.insertOne()
- db.collection.insertMany()
- db.collection.insert()

_insertOne：_

![mongo crud](../_media/crud_mongodb.svg)

**查(R)：**

查找集合中所有文档：

```shell
db.inventory.find( {} )
```

查找符合条件的文档：

```shell
# 查找匹配
db.inventory.find( { status: "D" } )

# 查找匹配，运用运算符
db.inventory.find( { status: { $in: [ "A", "D" ] }  } )

# 指定 AND 条件
db.inventory.find( { status: "A", qty: { $lt: 30 } } )

# 指定 OR 条件
db.inventory.find( { status: "A", qty: { $lt: 30 } } )

# AND 和 OR 一起用
db.inventory.find( {
  status: "A",
  $or: [ { qty: { $lt: 30 } }, { item: /^p/ } ]
} )
```

**改(U)：**

**删(D)：**

删除操作 remove 就是查找出要删的文档，然后执行删除操作。同时它还能接收选项。

```shell
db.collection.remove(
   <query>,
   {
     justOne: <boolean>,
     writeConcern: <document>,
     collation: <document>
   }
)
```

## 参考

[The mongo Shell --> mongoDB 官网](https://docs.mongodb.com/v4.2/mongo/#working-with-the-mongo-shell)
