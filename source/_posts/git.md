---
title: git
date: 2020-11-27 20:19:40
categories:
    - 项目
tags:
    - git
---

# git常用命令

```shell
# 进入目录 folder
# 建立git库
git init

# 设置基础信息
git config -l # 看一下当前的设置
git config --global user.name "username"
git config --global user.eamil "xxemial@gmail.com"
git config --global color.ui true

# 查看git命令
git config --help
git help config

# 新建一个文件 index.html
# 本地工作文件夹状态确认
git status
# 把文件加到索引区
git add	index.html
# 再次查看本地工作文件夹内容
git status
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

# 给index.html添加一段内容
# 查看文件状态，modified: index.html 修改过
git status
# 恢复文件到以前的状态, 之前添加的内容消失了
git checkout -- index.html
# 如果在checkout之前，修改的内容已经添加到索引区了
# 继续在index.html添加一段内容
# 查看文件状态，红色 modified: index.html
git status
# 加入到索引区
git add .
# 再查看状态，绿色的 modified: index.html
# 无法使用 checkout 恢复文件，因为文件已经加入索引区了
git checkout -- index.html # 无效
# 将文件从头部退出索引区
git reset HEAD index.html
# 再次使用checkout恢复文件初始状态, 添加的内容消失
git checkout -- index.html


# 比较修改内容 git diff [--cached]
# 先为文件添加一段内容，查看状态，红色 modified: index.html 有修改
git status
# 对比修改的内容，添加了一段内容
git diff
# 加入索引区
git add .
# 再次查看文件状态，绿色modified: index.html 修改完
git status
# 再次对比改动，没有内容修改，无法比较工作文件夹的修改文件
git diff
# 索引区都能比较
git diff --cached



# 文件操作
git add [file1 file2 ...] # 指定文件添加到索引区
git add . # 所有目录包括子目录的内容都添加到索引区
git rm [--cached] # 删除文件， 如果文件已经提交到索引区了，需要从索引区里删除，要使用 git rm --cached
git mv # 移动文件，更多的是用来改文件名





# 更新最后一次提交
git commit -m "commit message"
# 追加文件到上一次提交记录，不会产生多一条commit记录
git commit --amend
# 追加文件到上一次提交记录并且添加一条提示信息
git commit -am "commit message" [--amend]

# index.html 添加一行`ok... `, 添加到索引区，commit
git add .
git status # 查看文件状态
git commit -m "create ok..." # 提交
git log -1 # 查看最近一次提交记录信息
# 上面在添加`ok... `的时候，末尾多了个空格，现在删掉空格
# 查看状态，存在修改，添加到索引区
git add .
# git commit -m "remove ", 再用 git log 查看会生成一条commit记录，实际上这点小改动是补充之前commit的缺漏而已，没必要单独commit一条记录出来
# 使用 git commit --amend 追加到上一次提交记录，相当于合并到上一次commit(create ok...)中
git commit --amend






# 返回过去
git reset --hard HEAD # 硬往过去返回最后一次提交
git reset --hard HEAD~
git reset --hard HEAD~n
git reset --hard [commit_id] # 回到commit对应的id那条记录
git reflog [-n num] # 返回未来，-n是最近的几条操作记录




# 分支
# 查看分支
git branch
# 创建dev分支
git branch dev
# 切换到dev分支
git checkout dev

# 合并分支, 指定branch name合并到当前所在的分支
git merge [branch name]
# 删除分支
git branch -d [name]


# 版本管理
git tag [1.0.0]
# NNN.nnn.mmm
# N 大版本号（全面改动比较大的时候）
# n 小版本号（增加新功能）
# m bug修正版本号

# 查看版本发布的tag
git show [1.0.0]





# 把远程库克隆到本地文件夹
git clone [url]
git push [origin][master]
```



# rebase

目前gitdesktop仅支持rebase current branch

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
```



# git忽略文件

`.gitignore`文件中设置忽略的文件

```shell
# 例如忽略node包node_modules/, 忽略.log后缀的文件
node_modules/
*.log
```



https://git-scm.com/docs/gitignore

