# git 入门

## 1、 起步

### 1.1、Git基础

git 直接记录快照，而非差异比较
  
### 1.2、Git的三种状态

Git 有三种状态，你的文件可能处于其中之一：

1. 已修改（modified）
  已修改表示修改了文件，但还没保存到数据库中
2. 已暂存（staged）
  已暂存表示对一个已修改文件的当前版本做了标记，使之包含在下次提交的快照中
3. 已提交（committed）
  已提交表示数据已经安全的保存在本地数据库中。
  
**由此引入 Git 项目的三个工作区域的概念：Git 仓库、工作目录以及暂存区域**

*工作目录、暂存区域以及 Git仓库:*

![git 3种状态]()

对三种状态的理解：

* 工作目录其实是对项目的某个版本独立提取出来的内容。这些从 Git 仓库的压缩数据库中提取出来的文件，放在磁盘上供你使用或修改。
* 暂存区域是一个文件，保存了下次将提交的文件列表信息，一般在 Git 仓库目录中。
* Git 仓库目录是 Git 用来保存项目的元数据和对象数据库的地方。从其它计算机克隆仓库时，拷贝的就是这里的数据。

## 2、Git 基础

### 2.1、获取 Git 仓库

#### 2.2.1、将现有项目或目录下所有文件导入到 Git 中

**操作步骤：**
在该项目或目录下执行 git init 命令；（注: git init 后本地的目录就是一个本地git仓库Git repostory ）

如果在一个已经存在的文件夹里初始化Git,应该开始跟踪这些文件并提交；git add 实现对文件的跟踪， git commit 提交：

```shell
git add *.c
git add LICENSE
git commit -m 'initial project version'
```

#### 2.2.2. 第二种是从一个服务器克隆一个现有的 Git 仓库

**操作步骤：**

```shell
// 进入一个文件夹
cd XXX
// 克隆仓库到本地

```

git clone 命令不仅仅是克隆文件，还克隆了服务器上所有的提交信息等，并放入到本地的.git文件中。
    
    -2. 记录每次更新到仓库
        请记住，你工作目录下的每一个文件都不外乎这两种状态：已跟踪或未跟踪。

Figure 2. 文件的状态变化周期
          --1. 首先检查一下文件状态
            git status 命令，检查当前文件状态
        --2. 跟踪新文件/暂存已经修改的文件
            git add <fileName> 命令，将未跟踪的文件放到 stage (暂存区，git add之后到了暂存区)，实现git的跟踪
        --3. 忽略文件
        我们可以创建一个名为 .gitignore 的文件，列出要忽略的文件模式。
        --4. 查看已暂存和未暂存的修改
    git status 命令的输出过于模糊，git diff 将显示工作目录中修改之后未暂存的变化内容。
    git diff --staged  命令，查看已暂存的（git add 就是暂存）将要添加到下次提交里的内容
   --5. 提交更新
    git commit -m "提交的信息" 命令，提交更新到本地仓库，每一次运行提交操作，都是对你项目作一次快照，以后可以回到这个状态，或者进行比较。
   --6. 跳过使用暂存区域，直接提交
    git commit -a   命令，Git 会自动把所有已经跟踪过的文件暂存起来一并提交，从而跳过 git add 步骤
   --7. 移除文件
    git rm + fileName 命令，移除某个文件。要从 Git 中移除某个文件，就必须要从已跟踪文件清单中移除（确切地说，是从暂存区域移除），然后提交。
   
    -3. 查看提交历史
        --1. 列出所有的更新，最近的更新排在最上面
     git log  会按提交时间列出所有的更新
     git log -p -2  会列出最近的两次提交
     git log --stat  会列出简略的统计信息
     git log --pretty=oneline  会把提交信息放在一行显示
  -2. 限制git log 输出长度
             git log --since=2.weeks  会列出最近的
   
     -4. 撤销操作
        在撤销之前应该知道，有些撤销操作是不可逆的。这是在使用Git 的过程中，会因为操作失误而导致之前的工作丢失的少有的几个地方之一。
  --1. 撤销操作
    git commit --amend  会将暂存区的文件提交。如果自上次提交以来你还未做任何修改，（例如，在上次提交后马上执行了此命令），那么快照会保持不变，而你所修改的只是提交信息。例如，你提交后发现忘记了暂存某些需要的修改，可以像下面这样操作：
