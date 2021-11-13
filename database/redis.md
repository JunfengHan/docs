## <span style="color:#ffcc99">1. MacOs 安装 redis</span>

使用 brew 安装：

```bash
brew install redis
```

检查下 redis 版本：

```bash
redis-server -v
```

启动 redis 服务，会使用默认配置：

```bash
redis-server
```

Redis 命令用于在 redis 服务上执行操作，要在 redis 服务上执行命令需要一个 redis 客户端。Redis 客户端在我们安装的 redis 中：

运行 Redis 客户端，会连接到启动的 redis 服务：

```bash
redis-cli

// 连接到远程服务器
redis-cli -h host -p port -a password
```

检验是否连接成功：PING ，返回 PONG 为连接成功：

```bash
➜  ~ redis-cli
127.0.0.1:6379> PING
PONG
```

查看 Redis 信息：

```
127.0.0.1:6379> INFO
```
