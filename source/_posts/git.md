---
title: git
date: 2020-11-27 20:19:40
categories:
    - 项目
tags:
    - git
---

git命令基本都有提示，更有--help强大的查看帮助说明

https://git-scm.com/book/zh/v2

https://www.yiibai.com/git/git-quick-start.html

# Git使用前设置

```shell
# 查看当前的设置
git config -l 
git config --global user.name "username"
git config --global user.eamil "xxemial@gmail.com"
git config --global color.ui true

# 查看git命令
git config --help
git help config
```

# SSH

https://docs.github.com/en/free-pro-team@latest/github/authenticating-to-github/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent

```shell
ssh-keygen -t ed25519 -C "your_email@example.com"
# 一路按 enter 即可

# 切换 ssh地址
git remote set-url origin git@github.com:someaccount.git
```

# 初始化

```shell
# 建立git库
git init
```

# 创建文件到索引区流程

```shell
# 新建一个文件 index.html
# 执行git status查看本地工作文件夹状态
git status
# 可以看到有个提示 use "git add <file>.." to include in what will be committed
#					(红色字体)index.html

# 把文件加到索引区
git add	index.html

# 再次查看本地工作文件夹内容
git status
# 可以看到提示(绿色字体)new file: index.html

# 将索引区内容提交到本地库, -m 后面表示本次递交到本地库的说明
git commit -m "create index.html"

# 查看提交历史, --oneline一行查看，索引号缩略
git log --oneline
# 查看最近的几次
git log -2
git log --oneline -2
# 详细显示
git log -p
git log --help
```

# add

```shell
git add . # 将所有修改添加到暂存区
git add * # Ant风格添加修改
git add *Controller # 将以Controller结尾的文件的所有修改添加到暂存区
git add Hello* # 将所有以Hello开头的文件的修改添加到暂存区
git add Hello? # 将以Hello开头后面只有一位的文件的修改提交到暂存区，如 Hello1.txt
```

# 还原文件

```shell
# 给index.html添加一段内容content...
# 查看文件状态
git status
# modified: index.html 修改过文件

# 恢复文件到以前的状态, 之前添加的内容消失了
git restore index.html
# 或者 git checkout -- index.html

# 如果在checkout之前，修改的内容已经添加到索引区了
# 继续在index.html添加一段内容
# 查看文件状态，红色 modified: index.html
git status
# 加入到索引区
git add .
# 再查看状态，modified: index.html
# 继续执行 restore 或者 checkout， 发现没有效果，无法恢复文件，文件已经加入索引区了
git restore index.html # git checkout -- index.html # 无效

# 将文件从头部退出索引区 staged === cached
git restore --staged index.html # 或 git reset HEAD index.html

# 再次使用恢复文件初始状态, 添加的内容消失
git restore index.html # 或 git checkout -- index.html
```

# 对比改动

```shell
# 比较修改内容 git diff [--cached]
# 先为文件添加一段内容，查看状态，modified: index.html 有修改
git status
# 对比修改的内容，添加了一段内容，对比未添加到索引区之前的
git diff
# 加入索引区
git add .
# 再次查看文件状态，绿色modified: index.html 修改完
git status
# 再次对比改动，没有内容修改，无法比较工作文件夹的修改文件
git diff
# 索引区都能比较
git diff --cached
```

# 文件操作

```shell
# 文件操作
git add [file1 file2 ...] # 指定文件添加到索引区
git add . # 所有目录包括子目录的内容都添加到索引区
git rm <file> [--cached] # 删除文件， 如果文件已经提交到索引区了，需要从索引区里删除，要使用 git rm --cached
git mv # 移动文件，更多的是用来改文件名
```

# reset

​	`git reset --hard HEAD[~n]` n代表从当前尖端指向的位置开始计算，往前第N位

​	{% image commit-reset.png %}

```shell
git reset --hard HEAD # 硬往过去返回最后一次提交
git reset --hard HEAD~
git reset --hard HEAD~n # 根据当前指向的尖端看重置到第几次

# 加入reset错了，想重新设置回去，但有不确定commit_id是多少，可以使用
git reflog [-n] # 查看最近几条操作记录，找到commit对应的id
git reset --hard [commit_id] # 回到commit对应的id那条记录

# 或者使用revert，commit_id的被撤销，产生一个新的commit
git revert -n [commit_id]
# reset 将HEAD指向制定提交，历史记录不会出现放弃的提交记录，但是操作记录reflog还是可以找到
# revert 放弃commit_id的修改，会生成一次新的提交，以前的历史记录都在
# 操作完之后，用git log可以明显看到区别
```

