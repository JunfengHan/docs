## <span style="color:#ffcc99">1. 生产环境要哪些工作</span>

- 域名/备案
- 服务器
- 配置服务器应用环境：用户权限/无密码登录/ssl...
- 安装配置数据库（云数据库太贵）
- 项目远程部署发布与更新

_部署之路图：_

![截屏2020-05-11 上午5.13.29](/Users/han/Documents/blogImg/截屏 2020-05-11 上午 5.13.29.png)

- 首先，我们在本地电脑辛辛苦苦把所有代码写好，右下角就是我们开发的应用
- 然后我们需要有一台外网的电脑，也就是服务器，通过 ssh 连接到我们买的服务器
- ssh 连接想要安全，就要加些安全措施，如 IPtables 和 Fail2Ban
- 安全连接服务器后，我们要安装 NodeJs/mongoDB/NGINX/SSL 等必要的软件完成环境搭建
- 搭建好服务器环境，就要把本地代码放到服务器了，我们可以用 ssh/FTP 连接的方式传输过去。也可以把代码部署在一个私有的仓库，让服务器每次去拉取
- 如何拉取呢？当然是使用 git 啦，如何自动拉取呢？PM2 是进程管理的专业软件，会管理我们的进程，实现进程自动化管理
- 可是我们一共有 5 个应用，只有一个服务器，HTTP 的 80 端口给谁呢？给 NGINX，让它来转发更安全的 HTTPS 请求，给 5 个应用分别配置为 3001-3005 端口
- 最后，我们不能让用户直接访问我们的服务器 IP 啊，谁能记得住呢！于是，我们需要取域名服务商那里购买域名，然后把域名指向服务器相应的端口

## <span style="color:#ffcc99">2. 本地运行项目</span>

### <span style="color:#ffe6cc">2.1 创建个简单的静态文件</span>

下面使用静态站点，以便测试部署环境，

创建个目录 deploy ，然后写个静态服务：

```javascript
// app.js
const http = require("http");

const homePage = `
	<!DOCTYPE html>
	<html>
		<head>
			<meta charset='utf-8'>
			<title>NodeJs部署</title>
		</head>
		<body>
			<h1>NodeJs部署练习</h1>
		</body>
	</html>
`;

http
  .createServer((req, res) => {
    res.statusCode = 200;
    res.setHeader("Content-Type", "text/html");
    res.end(homePage);
  })
  .listen(3002, () => {
    console.log("Server Running At 3002");
  });
```

### <span style="color:#ffe6cc">2.2 安装本地环境 Node/mongoDB 等</span>

## <span style="color:#ffcc99">3. SSH 连接到服务器</span>

### <span style="color:#ffe6cc">3.1 购买服务器</span>

### <span style="color:#ffe6cc">3.2 连接到服务器</span>

win 系统可以使用 XShell, macOS 只能使用终端了，Royal 还不错吧。

在 Royal TSX 里可以创建一个文档，然后使用安装插件，使用 Terminal 插件，配置并进行 SSH 连接，使用 File Transfer 进行 FTP 连接。

也可以使用 macOS 自带的终端：

```shell
# ssh 连接服务器  用户名@服务器地址
ssh root@12.32.323.32
```

### <span style="color:#ffe6cc">3.3 无密码登录服务器</span>

#### 1. 使用 ssh-copy-id 把公钥添加到服务器用户目录的 ~/.ssh/authorized_keys 中

```shell
ssh-copy-id -i ～/.ssh/id_rsa.pub root@111.111.111.111 -p 22
```

#### 2. 如果不能无密码登录，需要手动添加 id_rsa

```shell
ssh-add -K id_rsa
```

## <span style="color:#ffcc99">4. 增强服务器安全等级</span>

### <span style="color:#ffe6cc">4.1 修改 ssh 默认端口  </span>

```shell
# 首先备份 ssh 配置文件
sudo cp /etc/ssh/sshd_config /etc/ssh/sshd_config.bak
# 编辑ssh配置文件
sudo vi /etc/ssh/sshd_config
__________________________
Port 22
Port 1122 (新增1122端口)
——————————————————————————
# 使1122 端口生效
sudo semanage port -a -t ssh_port_t -p tcp 1122

# *如果报错：找不到 semanage 命令，先安装
sudo yum -y install policycoreutils-python

# 允许新的端口通过防火墙
sudo firewall-cmd --permanent --zone=public --add-port=1122/tcp
# 重新加载防火墙
sudo firewall-cmd --reload

# 重启ssh 服务
sudo systemctl restart sshd.service
# 阿里云需要取自己配置一个入端口1122
```