$ git commit -m 'initial commit'
$ git add forgotten_file
$ git commit --amend
    最终你只会有一个提交 - 第二次提交将代替第一次提交的结果。
   --2. 取消暂存的文件
                 git reset HEAD <file>  会将暂存区的文件状态修改为未暂存的状态。
           -- 3. 撤销对文件的修改
            git checkout --<file>  会将该文件的修改全部撤销，把它还原成最初的样子。注意，这个命令很危险， 除非你确实清楚不想要那个文件了，否则不要使用这个命令。
        
        -5. 远程仓库的使用
            --1. 查看远程仓库
               git remote 会列出你指定的每一个远程服务器的简写。
                   git remote -v 会显示需要读写远程仓库使用的 Git 保存的简写与其对应的 URL。
            --2. 添加远程仓库
              git remote add <myRemote1> <url> 会在本地初始化后的git仓库添加一个新的远程 Git 仓库，同时指定了一个远程仓库的简写为myRemote1, myRemote1将代替url，例如，如果你想拉取这个仓库中有但你没有的信息，可以运行 git fetch myRemote1
              --3. 从远程仓库中抓取与拉取
            git fetch [remote-name] 会从远程仓库中获得你还没有的（例如，其他同事提交的）数据。执行完成后，你将会拥有那个远程仓库中所有分支的引用，可以随时合并或查看。必须注意 git fetch 命令会将数据拉取到你的本地仓库 - 它并不会自动合并或修改你当前的工作。 当准备好时你必须手动将其合并入你的工作。            
             git pull 会从远程仓库自动的抓取数据并自动尝试合并到当前所在的分支。
            --4. 推送到远程仓库
            git push [remote-name] [branch-name] 将本地 branch-name分支推送到对应的远程分支。 当你和其他人在同一时间克隆，他们先推送到上游然后你再推送到上游，你的推送就会毫无疑问地被拒绝。 你必须先将他们的工作拉取下来并将其合并进你的工作后才能推送。
               git push [remote-name] [local-branch-name]:[remote-branch-name] 将新建远程分支，并且将本地 branch-name分支推送到远程分支。
            --5. 查看某一个远程仓库的更多信息
            git remote show [remote-name]  可以查看某一个远程仓库的更多信息。
            --6. 远程仓库的移除与命名
            git remote rename origin origin1 会将远程仓库origin 的名称修改为origin1
            git remote rm origin1 会将远程仓库移除
        
        -6. 打标签
            Git 可以给历史中的某一个提交打上标签，以示重要。例如，用来标记发布结点（v1.0 等）。
             --1. 列出标签
                git tag  会列出已有的标签。
            --2. 创建标签
                git tag -a <tagName> -m 'some message'  其中， -a 选项会创建辅助标签，-m 选项指定了一条将会存储在标签中的信息。如：git tag -a v1.4 -m 'my version 1.4'  创建了一个名为v1.4的辅助标签，备注信息为 'my version 1.4'
                git tag <tagName> 会创建轻量标签。
         
        -7. Git 设置别名
           --1. git config 为Git设置信息
$ git config --global alias.co checkout
$ git config --global alias.br branch
$ git config --global alias.ci commit
$ git config --global alias.st status
  
        -8. 子模块
                --1. git submodule add repository   在项目添加子模块
                --2. git clone --recursive repository  克隆一个项目，同时初始化子项目 
                --3. 克隆仓库后子模块是空的，需要做两个步骤：
                        git submodule init   初始化子模块
                        git submodule update  抓取父项目数据并检出父项目中的合适提交
               --4. git  submodule update --remote --merge 拉取远端子模块并合并到本地 （*注意：拉取的是master分支）
                —5. git push —recurse-submodule=on-demand    将所有子模块的修改一起推送（在修改了自模块后最好这样直接推动，不然要单独推送所有子模块，且要注意分支问题）
                 


