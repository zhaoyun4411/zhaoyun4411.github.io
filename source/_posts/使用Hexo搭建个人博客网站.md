---
title: 使用Hexo搭建个人博客网站
date: 2018-03-25 13:35:20
tags:
  - Hexo
  - github
  - gitpage
  - 博客
---

最近准备安静的学习思考一段时间，并将这段时间学习与思考的内容通过博客记录下来。通过CSDN这些博客网站来搭建个人博客平台感觉存在许多不方便的地方，购买服务器又太贵，了解到可以通过github搭建，所以这里来试一试，并且将相关搭建过程记录如下。

<!--more-->

首先申明本文针对mac用户。文章内容参考[Hexo官方网站](https://hexo.io/zh-cn/)。

## 环境配置
 * Node.js：进入[nodejs官方网站](https://nodejs.org/en/)选择最新的版本直接安装即可。
 * git：mac系统已经自带，如果没有安装可以参考[hexo官方网站](https://hexo.io/zh-cn/)中的方法安装。


### 安装Hexo
当Node.js和git安装好了之后即可以开始安装Hexo。在终端中输入下面命令安装Hexo。

```
sudo npm install -g hexo
```

### Hexo初始化
通过控制台切换到您想要存放博客的目录位置，执行下面命令，初始化Hexo的配置

```
hexo init
npm install
hexo s
```
完成上面操作之后，即完成Hexo的初始化，这时打开浏览器输入下面的网址 http://localhost:4000 既可以看到生成的博客网址。最后需要通过control + c终止程序。

新建完成后指定文件夹下的目录结构如下：(具体含义参考[hexo官方网站](https://hexo.io/zh-cn/)，这里不重复说明)

```
.
|-- _config.yml
|-- package.json
|-- scaffolds
|-- source
|   |-- _drafts
|   └-- _posts
└-- themes
```

## 关联github账号
这里默认阅读这篇文章的人都熟悉github，并能够独立完成github的相关操作。

### 创建代码仓库
这里注意项目名必须为 **github用户名.github.io** 。如您的用户名为temp，则这个仓库需要命名为 **temp.github.io** 。

### 关联并托管个人博客到github
然后进入您的博客目录，找到目录中的 `_config.yml`文件，切换到文件最后的地方，添加如下：

```
deploy:
  type: git
  repository: git@github.com:zhaoyun4411/zhaoyun4411.github.io.git
  branch: master
```
这里需要注意每一个`:` 后面均需要加上空格否则最后的解析会报错。

修改完成后执行`npm install hexo-deployer-git --save
`安装hexo-deployer-git，然后执行下面命令生成代码并推送。
```
hexo clean
hexo g
hexo d
```
当以上命令执行完成之后，在浏览器打开 http://你的用户名.github.io ，能看到和打开 http://localhost:4000 一样的页面。
