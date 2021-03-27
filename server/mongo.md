# mongoDB 入门

## 1. 本地安装 mongoDB

macOS

使用 Homebrew 安装：

_Tap the MongoDB Home-brew Tap_

```bash
brew tap mongodb/brew

brew install mongodb-community@4.2
```

除了安装二进制文件，安装还创建了如下：

- mongod 配置文件 (/usr/local/etc/mongod.conf)
- 日志目录路径(/usr/local/var/log/mongodb)
- 数据目录路径(/usr/local/var/mongodb)

## 2. 启动 mongo 服务

运行时会使用安装 MongoDB 时创建的配置文件（/usr/local/etc/mongod.conf）, 当然，我们可以自己配置这个文件。

[配置详情](https://docs.mongodb.com/manual/reference/configuration-options/)

- 运行 MongoDB 作为 macOS 服务

  ```bash
  brew services start mongodb-community@4.2
  ```

  停止服务:

  ```bash
  brew services stop mongodb-community@4.2
  ```

- 手动运行 MongoDB 作为一个后台进程

  ```bash
  mongod --config /usr/local/etc/mongod.conf --fork
  ```

  停止 MongoDB 后台服务 -- 本地或认证的连接

  ```bash
  db.adminCommand({ shutdown: 1 })
  ```

  停止 MongoDB 后台服务 -- 副本集

  ```bash
  db.adminCommand({ shutdown: 1, force: true })
  ```

**查看是否有 mongod 程序运行:**

```bash
ps aux | grep -v grep | grep mongod

# 查到pid后，直接关闭mongod进程
kill -pid
```

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
