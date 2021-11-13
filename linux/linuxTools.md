# Linux 管理工具

## 1. systemd

> Systemd 是 Linux **系统工具**，用来**启动守护进程**，已成为大多数发行版的标准配置。
>
> systemd 是为 Linux**系统的启动**和**管理**提供一套完整的解决方案。
>
> systemd 包含一组命令，涉及系统管理的方方面面。

<code style="color: #708090; background-color: #F5F5F5; font-size: 18px">systemd</code>很复杂，不太 UNIX。

_systemd 架构：_

![ystemd 架构](./img/systemd_architecture.png)

### 1.1 系统管理命令

- systemctl

Systemd 的主命令，用于管理系统。

```shell
# 查看 systemd版本
systemctl --version

# 重启系统
sudo systemctl reboot

# 关闭系统，切断电源
sudo systemctl poweroff

# CPU停止工作
sudo systemctl halt

# 暂停系统
sudo systemctl suspend
```

- systemd-analyze

命令用于查看启动耗时。

```shell
# 查看启动耗时
systemd-analyze

# 查看每个服务的启动耗时
systemd-analyze blame
```

### 1.2 Unit

> 管理**系统资源**，不同的资源统称为 Unit（单位），如：network.service（LSB: Bring up/down networking）、sys-kernel-debug.mount（Debug File System）等系统资源。

**Unit**分为12种：

1. Service unit: 系统服务
2. Target unit: 多个 Unit 组成
3. Device unit: 硬件设备
4. Mount unit: 文件系统的挂载点
5. Automount unit: 自动挂载点
6. Path unit：文件或路径
7. Scope unit: 不是由Systemd 启动的外部进程
8. Slice Unit：进程组
9. Snapshot Unit：Systemd 快照，可以切回某个快照
10. Socket Unit：进程间通信的 socket
11. Swap Unit：swap 文件
12. Timer Unit：定时器

### Unit 管理

> 对于用户来说，最常用的是下面这些命令，用于启动和停止 Unit（主要是 service）。
>
> 如：systemctl start docker 就是启动 docker.service 这个服务。

```bash
$ sudo systemctl start apache.service

# 立即停止一个服务
$ sudo systemctl stop apache.service

# 重启一个服务
$ sudo systemctl restart apache.service

# 杀死一个服务的所有子进程
$ sudo systemctl kill apache.service

# 重新加载一个服务的配置文件
$ sudo systemctl reload apache.service

# 重载所有修改过的配置文件
$ sudo systemctl daemon-reload

# 显示某个 Unit 的所有底层参数
$ systemctl show httpd.service

# 显示某个 Unit 的指定属性的值
$ systemctl show -p CPUShares httpd.service

# 设置某个 Unit 的指定属性
$ sudo systemctl set-property httpd.service CPUShares=500
```



### 1.3 Target

> Target 就是一个 Unit 组，包含许多相关的 Unit 。

### 1.4 日志管理

> Systemd 统一管理所有 Unit 的启动日志。带来的好处就是，可以只用 journalctl 一个命令，查看所有日志。

[Linux Systemd 入门教程：命令篇](https://www.ruanyifeng.com/blog/2016/03/systemd-tutorial-commands.html)

## Linux 的包管理工具

- yum
- dnf

CentOS 8 自带了 dnf，CentOS7 及其他版本未安装 dnf，需要手动安装：

```bash
yum install dnf -y
```

**dnf**常用命令：

```bash
# 搜索软件
dnf search $package
# 查看软件详细信息
dnf info $package
# 安装软件
dnf install $package
# 升级软件
dnf update $package
# 删除软件
dnf remove $package
# 查看已经安装的包
dnf list installed
# 列出可用软件
dnf repolist
# 检查并升级可用软件包
dnf update
# 查看帮助
dnf help
```



## 参考

[配置yum源为aliyun](https://developer.aliyun.com/article/704987)
