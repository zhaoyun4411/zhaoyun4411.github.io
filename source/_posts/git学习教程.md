---
title: git学习教程
tags:
  - github
  - code
date: 2018-04-19 22:58:21
---


github是一个只要是写代码就绕不开的问题，这里将在这篇博客对其的一些基本操作作一些描述。

<!--more-->

## github简介

git是一种免费、开源的版本控制系统，而github是一个主要提供基于git的代码托管的平台。

## 加入github

### 注册github

进入[github官方网站](https://github.com/)，注册「Sign Up」一个账号，后面可以选择两种个人方案，其中一种是全部为公共仓库的方案，另外一种为可选择私有仓库的方案（每月7刀），一般对于个人选择免费的共有仓库方案即可。

### 认识github

注册成功后就会跳转到github的主页上面。点击`Your profile`菜单既可以进入你的个人github主页。

### 设置你的github

点击`setting`菜单，进入设置页面来设置一些基本信息，如头像、Name、公开的邮箱等等。

### github的基本概念

  * Repository 仓库。即你的项目，如果你想通过github托管一个项目，那就必须新建一个Repository。
  * Issue 问题。当你的项目被别人使用，但是别人在使用过程中对代码的设计流程有疑问，或者认为代码有做的不好的地方，就可以通过此处体出问题，你可以对问题作出解答然后 Close掉。
  * Star 星星。即给你的项目点赞。
  * Fork 分叉。当有人想基于你的项目作出一定的修改的情况下，他可以Fork你的项目，这个时候他的github主页上面就多了一个同样的项目，这时他可以自行修改不会影响到原来的代码结构。
  * Pull Request 发起请求。当别人Fork你的项目之后，作出了一些改进，同时觉得这个改进不错，想要让这个改进让更多人受益，这时候就可以发起一个Pull Request，愿项目的创建人收到这个请求之后review代码，觉得Ok后就会接受你的请求，这时你的代码将添加到原项目。
  * Watch 观察。当你watch某个项目之后，项目的相关更新你都会收到通知。
  * Gist 当你没有项目，只是想分享一些代码片段，这时Gist就派上用场了。

### 创建自己的项目

点击顶部导航窗口中+号图标既可以快速创建一个新的项目，这时候根据页面指导填写相关的项目名称、项目描述、和简介即可以创建你的第一个Github项目。

## git介绍

git是linux之父linus开发的一种版本控制工具，是一种记录一个或者多个文件内容变化，以便将来查询特定版本修订情况的系统。

### git安装

Mac平台下自带了git无需安装。

### 如何学习git

这里建议不要通过图形化的软件的方式，通过命令行的方式学习git。

### git命令列表

在安装完成git之后可以通过在控制台中输入git的方式来验证是否安装成功。如果提示git用法则表明安装成功。这里可以尝试看一下控制台中提示的用法说明。

> master : 默认分支

> origin : 默认远程版本库

> Head : 默认开发分支

> Head^ : Head的父提交


| git具体命令 | 说明 |
|:----------:|:----:|
| git clone {url} | 克隆远程版本仓库 |
| git init | 初始化本地版本仓库 |
| git status | 查看状态 |
| git diff | 查看变更内容 |
| git add . | 跟踪所有改动文件 |
| git add {file} | 跟踪指定文件 |
| git mv {old} {new} | 文件改名 |
| git rm {file} | 删除文件 |
| git rm --cached {file} | 停止跟踪文件 |
| git commit -m "{commit message}" | 提交更新的文件 |
| git commit --amend | 修改最后一次提交 |
| git log | 查看提交历史 |
| git log -p {file} | 查看指定文件的提交历史 |
| git blame {file} | 以列表的方式查看指定文件的提交历史 |
| git reset --hard Head | 撤销工作目录中所有未提交文件的修改内容 |
| git checkout Head {file} | 撤销指定的未提交文件的修改内容 |
| git revert {commit} | 撤销指定提交 |
| git branch | 显示所有本地标签 |
| git checkout {branch}/{tag} | 切换指定分支或标签 |
| git branch {new-branch} | 创建新分支 |
| git branch -d {branch} | 删除本地指定分支 |
| git tag | 显示所有本地标签 |
| git tag {tagname} | 基于最新代码创建标签 |
| git tag -d {tagname} | 删除标签 |
| git merge {branch} | 合并指定分支到当前分支 |
| git rebase {branch} | 衍合指定分支到当前分支 |
| git remote -v | 查看远程版本库信息 |
| git remote show {remote} | 查看指定远程版本库信息 |
| git remote add {remote} {url} | 添加远程版本库 |
| git fetch {remote} | 从远程库获取代码 |
| git pull {remote} {branch} | 下载代码及快速合并 |
| git push {remote} {branch} | 上传代码及快速合并 |
| git push {remote} :{branch}/{tag-name} | 删除远程标签或者分支 |
| git push --tags | 上传所有标签 |

### 用一个例子说明

首先新建一个目录存放你的代码。
```shell
mkdir temp    # 创建仓库文件夹
cd temp
touch readme.md   # 新建readme.md文件
```

> 提示：在进行任何git操作之前，都需要切换到git仓库对应的文件目录下面

这时候随便输入一个git命令会提示我们不是一个代码仓库。这时需要输入`git init`来初始化代码仓库。初始化代码仓库之后在输入`git status`就可以发现在当前分支下「默认为mater分支」存在没有被跟踪的文件`readme.md`，而且提示你可以使用`git add`去提交你想要提交的文件。

> git init 用来初始化代码仓库
> git status 用来查看状态

这里通过`git add readme.md`命令之后即将代码提交到暂存区之中了，这个时候代码等待被提交，同时这里可以使用`git rm -cached`这个命令去移除这个缓存。接着可以通过输入`git commit -m '{提交说明}'`来将这个文件提交到代码仓库（其中 -m 表示提交信息）。

## 向github提交代码

通过前面一章，相信已经基本熟悉git的相关操作了。这一章将重点介绍如何向github提交第一行代码。

### SSH

当你刚刚注册一个github账号，可以自由的clone其他项目，也可以创建自己的项目，但是无法提交代码，github是基于SSH授权校验来管控提交代码的权限的。所以想向github提交代码第一步是添加ssh key。

### 生成ssh key

这里采用的Mac环境，自带ssh，可以通过在命令行中输入ssh验证是否安装。

接着输入`ssh-keygen -t rsa`命令，意思是通过指定的rsa算法生成密钥，接着连续输入三个回车键，这时就会生成两个文件`id_rsa`（密钥）和`id_rsa.pub`（公钥）。这两个文件在Mac系统的`~/.ssh`目录下。

### github上面添加ssh Key

进入github设置页面，找到最SSH and GPG keys页签，选择New SSH key，将id_rsa.pub公钥文件中的内容复制到key那一栏中，title，填写一个方便您管理的名字，点击Add SSH Key按钮就OK了。

添加成功后可以通过输入` SSH -T git@github.com `来进行测试。

### push & pull

向远程代码仓库提交代码，需要了解到两个命令：

  * push： 当你本地代码更新后，就需要将本地代码推到远程代码仓库，这样本地仓库既可以和本地代码仓库保持同步了。

  ```shell
  git push origin master #将本地代码推送到远程的master分支上面
  ```

  * pull： 当别人提交了代码到远程仓库，这个时候你需要使用这个命令将代码拉下来，保证代码的同步。一般在push代码之前都会先pull代码，这样可以避免冲突。

  ```shell
  git pull origin master #将远程代码更新到本地
  ```

### 提交代码

  1. 首先clone自己的项目，执行命令如下：

  ```shell
  git clone git@github.com:{用户名}/{项目名称}.git
  ```

  2. 通过上面的步骤就可将一个完整的项目复制到本地，这个本地的项目本身已经是一个git仓库，这里只需要在代码仓库的目录下任意修改或添加文件，commit之后既可以push到远程仓库。

  ```shell
  git push origin master
  ```


将设本地已经存在一个git仓库，并且这个仓库已经有了很多次commit记录了，这个时候需要采用另外一种方法来关联github上面的项目。

  1. 在github上面新建一个项目。

  2. 将本地项目与github上面的项目进行关联，切换到项目目录执行如下命令。这里表示添加一个远程仓库，这个远程仓库的地址是git@github.com:{用户名}/{项目名称}.git

  ```shell
  git remote add {远程仓库名称} git@github.com:{用户名}/{项目名称}.git
  ```

  可以通过命令`git remote -v`查看当项目有那些远程仓库。

  3. 可以通过命令`git push {远程仓库名称} {分支名称}`向指定远程仓库的指定分支提交代码。

  > 在提交代码前要通过下面命令关联自己的用户名和邮箱：这些信息会出现在你的commit记录里面。
  > ```shell
    git config —global user.name "{用户名}"
    git config —global user.email "{邮箱}"
    ```
  >