# branch & merge

​	{% image merge.png %}

```shell
# 查看分支
git branch
# 查看本地分支和远程分支
git branch -a
# 创建dev分支，在当前的分支上创建出一条分支, 这里是 master => dev
git branch dev
# 切换到dev分支
git checkout dev
# 提交点东西
git add .
git commit -m "branch dev add some content"

# 准备合并到master分支，先切换到master分支
git checkout master
# 合并分支, 将dev合并到master
git merge dev
# 删除分支 dev
git branch -d dev

# 拉取远程分支并创建本地分支，自动切换到该分支
git checkout -b 本地分支branch_x origin/远程分支名name
```

# tag

```shell
# 版本管理
git tag [1.0.0]
# NNN.nnn.mmm
# N 大版本号（全面改动比较大的时候）
# n 小版本号（增加新功能）
# m bug修正版本号

# 查看版本发布的tag
git show [1.0.0]
```

# 远程仓库

​	`git clone <版本库地址> <本地目录名>`
​	该命令会在本地生成一个目录，与远程主机的版本库同名。如果要指定不同的目录名，可以将目录名作为`git clone`命令的第二个参数

```shell
# 把远程库克隆到本地文件夹, clone会自动添加远程仓库并默认为 origin 
git clone <版本库地址> <本地目录名>
# 推送到远程库, 假如推送 master 分支到 origin => git push origin master
git push [remote-name][branch-name]
# 跟新所有分支
git fetch [remote-name]

# git fetch git://git.kernel.org/pub/scm/git/git.git maint
# 取回orgin主机的master分支
git fetch origin master

# 拉取远程分支并创建本地分支
git fetch origin 远程分支名name:本地分支branch_x

# 取回远程主机的某个分支的更新，再与本地的制定分支合并
git pull <远程主机名> <远程分支名>:<本地分支名>

# 查看远程仓库
git remote show [remote-name]

# 远程仓库重命名, 例如将 gs 重命名为 newgs
git remote rename gs newgs

# 删除远程仓库
git remote rm newgs
```

## fetch

​	`git fetch <远程主机名> <分支名>` , 更新远程跟中分支

```shell
# 从远程 refs/heads/ 命名空间复制所有分支，并将它们存储到本地的 refs/remotes/origin/ 命名空间
git fetch origin

# 取回 origin 主机的 master 分支
git fetch origin master
```

## pull

​	`git pull <远程主机名> <远程分支名>:<本地分支名>`
​	取回远程库某个分支的更新，再与本地指定的分支合并。
​	默认情况下, git pull 是 `git fetch,` `git merge FETCH_HEAD` 的缩写。如果使用`--rebase`，运行`git rebase`而不是`git merge`

```shell
# 要取回origen主机的dev分支，与本地的master分支合并
git pull origin dev:master

# 如果远程分支 dev 要与当前分支合并，冒号后面的部分可以省略，如下命令
git pull origin dev
# 上面命令标识，取回 origin/dev 分支，再与当前分支合并，等同于先执行 git fetch ， 再执行 git merge

# git clone 的时候，所有本地分支默认与远程主机的同名分支，建立追踪关系
# 本地的master分支自动追踪 origin/master 分支
# 存在追踪关系的，git pull 就可以省略远程分支名，执行 git pull origin 即可
# git允许手动建立追踪关系
git branch --set-upstream master origin/dev

# 如果合并需要采用rebase模式，使用--rebase
git pull --rebase <远程主机名> <远程分支名>:<本地分支名>
```

## push

​	`git push <远程主机名> <远程分支名>:<本地分支名>`
​	将本地分支的更新，推送到远程主机，格式与 `git pull` 相似

```shell
# 将本地 master 分支推送到 origin 主机的 master 分支，如果master不存在，则会被新建
git push origin master

# 如果省略本地分支名，则标识删除指定的远程分支，这等同于推送一个空的本地分支到远程分支
git push origin :master
# 等同于, 表示删除 origin 主机的 master 分支
git push origin --delete master

# 强推
git push --force origin

# 将当前分支推送到远程的同名简单方法
git push origin HEAD

# 用本地分支 brahch-3 覆盖远程分支 branch-1
git push -f origin branch-3:refs/branch-1
# 或者
git push origin :refs/branch-1 # 删除远程的branch-1分支
git push origin branch-3:refs/branch-1

# git push 不会推送标签 tag ， 除非使用 -tags 参数
git push origin --tags
# 推送tag
git push origin tag_name
# 删除远程标签
git push origin :tag_name
```

