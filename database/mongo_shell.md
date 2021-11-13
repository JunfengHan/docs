## mongo Shell

[mongo shell 官方使用文档](https://docs.mongodb.com/manual/mongo/)

mongo shell 作为 MongoDB 服务安装的一部分包含在内。

> 使用终端连接后可以使用 mongo shell 来和 MongoDB 交互。你可以使用 mongo shell 来<span style="color:#ff661a">查询、更新数据</span>，也可以<span style="color:#ff661a">执行管理操作</span>。

### 1. 给MongoDB 实例添加认证方式

修改mongo.conf ，打开认证：

```shell
# 开启认证
vi /etc/mongod.conf
_____________________
security:
  authorization: "enabled"
____________________
# 重启mongo服务
sudo service mongod restart
```



### 2. 使用 mongo shell 工作

显示当前使用的数据库：(默认为 test )

```shell
db
```

切换数据库：

```
use <database>
```

列出当前用户下的数据库：

```
show dbs
```

你可以切换到一个不存在的数据库(不存在就自动创建)，当你第一次在数据库存储时，例如你创建了一个集合，<span style="color:#ff661a">这时MongoDB会创建这个数据库</span>:

```shell
use myNewDatabase
db.myCollection.insertOne({ x: 1 })
```