三、Git 分支
      
  *Git保存的不是文件的变化差异，而是一系列不同时刻的文件快照
  *Git提交时会保存一个提交对象（包含着指向前树对象的所有提交信息）
  *Commit后Git仓库包含blob对象（保存文件快照）、一个树对象（记录目录结构和blob对象索引）以及一个提交对象

图：首次提交对象及其树结构



修改后再次提交，提交对象会包含一个指向上次提交对象（父对象）的指针




  *Git 的分支，其实本质上仅仅是指向提交对象的可变指针。 Git 的默认分支名字是 master。 在多次提交操作之后，你其实已经有一个指向最后那个提交对象的 master 分支。 它会在每次的提交操作中自动向前移动。

  *Git 有一个名为 HEAD 的特殊指针,指向当前所在的本地分支，可以通过切换HEAD指针来切换本地分支。




  *使用分支意味着你可以把你的工作从开发主线上分离开来，以免影响开发主线


    -1. 分支简介
        https://progit.bootcss.com/#_git_branches_overview，介绍的非常好！
        
         git branch  会列出所有的分支
    -2. 分支创建
        Git 是怎么创建新分支的呢？ 很简单，它只是为你创建了一个可以移动的新的指针。
    git branch <branchName> 会在当前所在的提交对象上创建一个分支。
        

     名为 HEAD 的特殊指针指向当前分支：
     


    git log --oneline --decorate  列出所有的分支和分支指向的提交对象。
        
 -3. 分支切换
        git checkout testing 这样 HEAD 就指向 testing 分支了。
        *** git checkout 命令做了两件事 ***：
            --1：使 HEAD 指向 testing 分支
            --2：将工作目录恢复成 testing 分支所指向的快照内容。也就是说，工作目录里的文件会被改变。
     

        在testing分支再提交一下，HEAD分支随着提交操作向前移动：

     


    
 
    -4. 分支的新建与合并
        4.1 父子分支前后合并
        工作场景再现：
      -> 我在master 分支做开发，突然有个紧急的问题要处理，我新建一个 iss123 分支来专门解决这个问题；
      git checkout -b iss123 会创建一个名为 iss123 的分支，然后切换到该分支
    -> 我在 iss123 分支上开发.......
    -> 在 iss123 分支开发的过程中，又有一个紧急问题，我需要回到之前的master 分支，基于master 分支来新建一个分支
        git checkout master 切换到 master 分支      
        git checkout -b hotfixBranch 新建并切换到 hotfixBranch 分支
    -> 在 hotfixBranch 分支开发并解决问题，确保问题解决了之后，需要将其合并到 master 分支来部署到线上。
     git checkout master 切换到 master 分支
    git merge hotfixBranch 将hotfixBranch 分支合并到 master 分支。
     --: git merge 会将 master 分支指针移动到 hotfixBranch 分支的提交对象，此时，master 分支和 hotfixBranch 分支的提交对象相同，相当于将master 分支快进到这个提交对象，可以在master 分支着手发布修复了。 
         -> 问题已经解决， hotfixBranch 分支已经没有存在的意义了，删了它
    git  branch -d hotfixBranch 删除hotfixBranch 分支
     

         合并操作没有需要解决的分歧——这就叫做 “快进（fast-forward）”：
     


    4.2 非父子分支的三方合并
         切换回你正在工作的分支继续你的工作，也就是针对 #53 问题的那个分支（iss53 分支）：
     


    做了个C5提交，这时需要做个合并，因为master分支不是iss53分支提交的直接祖先，Git会使用两个分支的末端所指的快照（C4和C5）以及两者的祖先（C2），做一个三方合并：

   

    Git 将此次三方合并做了一个新的快照并且自动创建一个新的提交指向它，这个被称作一次“合并提交”，它有两个父提交：
 
    

    
     4.3 合并不相关历史的分支
        有时我们会遇到如下场景：
    我们在本地开发，开发了一段时间后想推送到远程git 仓库做合作开发。于是，我们：
    1.  在本地 git init 初始化本地仓库，这个操作不但初始化了一个本地git 仓库，还默认初始化了一个分支 master
    2.     我们又在远程服务器（github 或其它地方）初始化了一个项目，并且新建了 README.md 文件
    3.     git remote add [remoteName] [url] 添加远程仓库
    4.     git fetch [remoteName] 抓取远程仓库数据
    5.     git merge remoteName/master : 合并没有相关历史的远程分支，这时就会报错‘fatal: refusing to merge unrelated histories  Error redoing merge master’,提示你不能合并不相关历史的分支，Git 2.9.0 默认是不允许的

        解决方案：
            git merge --allow-unrelated-histories origin/master ： 使用命令，允许合并不相关的历史
             
      
     4.4  遇到冲突时的分支合并
    *  如果在两个不同的分支中，对同一个文件的同一部分进行了不同的修改，Git就没办法干净的合并它们；

    *  合并提交遇到冲突时Git会将没冲突的文件暂存，有冲突的文件放到工作区，需要手动来修改冲突的内容；

    *  然后使用 git add 命令对修改做暂存，暂存后Git会将它们标记为冲突已解决；

    *  最后再用git commit 把暂停的提交重新提交一下

    -5. 分支管理
        git branch  会列出所有的分支
               如下：* 表示当前HEAD 指针所指向的分支，如果在这时候提交，master 分支将会随着新的工作向前移动。
