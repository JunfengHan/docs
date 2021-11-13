# <span style="color:#ffcc99">Git submodule</span>

### <span style="color:#ffe6cc">1.1. 什么是子模块</span>

> 子模块指的是 Git 仓库作为另一个 Git 仓库的子目录。可以将另一个仓库克隆到自己的项目中，同时还保持提交的独立。

- 子模块是一个完整的 git 仓库

### <span style="color:#ffe6cc">1.2. 使用子模块</span>

将 project 仓库添加子模块仓库

```bash
$ git submodule add https://github.com/...
```

这样你的项目会添加一个字模块目录[sub]和[.gitsubmodules] 文件，<span style="color:#ff661a">实际上，Git 做的更多，例如，他会在 ".git/config" 保存对子模块的记录，在 ".git/modules"中保存每一个子模块的“.git” 仓库</span>：

```bash
# git status 会列出文件夹记录
$ git status
-------------------------------
On branch master
Your branch is up to date with 'origin/master'.

Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
	new file:   .gitmodules
	new file:   sub
----------------------------------
```

![截屏2020-06-06 下午2.07.09](/Users/han/Desktop/souce/截屏 2020-06-06 下午 2.07.09.png)

.gitsubmodules 文件内容是这样的

```
[submodule "sub"]
	path = sub
	url = git@github.com:JunfengHan/sub.git
```

.gitsubmodules 文件确保了拉取 project 项目时可以正确获取所依赖的子模块：

```
// 假如你拉取了project 项目
$ git clone git@github.com:JunfengHan/project.git
// 你需要初始化字模块
$ git submodule init
// 然后更新子模块,这样所有的子模块会被拉取并更新到project 项目中
$ git submodule update
```

#### 值得注意的是：

- sub 虽然是工作目录里的一个子目录，但 Git 还是会将它作为一个子模块
- 当你不在那个目录中时，Git 并不会跟踪它的内容，而是<span style="color:#ff661a">将它看作该仓库中的一个特殊提交</span>

```bash
$ git diff --cached --submodule
---------------------------------
diff --git a/.gitmodules b/.gitmodules
new file mode 100644
index 0000000..3ef1eaa
--- /dev/null
+++ b/.gitmodules
@@ -0,0 +1,3 @@
+[submodule "sub"]
+       path = sub
+       url = git@github.com:JunfengHan/sub.git
Submodule sub 0000000...5925ffb (new submodule)
-----------------------------------
```

我们提交一下代码：

```bash
$ git commit -am "added sub module"
-------------------------------------------
[master a92567f] added sub module
 2 files changed, 4 insertions(+)
 create mode 100644 .gitmodules
 create mode 160000 sub
-------------------------------------------
```

注意 ⚠️：

sub 记录的 160000 模式。这个 Git 的一种特殊模式，它本质上意味着你时将一次提交作为一项目录记录的，而非将它记录成一个子目录或一个文件。

### <span style="color:#ffe6cc">1.3. 克隆含有子模块的项目</span>

克隆含有子模块的 project 项目：

```bash
$ git clone git@github.com:JunfengHan/project.git
```

![截屏2020-06-06 下午2.37.06](/Users/han/Desktop/souce/截屏 2020-06-06 下午 2.37.06.png)

进入 sub 目录看看有 哪些文件：

```bash
➜  sub git:(master) ll
---------------------------------
---------------------------------
```

<span style="color:#ff661a">sub 目录是空的</span>。上面已经说了，我们 commit 时 sub 的记录类型是 160000 模式，并非一个字目录或一个文件，所以里面是空的。<span style="color:#ff661a">因为主项目是通过在 ".git" 保存子模块的配置信息来实现版本管理的</span>。

只有初始化子模块并更新它，sub 的内容会被拉取到 sub 目录中：

