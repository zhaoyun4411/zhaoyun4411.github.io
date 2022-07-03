---
title: 微软IIS相关知识
date: 2019-06-21 07:56:24
tags:
  - Web
  - IIS
  - 前端开发
---

## Q1. 将源码直接部署道IIS上面会有什么问题

发布最终程序有两种方式， 一种是编译原始程序，一种是直接在IIS中部署原始程序，这两种都可以在IIS中
运行。其中编译打包后生成的为dll文件，不容易被窃取成果。源程序的话首次访问速度会很慢，因为会进行
编译，第二次访问则正常速度。

<!--more-->

## Q2. 安装IIS并将web程序发布道IIS上面

直接可以通过windows功能安装IIS。

发布网站将：

1. 打开IIS，将web程序添加到IIS，然后对其设置。
2. 添加网站时注意将应用池改为对应的ASP.net版本；物理路径即为准备发布的web程序路径；
3. 选择绑定类型和绑定端口号。
4. 将应用程序池标识改为LocalSystem，启用父路径。选中新建网站，在中间选项的IIS栏下点击ASP，
选择启用父路径为true

## Q3. 队列Queue的链表的实现

用两个变量来记录最早和最后添加的节点。若两个节点相等，Queue为空。每次添加数据更新最后添加的节点。
取数据更新最新添加的节点。

## 参考文献

1. [安装IIS并将web程序发布到IIS上](https://blog.csdn.net/hanchaoqi/article/details/9146407)
2. [队列Queue的链表实现（FIFO)](https://blog.csdn.net/weixin_33896069/article/details/88161471)