$ git branch
iss53
* master
testing
           git branch -v  会列出每一个分支的最后一次提交
            git branch --merged  会列出哪些分支已经合并到当前分支
            git branch --no-merged  会列出哪些分支未合并到当前分支

    -6. 分支开发工作流
      6.1  长期分支
          长期分支如 master 分支，保留完全稳定的代码，可以是已经发布或即将发布的代码；

    还有名为 develop 或者 next 的平行分支，用来做后续开发或者测试稳定性，这些分支不需要绝对稳定，但是一旦达到稳定状态，它们就要合并到 master 分支；

    有些突然需要做的事情，如紧急修改，这时可以用特性分支（端分支，如iss53）解决临时问题，解决好并通过测试后可以删除；

   长期分支的稳定版本一般都比较落后（如下图）：



    通常把他们想象成水流（work silos）会更好理解，通过测试的提交会被筛选到稳定的水流上去：
   
 
  6.2  特性分支
          特性分支是一种短期分支，它被用来实现单一特性或其相关工作。

      6.3  远程分支
    * 我们所说的远程分支大多是远程跟踪分支，远程跟踪分支是远程分支状态的引用。
    * 跟踪远程分支是我们不能移动的本地引用，只有做网络通信操作（git fetch）时,它们会自动移动
    * 远程跟踪分支像是你上次连接到远程仓库时，那些分支所处状态的书签


如图：红色部分为远程跟踪分支
    


        看一个实际例子：
    我们从公司Git服务器使用 git clone命令克隆一个仓库，它做了什么呢？
    1.  自动将该远程仓库命名为 origin,并拉取它的数据，数据不仅包含文件信息，还包含所有的提交记录等（.git文件）
    2.  创建一个指向它的master 分支的指针，并将其命名为 origin/master
    3.  创建一个和origin 的 master 分支指向同一个地址的本地master 分支
     
    如图：使用git clone 命令克隆远程仓库
    

        其他人推送提交到 git.ourcompany.com并更新了master 分支，你在本地也修改了本地的 master分支，只要你不连接到 origin 服务器，你的 origin/master 指针就不会移动：
    如图：远程分支改变，本地的oring/master 不会变化
     


    这时你需要同步你的工作，运行 git fetch origin, 会去拉取git.outcompany.com中本地没有的数据（如其他同事的提交），并更新到本地数据库，移动 origin/master 指针指向新的、更新后的位置：
    如图：git fetch 更新远程仓库引用
     


