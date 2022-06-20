# 阿里云 Ecs 配置

### 1. 设置 SSH 客户端连接保持回话时间

[Linux 实例如何设置 SSH 客户端的会话连接保持时间--aliyun](https://help.aliyun.com/document_detail/38055.html)

```bash
# 编辑/etc/ssh/sshd_config配置文件
vim /etc/ssh/sshd_config
		# 修改连接回话保持时间（以s为单位）
		ClientAliveInterval 60
		# 修改允许超时次数
		ClientAliveCountMax 300
# 保存退出后，重启 sshd 配置服务
systemctl restart sshd.service
```

### 2. 安装 zsh 和 ohmyzsh

再好的 Blog 也没有官方文档香啊：👉 (ohmyzsh)[https://github.com/ohmyzsh/ohmyzsh]

概括一下官网的安装过程：

1. 安装条件

   - 先安装 Zsh。ohmyzsh 是为 Zsh 服务的；`zsh --version` 查看是否安装；

     如果没安装，看官方介绍，[install zsh](https://github.com/ohmyzsh/ohmyzsh/wiki/Installing-ZSH#how-to-install-zsh-on-many-platforms)

     ```bash
     # Cent OS 安装
     sudo yum update && sudo yum -y install zsh
     ```

   - `curl` 或 `wget`要安装，不然怎么下载资源呢？

   - `git`也要安装，推荐 V2.4.11 及以上

2. 基本安装（安装后就可以使用 on-my-zsh 了）

   ```bash
   # 运行 sh 脚本，使用 wget 执行安装
   sh -c "$(wget -O- https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
   ```

   安装后你的 Shell 工具会被替换成 `Zsh`;

3. 使用 Oh my Zsh

   **使用插件：**

   - 安装插件，使用 yum 安装，或者 直接 git clone 到 `$ZSH_CUSTOM/plugins/zsh-autosuggestions`目录

   ```bash
   # 1. yum安装 autojump 插件
   yum install autojump-zsh
   # 2. 编辑 zsh配置文件
   vi ~/.zshrc
   		# 在 plugins 中查看相关插件,插件使用空格隔开；如下，添加 autojump插件
   		plugins=(git autojump)
   		[[ -s ~/.autojump/etc/profile.d/autojump.sh ]] && . ~/.autojump/etc/profile.d/autojump.sh

   # 3. 执行一下 .zshrc , 使修改生效
   source ~/.zshrc
   ```

   Git 方式安装：

   ```bash
   # 1. 安装插件 zsh-autosuggestions （自动补全命令，非常好用）
   git clone git://github.com/zsh-users/zsh-autosuggestions $ZSH_CUSTOM/plugins/zsh-autosuggestions
   # 2. 编辑 zsh
   vi ~/.zshrc
   		# 在 plugins 中查看相关插件,插件使用空格隔开；如下，添加 autojump插件
   		plugins=(git autojump zsh-autosuggestions)
   # 3. 使修改生效
   source ~/.zshrc
   ```

   一个很不错的 Blog，[zsh+on-my-zsh 配置教程指南](https://segmentfault.com/a/1190000013612471)

### 3. 安装 Docker

[阿里云 ECS 安装 Docker](https://help.aliyun.com/document_detail/187598.html)
