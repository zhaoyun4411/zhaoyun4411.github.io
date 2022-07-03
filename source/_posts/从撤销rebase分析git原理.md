---
title: 从撤销rebase分析git原理
tags:
  - rebase
  - git
date: 2018-06-30 21:04:52
---


假设我们有两个分支master和dev，他们都有一个相同的提交，如下图所示：

<!--more-->

```
      A---B---C dev
     /
D---E---F---G master
```

这时我们可以从master分支将代码rebase到dev分支上面，平时开发时会遇到这样的情况
，假设dev1和dev2是两个独立的开发分支，但是不小心被我们错误的rebase了代码。现在
相当于两个feature分支中原本独立的业务被揉起来，这里选择利用reflog命令。

## reflog

输入命令`git reflog` 可以看到下面日志。

```
42f7f74 (HEAD -> dev) HEAD@{0}: checkout: moving from master to dev
c174ff5 (master) HEAD@{1}: commit: a
42f7f74 (HEAD -> dev) HEAD@{2}: rebase finished: returning to refs/heads/master
42f7f74 (HEAD -> dev) HEAD@{3}: rebase: checkout dev
771c881 HEAD@{4}: checkout: moving from dev to master
42f7f74 (HEAD -> dev) HEAD@{5}: commit: b
0d89b15 HEAD@{6}: commit: a
771c881 HEAD@{7}: checkout: moving from master to dev
771c881 HEAD@{8}: commit (initial): 1111
```


分析上面日志如下：
	1. HEAD@{0}: 切换到dev分支
	2. HEAD@{1}: 在master分支上提交a
	3. HEAD@{2}: rebase结束
	4. HEAD@{3}: rebase dev分支到master分支
	5. HEAD@{4}: 从dev分支切换到master分支
	6. HEAD@{5}: 在分支上提交记录b
	7. HEAD@{6}: 在分支上提交记录a
	8. HEAD@{7}: 切换到dev分支
	9. HEAD@{8}: 初始化代码仓库

如果想撤销上面的rebase记录，只要输入命令`git reset --hard HEAD@{3}`即可。