```bash
// 进入sub 字目录
$ git submodule init
---------------------------------
Submodule 'sub' (git@github.com:JunfengHan/sub.git) registered for path './'
---------------------------------

➜  sub git:(master) git submodule update
---------------------------------
Cloning into '/Users/han/Desktop/test/tetetete/project/sub'...
Submodule path './': checked out '5925ffbab7db48cb037d35cb804a98a1056cbd81'
---------------------------------
```

再来看看 sub 目录：

```bash
➜  sub git:(master) ll
---------------------------------
total 8
-rw-r--r--@ 1 han  staff     5B  6  6 14:46 README.md
---------------------------------
```

sub 项目里的内容都被拉取下来了。

git clone 的 --recursive 选项会递归拉取所有子模块：

```bash
$ git clone --recurse-submodules git@github.com:JunfengHan/project.git
```

### <span style="color:#ffe6cc">1.4. 在含有子模块的项目上工作</span>

我们有了一份包含子模块的项目的副本，我们将会同时在主项目和子模块项目上与队员协作。

#### <span style="color:#ffe6cc">1.4.1 拉取上游修改</span>

使用子模块最简单的模型就是只是用子项目，并不时地获取更新，而不去做任何修改。

来看个例子：

假如我们的子模块有更新了，在本地项目中，直接进入子模块，fetch 代码更新，并合并更新：

```bash
$ cd sub
$ git fetch
---------------------------------
➜  sub git:(5925ffb) git fetch
remote: Enumerating objects: 4, done.
remote: Counting objects: 100% (4/4), done.
remote: Compressing objects: 100% (2/2), done.
remote: Total 3 (delta 0), reused 3 (delta 0), pack-reused 0
Unpacking objects: 100% (3/3), done.
From github.com:JunfengHan/sub
   5925ffb..f4913dd  master     -> origin/master
---------------------------------

$ git merge origin/master
---------------------------------
➜  sub git:(5925ffb) git merge origin/master
Updating 5925ffb..f4913dd
Fast-forward
 test.js | 1 +
 1 file changed, 1 insertion(+)
 create mode 100644 test.js
 ---------------------------------
```

嫌手动更细麻烦，还可以自动抓取并更新：

```bash
# 更新子模块 sub
git submodule update --remote sub
```

自动获取的是默认 master 分支，我们可以设置为想要的其他分支，如想要 sub 子模块跟踪仓库的 “stable” 分支，在 .gitmodules 设置：

```bash
# 添加跟踪分支 stable
[submodule "sub"]
	path = sub
	url = git@github.com:JunfengHan/sub.git
	branch stable
```

#### <span style="color:#ffe6cc">1.4.2 在子模块上工作</span>

通常你在主项目上工作，子模块无法满足你的需求时，你可能同时还需要修改子模块的代码。

目前为止，我们在子模块运行 git submodule update 获取最新的子模块修改。

<span style="color:#ff661a">但是会将子库留在一个称作“游离的 HEAD”的状态。这意味着没有本地工作分支（如“master”）跟踪改动，所以你做的任何修改都不会被跟踪。</span>

我们来把子模块设置一下，使它更容易进入并修改：

1. 进入子模块并检出其相应的工作分支

   ```bash
   $ cd sub
   # 从远端的stable 分支检出本地分支 stable
   $ git checkout -b stable remotes/origin/stable
   ```

2. 进入到项目工作目录，更新子模块

   ```bash
   $ cd ..
   # 更新并合并子模块的远端分支
   $ git submodule update --remote --merge
    ---------------------------------
   remote: Enumerating objects: 5, done.
   remote: Counting objects: 100% (5/5), done.
   remote: Compressing objects: 100% (2/2), done.
   Unpacking objects: 100% (3/3), done.
   remote: Total 3 (delta 0), reused 3 (delta 0), pack-reused 0
   From github.com:JunfengHan/sub
      f719be7..b8d9328  stable     -> origin/stable
   Already up to date.
   Submodule path 'sub': merged in 'f4913ddec2f835703da9f0d5c3218e75830bcdf3'
    ---------------------------------
   ```