6.4 推送
   git push: 默认，推送当前分支到远端的同名分支
    git push origin branchName : 推送当前分支到远程仓库的同名远程分支,远程没有该分支的话会新建远程分支
    git push origin branchName1:remoteBranchName : 推送本地 branchName1 分支到远端 remoteBranchName 分支
    git push --set-upstream origin branchName : 推送当前分支且设置远端上游分支

 6.5 跟踪分支
   一般我们合作开发时的流程是：
    git fetch origin : 抓取远程服务器的数据到本地
    git checkout -b dev origin/dev : 从远程跟踪分支origin/dev 检出一个本地“跟踪分支”并切换到该分支，推送时会推送到本地分支所跟踪的远程分支，在该分支运行 git pull 会拉取并合并远程分支到本地跟踪分支
    git checkout --track origin/dev： 和上面的命令一样，--track 是快捷方式，创建一个名称相同的本地跟踪分支
  git checkout -b sf origin/dev : 创建了一个和远程分支不同的本地跟踪分支

            git branch -vv： 查看所有跟踪分支

    *  从一个远程跟踪分支检出一个本地分支会自动创建一个叫做 “跟踪分支”（有时候也叫做 “上游分支”） 
    * 跟踪分支是与远程分支有直接关系的本地分支
    * 当克隆一个仓库时，它通常会自动地创建一个跟踪 origin/master 的 master 分支

    -7. 删除分支
        git branch -d <branchName> 会删除本地分支
      git push <remoteName> --delete <branchName> 会删除远程分支




技巧： 
-1. 解决合并冲突
    发生冲突后命令行会出现提示：CONFLICT标记、分支会变为（xxxx | MERGING）;
    先 git status 查看一下冲突内容;
    然后到编辑器里找到相关文件，对冲突内容做出修改;
    修改之后 add 冲突文件;
    最后 merge -a -m "信息"，这样就合并成功了。
    








附录：Git 中会用到的一些命令
-1.  git config --list
    ：会列出你的git 的所有配置信息

-2.    git help config
    : 获取config命令的手册，无需联网就可使用

-3.    git status
    : 查看当前文件状态
     状态简写:  git status -s （git status --short）
        ??    表示新添加的未跟踪文件
            A     表示新添加到暂存区中的文件前面有
            M     修改过的文件
            MM    出现在右边的 M 表示该文件被修改了但是还没放入暂存区，出现在靠左边的 M 表示该文件被修改了并放入了暂存区

-4.   放弃修改
    --1. 文件未跟踪:
            删除新增文件：rm fileNane / rm dir -rf
            git clean -df 删除所有未跟踪的文件和目录（危险⚠️）
    --2. 文件已跟踪且未暂存（git add），放弃修改： git checkout -- fileName
           放弃所有： git checkout .  ***注意，不可逆的丢失***
    --3. 文件已经暂存（git add）到暂存区，想要放弃： git reset HEAD fileName
           放弃所有暂存文件： git reset HEAD
    --4. 文件已经已经暂存（git add）且已提交（git commit）,想撤销此次commit : git reset commit_id
                撤销commit后文件保留在暂存区：git reset -- hard commit_id
             *可通过 git log 查看 commit_id


-5.    git log
    : 显示commit的日志记录
    git log -p -2 : 最近2次提交的内容差异
    git log --stat : 提交信息简略统计
    git log --pretty : 提交信息一行显
    git log --since=2weeks : 2周内的提交信息


-6.    git remote  
    : 显示远程仓库
    git remote -v : 显示远端详细信息
    git remote add [name] https://github.com/name1: 添加远程仓库，并命名为name1
    git fetch [remote-name] : 从远端仓库抓取本地仓库没有的信息，用于随时合并或查看
    git pull : 拉取并合并远端分支到本地仓库，并合并到已跟踪的分支
    git remote rename [name1] [name2] : 将远端仓库名name1重命名为name2
    git remote rm [name1] : 删除远端仓库
    

-7.    git branch
    : 列出本地分支（不包含跟踪分支）
    git branch -a : 显示本地所有分支
    git branch -v : 显示分支最后一次提交信息
    git branch -vv : 显示分支的跟踪分支