### <span style="color:#ffe6cc">4.2 防火墙  </span>

> 注意： firewall-cmd 是 centOS 的一个操作防火墙工具；

查看防火墙状态：

```shell
# 查看firewall 服务状态
sudo firewall-cmd --state
# 查看firewall 状态
sudo systemctl status firewalld
```

启动防火墙：

```shell
sudo systemctl enable firewalld
sudo systemctl start firewalld
```

允许端口通过防火墙：

```
sudo firewall-cmd --permanent --zone=public --add-port=1122/tcp
```

关闭某个端口：

```shell
firewall-cmd --permanent --remove-port=8080/tcp
```

查看端口是否能通过防火墙：

```shell
# 查看端口是否开放
firewall-cmd --query-port=80/tcp

# 查看所有开放端口
sudo firewall-cmd --zone=public --list-ports
```

重新加载防火墙，<span style="color:#ff661a">一定要记得，修改防火墙后要重新加载</span>：

```shell
sudo firewall-cmd --reload
```

关闭防火墙：

```shell
systemctl stop firewalld.service
```

### <span style="color:#ffe6cc">4.3 安装 fail2ban </span>

fail2ban 是一个入侵防御软件，可以缓解服务器上的非法访问。

fail2ban 由 Python 编写，基于 auth 日志工作，默认会扫描所有 auth 日志，如 /var/log/auth.log、/var/log/apach/access.log 等，并禁止带有恶意标志的 IP，比如密码失败太多，寻找漏洞等等标志。

```shell
# 安装 fail2ban
sodu yum install fail2ban
# 打开配置文件
sudo vi /etc/fail2ban/jail.conf
# 重启文件
sudo systemctl restart fail2ban.service
# 查看fail2ban 运作状态
sudo service fail2ban status
```

## <span style="color:#ffcc99">5. 搭建 NodeJs 生产环境</span>

### <span style="color:#ffe6cc">5.1 安装相关软件  </span>

```shell
sudo yum install vim openssl build-essential libssl-dev wget curl git
```

安装 nvm:

```shell
wget -qO- https://raw.githubusercontent.com/nvm-sh/nvm/v0.35.3/install.sh | bash
```

vnm 安装 NodeJs：

```shell
nvm i v12.16.1
```

设置 npm 下载源：

```
npm --registry=https://registry.npm.taobao.org install -g cnpm
```

设置系统文件监控数目：

```shell
echo fs.inotify.max_user_watches=524288 | sudo tee -a /etc/sysctl.conf && sudo sysctl -p
```

### <span style="color:#ffe6cc">5.2 pm2 管理 node 进程  </span>

PM2 是一个 node 进程管理工具.

```shell
# pm2 启动一个进程
pm2 start app.js
# 查看pm2 启动了哪些服务
pm2 [ ls | list | stauts ]

# 查看应用详细信息
pm2 show app
# 查看pm2 实时log
pm2 logs
pm2 restart app_name
pm2 reload app_name
pm2 stop app_name
pm2 delete app_name

```

## <span style="color:#ffcc99">6. Nginx 端口代理和负载均衡</span>

安装 Nginx:

```shell
sudo yum install nginx
```

新建 nginx 配置文件：

```shell
# 进入nginx目录
cd /etc/nginx/conf.d
# 新建配置文件,命名规则是为了好记，一般的nginx配置可以在网上找到
sudo vi shushuo-me-8081.conf
# 修改/etc/nginx 目录下的 nginx.conf 配置文件,查看是否包含刚刚写的配置文件
cd /etc/nginx
vi nginx.conf
————————————————————————————
# 可以看到包含了刚新建的配置文件，所以不需要处理 nginx.conf
include /etc/nginx/conf.d/*.conf;
# 修改nginx欢迎页面默认端口，默认是80端口被占用（将80改为任意不常用的端口，如：8089）
listen 80 default_server;
listen [::]:80 default_server;
————————————————————————————
```

检验 nginx 配置文件是否生效：

```shell
nginx -t
```

重新加载 nginx 配置：

```shell
nginx -s reload
```

启动 nginx:

```shell
# 启动nginx
nginx
# 优雅停止nginx
nginx -s quit
# 强制停止nginx
nginx -s stop

```

```shell
# ** 注意，如果80/443端口不能访问，有可能是防火墙限制，
# 防火墙开启 80/443 端口
sudo firewall-cmd --permanent --zone=public --add-service=http
sudo firewall-cmd --permanent --zone=public --add-service=https
sudo firewall-cmd --reload
```

