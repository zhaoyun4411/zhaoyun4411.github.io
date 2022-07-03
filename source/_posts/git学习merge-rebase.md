---
title: git学习merge&rebase
tags:
  - git
  - merge
  - rebase
date: 2018-06-18 16:48:50
---


git merge和git rebase 的作用都是从一个分支获取代码合并到当前分支，但是这两个命令采用不同的工作方式，在我前面介绍git基本命令的博客中( [链接](https://zhaoyun4411.github.io/2018/04/19/git%E5%AD%A6%E4%B9%A0%E6%95%99%E7%A8%8B/#more) )提到，git merge代表合并指定分支到当前分支，git rebase 代表衍合指定分支到当前分支。一直不明白这两个的区别。查找资料写下这篇博客作为记录。

<!--more-->

merge在官方手册中给出的定义是 `git-merge - Join two or more development histories together`，如下面的两个分支使用merge命令，在topic分支上面存在commit记录B和C，在master分支上面存在commit记录F和G运行
命令 `git checkout master` 后将topic分支的代码合并到master分支上面，`git merge topic`即将topic的代码合并到master分支上面。这时master分支上面会产生一个新的commit记录。

```txt
merge前
                     A---B---C topic
                    /
               D---E---F---G master

merge后

                     A---B---C topic
                    /         \
               D---E---F---G---H master
```

rebase在官方手册中的定义是 `git-merge - Join two or more development histories together`, 如下面两个分支上面使用rebase命令，在topic分支上面记录了commit记录B和C，在master分支上面存在commit记录F和G，运行命令`git checkout topic`后将master的代码合并到topic分支上面， `git rebase master`即将master代码合并到topic分支上面。这时提交记录的结构如下。

```txt
rebase前


                     A---B---C topic
                    /
               D---E---F---G master

rebase后

                            A'--B'--C' topic
                            /
               D---E---F---G master
```

> 参考资料来自官方help文档 `git merge --help` `git rebase --help`.