让我们来对子模块做些改动并提交：

```bash
$ cd sub
# 新建一个文件，并保存文件
$ vi log.js
$ git add .
$ git commit -m "add log.js"
```

辛辛苦苦修改了 sub,我们提交吧，让小伙伴看看我写的 bug:

```bash
$ git push origin stable
 ---------------------------------
 To github.com:JunfengHan/sub.git
 ! [rejected]        stable -> stable (non-fast-forward)
error: failed to push some refs to 'git@github.com:JunfengHan/sub.git'
hint: Updates were rejected because the tip of your current branch is behind
hint: its remote counterpart. Integrate the remote changes (e.g.
hint: 'git pull ...') before pushing again.
hint: See the 'Note about fast-forwards' in 'git push --help' for details.
 ---------------------------------
```

提交失败了，why?

上面的信息清晰地告诉我们，hint: Updates were rejected because the tip of your current branch is behind

是的，我们当前的分支落后了。

在 sub 里 fetch 抓取远程的更新，然后看看 git 历史时这样的:

![截屏2020-06-06 下午5.50.38](/Users/han/Desktop/souce/截屏 2020-06-06 下午 5.50.38.png)

- sub 当前所在的是 stable 本地分支，提示我们超前一个版本/落后一个版本
- 超前一个版本是因为我们 commit 了一下（added log.js），
- 落后一个版本是因为我们的小伙伴有人更新了远端的 origin/stable 分支（update stable.js）
- 很显然，如果我们想要 push 我们的 stable 分支来更新远端的 origin/stable,首先我们的代码必须是最新的，因此需要先 pull 一下远端的分支，拉取最新的分支，并合并到本地

git pull 之后，project 的子模块 sub 的历史变成了这样，超前了两个版本：

最好不要 git pull, 而是 git fetch 后手动 merge，因为 git pull 的自动合并可能带来合并错误的麻烦。

![截屏2020-06-07 上午12.24.05](/Users/han/Desktop/souce/截屏 2020-06-07 上午 12.24.05.png)

（ 如果我们在主项目中提交并推送，但是并没有推送子模块上的改动，其他尝试检出我们修改的人会遇到麻烦，因为他们无法得到依赖的子模块的改动，那些改动只存在于我们的本地拷贝中。）

再次推送我们的本地修改到远端：

```bash
$ git push origin stable
 ---------------------------------
Enumerating objects: 7, done.
Counting objects: 100% (7/7), done.
Delta compression using up to 16 threads
Compressing objects: 100% (4/4), done.
Writing objects: 100% (5/5), 576 bytes | 576.00 KiB/s, done.
Total 5 (delta 1), reused 0 (delta 0)
remote: Resolving deltas: 100% (1/1), done.
To github.com:JunfengHan/sub.git
   b8d9328..091019f  stable -> stable
 ---------------------------------
```

此时，我们的本地子项目 stable 分支 和 远端的 stable 分支是同步的，既没有超前，也没有落后。

![截屏2020-06-07 上午12.33.33](/Users/han/Desktop/souce/截屏 2020-06-07 上午 12.33.33.png)

子项目修改完了，也做了同步，来看看我们的工作目录：

```bash
➜  project git:(master) ✗ git status
---------------------------------
On branch master
Your branch is up to date with 'origin/master'.

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
	modified:   sub (new commits)

no changes added to commit (use "git add" and/or "git commit -a")
---------------------------------
```

可以看到，我们的主项目中显示 sub 子模块有修改，并建议我们 stage 这些修改，

为了让修改生效，我们在主项目中需要提交它到仓库中：

```bash
$ git commit -a -m "update submodule"
$ git push origin master
# 为了防止忘记推送子模块而导致其他人无法检出我们的提交，可以加个限制参数，如果忘记push 子模块会使主项目推送失败
$ git push --recurse-submodules=check
```
