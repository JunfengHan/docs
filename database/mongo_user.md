# MongoDB User 相关

## <span style="color:#ffcc99">1.新建用户</span>

#### 1.1 切换数据库

MongoDB 用户管理一般在 admin 数据库里：

```shell
use admin
```

#### 1.2 新建 admin 超级管理用户

超级管理员拥有查看所有数据库的权限，主要用于管理 admin 数据库用户，别用来操作其他数据数据

```shell
db.createUser(
  { user: "admin",
    customData：{description:"superuser"},
    pwd: "xxxxxx",
    roles: [ { role: "userAdminAnyDatabase", db: "admin" } ]
  }
)
```

#### 1.3 新建一个不受访问限制的超级用户

```shell
db.createUser(
    {
        user:"root",
        pwd:"pwd",
        roles:["root"]
    }
)
```

#### 1.4 为某个数据库创建一个数据库，只能管理该数据库

```shell
db.createUser({
    user:"user001",
    pwd:"123456",
    customData:{
        name:'jim',
        email:'jim@qq.com',
        age:18,
    },
    roles:[
        {role:"readWrite", db:"blog"},
    ]
})
```

## <span style="color:#ffcc99">2. 查看/修改用户信息</span>

#### 2.1 查看用户

```shell
# 查看当前数据库用户
show users
# 查看所有用户
db.system.users.find().pretty()
```

#### 2.2 修改信息

```shell
# 修改密码
use admin
db.changeUserPassword("username", "XXX")

# 修改密码和用户信息
db.runCommand(
	{
		updateUser: "username",
		pwd: "xxx",
		customData: {title: "zzz"}
	}
)
```

## <span style="color:#ffcc99">3. 删除用户</span>

```shell
use admin
db.dropUser("username")
```

## <span style="color:#ff661a">注意：</span>

1. 用户管理一般在 admin 数据库下进行，先 use admin
2. 在某个单一数据库下只能对当前数据库的权限进行操作