通过 systemctl 管理 Nginx:

```shell
# 启动nginx
systemctl start nginx
# 查看nginx 服务状态
systemctl status nginx.service
# 停止nginx
systemctl stop nginx
# 重启nginx
systemctl restart nginx
# 修改 Nginx 配置后，重新加载
systemctl reload nginx
# 设置开机启动nginx
systemctl enable nginx
```

## <span style="color:#ffcc99">7. 安装 MongoDB</span>

[MongoDB 社区版官网 centOS 系统安装教程](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-red-hat/)

### <span style="color:#ffe6cc">7.1 MongoDB </span>

tep1: 创建一个文件

```shell
vi /etc/yum.repos.d/mongodb-org-4.2.repo
```

Step2: 粘贴配置文件到配置文件中

```
[mongodb-org-4.2]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/4.2/x86_64/
gpgcheck=1
enabled=1
gpgkey=https://www.mongodb.org/static/pgp/server-4.2.asc
```

Step3: yum 安装

```shell
sudo yum install -y mongodb-org
```

Step4: 检查安装是否正确

```shell
rpm -qa |grep mongodb
rpm -ql mongodb-org-server
```

### <span style="color:#ffe6cc">7.2 启动 MongoDB 服务及常用命令  </span>

启动 MongoDB servier:

```shell
systemctl start mongod.service
```

查看 27017 端口是否成功开启：

```shell
netstat -natp | grep 27017
```

检查数据库是否启动成功：查看 mongod 进程

```shell
ps -aux | grep mongod
```

连接服务：

```shell
# mongo 命令其实是 mongo 127.0.0.1:27017 的简写，如果修改了端口，就不能简写了
mongo
```

**常用命令：**

```shell
# 启动mongod 服务
sudo service mognod start
systemctl start mongod.service

# 关闭mongod 服务
sudo service mongod stop

# 重启mongod 服务
sudo service mongod restart

# 修改mongod配置文件
sudo vi /etc/mongod.conf

```

### <span style="color:#ffe6cc">7.3 远程连接 MongoDB 服务 </span>

修改 mongodb.conf 配置文件：

```shell
sudo vi /etc/mongod.conf

下面是配置中的网络连接部分：
-------------------------------
# network interfaces
net:
  port: 27017
  bindIp: 127.0.0.1  # Enter 0.0.0.0,:: to bind to all IPv4 and IPv6 addresses or, alternatively, use the net.bindIpAll setting.
-------------------------------

```

- 将 127.0.0.1 改为：0.0.0.0， 允许远程连接
- 将 27017 随便改为其他端口，如 27999

开启防火墙 27999 端口：

```shell
# 查看防火墙状态
systemctl status firewalld
# 允许 27999 通过防火墙
sudo firewall-cmd --permanent --zone=public --add-port=27999/tcp
# 记得重新加载防火墙
```

    * 注意！阿里云也要允许相关接口接入

远程连接数据库实例：

```shell
# 文本方式连接
mongo "mongodb://mongodb0.example.com:28015"
# 命令行选项方式连接
mongo --host mongodb0.example.com --port 28015
# 带认证的方式连接
mongo "mongodb://alice@mongodb0.examples.com:28015/?authSource=admin"

mongo --username alice --password 123456 --authenticationDatabase admin --host mongodb0.examples.com --port 28015
```

远程连接数据库副本集：

```shell
# 字符串方式连接到副本集
mongo "mongodb://mongodb0.example.com.local:27017,mongodb1.example.com.local:27017,mongodb2.example.com.local:27017/?replicaSet=replA"

# 命令行选项，把副本集名称放在前面
mongo --host replA/mongodb0.example.com.local:27017,mongodb1.example.com.local:27017,mongodb2.example.com.local:27017
```

TLS/SSL 安全方式连接副本集：

```shell
# 末尾添加 ssl=true
mongo "mongodb://mongodb0.example.com.local:27017,mongodb1.example.com.local:27017,mongodb2.example.com.local:27017/?replicaSet=replA&ssl=true"
```

### <span style="color:#ff661a">远程连接：</span>

```shell
mongo 123.33.22.11:27999
```

> 注意：如果远程连接不上，请连接服务后检查 127.0.0.1:27999 能否连接，如果服务器本地可以连接，远程无法连接说明 端口有问题，需检查防火墙等。

1. 创建用户，设置账号，密码，权限

   ```
   # 使用 admin 数据库
   use admin
   # 创建用户
   db.createUser({user:"root", pwd:"123789", "roles": ["root"]})
   ```

