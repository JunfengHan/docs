## 文件管理

#### 1. 查看文件

```shell
# 将文件从第一行开始连续的将内容输出在屏幕上
cat fileName

# 根据窗口大小，适当的输出文件内容到屏幕,“回车键/空格”翻页
more fileName

# 和more类似，可以使用 pageUp 和 pageDown 翻页
less fileName

```

### 2. 新建文件

### 2. 新建/删除文件夹

```shell
# 新建子目录,不存在就新建
mkdir [-p] dirName

# 删除目录
rm [options] name
	-i 删除前询问
	-f 即使只读也删除，不询问
	-r 将目录及所有档案删除
```

### 3. 查看文件权限

```shell
# 查看当前文件夹下的所有文件权限
ls -l
# 查看文件的权限
ls -l [fileName]
```

## Lunix 系统：

#### 1.查看系统数据盘

```shell
fdisk -l
```

#### 2.查看硬盘使用情况

```shell
df -h
```

#### 3.添加用户

```shell
# 添加用户
adduser jun
# 添加密码
passwd jun
# 增加 sudoers 文件写的权限，默认只读
chmod -v u+w /etc/sudoers
# 修改 sudoers
vi /etc/sudoers
___________________________
root ALL=(ALL) ALL
jun  ALL=(ALL) ALL (增加这一行)
# 保存退出

#删除掉sudoers文件写的权限，只保留读的权限
chmod -v u-w /etc/sudoers

```

#### 4. 切换用户

```shell
su jun
```

#### 5.端口

```shell
# 查看端口监听
netstat -nlp | grep LISTEN

# 查看80端口情况
netstat -nlp | grep 80

# 列出所有端口
netstat -a
netstat -at [所有tcp端口]
netstat -au [所有udp端口]
```

#### 6.进程

```shell
# 查看进程
ps a

# 关闭某个进程
kill PID
```
