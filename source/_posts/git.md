---
title: git
date: 2020-11-27 20:19:40
categories:
    - 项目
tags:
    - git
---

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



参考地址：https://juejin.cn/post/6844903600976576519