2. 修改 mongodb.conf 文件，启用身份验证

   ```shell
   sudo vi /etc/mongod.conf

   修改 security 配置如下：
   _____________________________
   security:
     authorization: "enabled"
   _____________________________
   ```

3. 重启 mongod 服务

   ```shell
   sudo service mongod restart
   ```

4. 用户认证

   ```
   use admin
   db.auth("root", "123789")
   ```

### <span style="color:#ffe6cc">7.4 创建管理 MongoDB 数据库  </span>

创建一个数据库：

```shell
# use 方法会自动创建不存在的数据库
use blog
```

新建用户：

```shell
# 切换到 admin数据库
use admin
# 认证管理员用户
db.auth('root', '******')
# 新建用户，授权可 读写 blog 数据库
db.createUser({user: 'test', pwd: '123', roles: [{role: 'readWrite', db:'blog'}]})
```

## <span style="color:#ffcc99">8. 安装使用 redis</span>

[How to Install and Secure Redis on Centos7](https://www.digitalocean.com/community/tutorials/how-to-install-secure-redis-centos-7)

### <span style="color:#ffe6cc">8.1 redis 基础  </span>

#### 1. 安装 reids

```shell
# 安装 epel 库
sudo yum install epel-release

# 使用yum 安装redis
sudo yum install redis -y
```

#### 2. 启动 redis

```shell
sudo systemctl start redis.service

# 重启服务
sudo systemctl restart redis.service
```

#### 3. 检查 reids 状态

```shell
sudo systemctl status redis.service
```

#### 4. 检查是否启动

```shell
# 返回 PONY 代表运行成功
redis-cli ping
```

### <span style="color:#ffe6cc">8.2 绑定 redis，防火墙配置  </span>

#### 1. 修改 redis 配置

```shell
sudo vi /etc/redis.conf

------------------------
# 修改绑定IP 为自己的服务器地址
# !! 不能乱改啊，改为服务器IP会无法启动
bind xxxxxx

# 修改默认端口6379
port 27998

# 取消密码注释，自定密码
requirepass xxxxxx
------------------------
```

#### 2. 开启防火墙端口

```shell
# 开启 27998 端口
sudo firewall-cmd --permanent --zone=redis --add-port=27998/tcp

sudo firewall-cmd --reload
```

### <span style="color:#ffe6cc">8.3 连接 redis </span>

#### 1. 本地连接到 redis-server

```shell
redis-cli -h 127.0.0.1 -p 27998
```

#### 2. 操作 redis 数据库

开启认证是无法直接操作的，需要先验证密码

```shell
# 直接操作会报错，因为我们开启了密码认证
set key1 10

# 输入验证密码
auth xxxxxx
```

## <span style="color:#ffcc99">9. 使用 github 仓库部署</span>

### <span style="color:#ffe6cc">9.1 从 github 仓库获取代码  </span>

#### 1. 把服务器的公钥添加到 github

[github 官方生成新 SSH 密钥](https://help.github.com/cn/github/authenticating-to-github/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent)

```shell
# 查看并复制id_rsa.pub
ll ~/.ssh

# 没有id_rsa.pub则生成一个
ssh-keygen -t rsa -C "hanjunfeng1005@gmail.com"

# 复制公钥，添加到github
```

#### 2. 测试从服务器获取 github 仓库代码

```shell
# 在服务器新建代码存放目录
mkdir -p /data/source

# 进入文件存放目录并克隆代码
cd /data/source
git clone "git@github.com:JunfengHan/website.git"
```

巨坑啊：

```shell
# 用户为jun, 目录为 /data/source, 执行下面命令
[jun@mac source]$ git clone git@github.com:JunfengHan/website.git
fatal: 不能为 'website' 创建工作区目录。: ????
# 估计是权限问题，添加sudo 来执行
[jun@mac source]$ sudo git clone git@github.com:JunfengHan/website.git
正克隆到 'website'...
Permission denied (publickey).
fatal: Could not read from remote repository.

Please make sure you have the correct access rights
and the repository exists.
# 这个报错就更离谱了，以为是 ssh-key 没有添加到github，各种找原因。。。。。。
[jun@mac source]$ ssh -vT git@github.com
Hi JunfengHan! You\'ve successfully authenticated, but GitHub does not provide shell access.
# WTF,居然ssh 连接github 测试成功
# 以为是端口问题？阿里云服务器配置问题？。。。。。。
# 各种论坛都是让配置ssh-key的，我都重新删除.ssh 两次了
# 绝望中。。。
# 官方网站看到希望，少年，有问题还是首先官方看看能不能解决啊
https://help.github.com/cn/github/authenticating-to-github/error-permission-denied-publickey
不应对 Git 使用 sudo 命令。
# 一语惊醒梦中人啊，权限问题不能用sudo 解决啊，换个解决权限的方法
# 777 可读/可写/可执行
sudo chmod 777 /data/source
[jun@mac source]$ git clone git@github.com:JunfengHan/website.git
# 成功克隆啊
```

## <span style="color:#ffcc99">10. PM2 部署代码</span>

### <span style="color:#ffe6cc">10.1 添加 pm2 部署配置文件  </span>

[PM2 部署](https://pm2.keymetrics.io/docs/usage/deployment/)

#### 1. 在本地项目根目录新建文件 ecosystem.json

```json
{
  // Applications part
  "apps" : [{
    "name"      : "API",
    "script"    : "app.js",
    "env": {
      "COMMON_VARIABLE": "true"
    },
    // Environment variables injected when starting with --env production
    // http://pm2.keymetrics.io/docs/usage/application-declaration/#switching-to-different-environments
    "env_production" : {
      "NODE_ENV": "production"
    }
  },{
    "name"      : "WEB",
    "script"    : "web.js"
  }],
  // Deployment part
  // Here you describe each environment
  "deploy" : {
    "production" : {
      "user" : "node",
      // Multi host is possible, just by passing IPs/hostname as an array
      "host" : ["212.83.163.1", "212.83.163.2", "212.83.163.3"],
      // Branch
      "ref"  : "origin/master",
      // Git repository to clone
      "repo" : "git@github.com:repo.git",
      // Path of the application on target servers
      // pm2 会自动获取git 仓库代码，放到 /var/www/website 目录
      // production 为任务名，可随便修改
      "path" : "/var/www/website/production",
      // Can be used to give options in the format used in the configura-
      // tion file.  This is useful for specifying options for which there
      // is no separate command-line flag, see 'man ssh'
      // can be either a single string or an array of strings
      "ssh_options": "StrictHostKeyChecking=no",
      // To prepare the host by installing required software (eg: git)
      // even before the setup process starts
      // can be multiple commands separated by the character ";"
      // or path to a script on your local machine
      "pre-setup" : "apt-get install git",
      // Commands / path to a script on the host machine
      // This will be executed on the host after cloning the repository
      // eg: placing configurations in the shared dir etc
      "post-setup": "ls -la",
      // Commands to execute locally (on the same machine you deploy things)
      // Can be multiple commands separated by the character ";"
      "pre-deploy-local" : "echo 'This is a local executed command'"
      // Commands to be executed on the server after the repo has been cloned
      "post-deploy" : "npm install && pm2 startOrRestart ecosystem.json --env production"
      // Environment variables that must be injected in all applications on this env
      "env"  : {
        "NODE_ENV": "production"
      }
    },
    "staging" : {
      "user" : "node",
      "host" : "212.83.163.1",
      "ref"  : "origin/master",
      "repo" : "git@github.com:repo.git",
      "path" : "/var/www/development",
      "ssh_options": ["StrictHostKeyChecking=no", "PasswordAuthentication=no"],
      "post-deploy" : "pm2 startOrRestart ecosystem.json --env dev",
      "env"  : {
        "NODE_ENV": "staging"
      }
    }
  }
}
```

### <span style="color:#ffe6cc">10.2 执行 PM2 启动程序  </span>

```shell
# 创建项目目录
cd /var/www
sudo mkdir -p website

# 修改目录权限
sudo chmod 777 website
```

#### 1. 打开终端，进入本地项目目录

```
cd DeskTop/code/website
```

#### 2. 初始化远端文件夹

```shell
pm2 deploy ecosystem.json production setup

# 查看远端项目文件夹,新建了3个文件
[jun@mac production]$ ls
current  shared  source
```

#### 3. 将本地最新项目推送到 github

```shell
# 查看本地项目仓库 git 状态,如果有更新推送到github
➜  website git:(master) ✗ git status
```

#### 4. 正式部署代码

代码会自动拉取 github 的仓库，并启动 PM2

```shell
pm2 deploy ecosystem.json production
```

注意：有个小坑，官方的“post-deploy”脚本没有自动拉取最新代码，需要手动添加：

```json
// Commands to be executed on the server after the repo has been cloned
"post-deploy" : "npm install && pm2 startOrRestart ecosystem.json --env production"
// 添加 git pull 选项
"post-deploy" : "npm install && git pull && pm2 startOrRestart ecosystem.json --env production"
```

!!! 没问题的话就部署成功了。