## remote

​	管理一组跟踪的存储库

```shell
# 不带参数，列出已经存在的远程分支, -v 列出详细信息，每个名字后面列出url
git remote [-v]
```



# git忽略文件

`.gitignore`文件中设置忽略的文件   https://git-scm.com/docs/gitignore

```shell
# 例如忽略node包node_modules/, 忽略.log后缀的文件
node_modules/
*.log
```
# 创建新的commit来

# 替换当前commit的尖端(HEAD)

​	假如修改了一段代码，`git commit`, 当前commit的尖端(HEAD)会指向最新`commit`的记录；我想继续添加代码，但不想在`commit`的时候会有多一条`commit`记录，那么可以使用`git commit --amend`来和尖端的`commit`合并创建出一条新的`commit`并替换当前`commit`的尖端，已达到`commit`记录一条的目的。

```shell
# 新建一个文件 first.html, 并添加到索引区，commit
git add .
git commit -m 'new first.html'
# 新的first.html，忘记写内容了，现在补上<h1>first features..</h1>，添加到索引区
git add .
# 更新最后一次提交, 最终只有一个commit， commit --amend 会代替前面commit
git commit --amend
# git终端界面会提示输入内容
new first.html # 这是上一次commit的message，可以修改，最后将以这个message为准，与尖端(HEAD)commit合并，创建一个新的commit来替换尖端(HEAD)commit
# 修改完之后，可以按下ESC+Shift，再按两下Z退出
git log --oneline
# 可以看到，尖端的commit被替换新的commit替换掉了！而且没有两个commit
```

实际上相当于

```shell
$ git reset --soft HEAD^
$ ... do something else to come up with the right tree ...
$ git commit -c ORIG_HEAD
```

想要到达真正意义上的合并`commit`，请参考下面`rebase`

# rebase

​	在另一个分支基础智商重新应用，用于把一个分支的修改合并到当前分支

```shell
git rebase -i  [startpoint]  [endpoint]
git rebase -i HEAD~~

pick：保留该commit（缩写:p）
reword：保留该commit，但我需要修改该commit的注释（缩写:r）
edit：保留该commit, 但我要停下来修改该提交(不仅仅修改注释)（缩写:e）
squash：将该commit和前一个commit合并（缩写:s）
fixup：将该commit和前一个commit合并，但我不要保留该提交的注释信息（缩写:f）
exec：执行shell命令（缩写:x）
drop：我要丢弃该commit（缩写:d）

# 修改完之后，可以按下ESC+Shift，再按两下Z退出
```

​	实例：

```shell
# 现在基于远程分支 origin, 创建一个 mywork 分支
git checkout -b mywork origin
# 添加两个文件，生成两次commit
# 创建 a.html, git add .
git commit -m 'create a.html'
# 创建 b.html, git add .
git commit -m 'create b.html'

# 在这个时候， origin 分支也有其他人在 commit，这就意味着 origin 和 mywork 两个分支各自开发重
# pull 拉取 origin 分支的修改并和当前的修改合并，结果看起来就像一个新的 merge commit
# 如果想让 mywork 分支历史看起来像没有经过合并一样，也可以用 git rebase，如下所示：
git checkout mywork
git rebase origin
# 会把 mywork 分支里的每个 commit 取消掉，并且把它们临时保存为补丁patch，这些补丁放到 .git/rebase目录
# 然后把 mywork，分支更新到最新 origin 分支，最后把保存的补丁应用到 mywork 分支上
# 当 mywork 分支更新之后，它会指向这些新创建的提交commit，而那些老的提交会被丢弃
# 在 rebase 的过程中，也许会出现冲突，git会停止 rebase 并会让你去解决冲突；
# 在解决完冲突后，用 git add 命令去更新这些内容的索引
# 无需执行 git commit只要执行
git rebase --continue
# 这样git会继续应用apply余下的补丁
# 在任何时候，可以用 --abort 参数来终止 rebase 的操作，并且 mywork 分支会回到 rebase 开始前的状态
git rebase --abort
```

