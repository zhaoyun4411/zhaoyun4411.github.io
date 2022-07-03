---
title: SICP学习笔记-SICP简介与环境配置
tags:
  - SICP
  - code
  - 环境配置
  - lisp
  - Scheme
date: 2018-05-03 20:27:08
---

SICP的全称是Structure and Interpretation of Computer Programs，翻译过来叫《计算机程序的构造和解释》。你可以在网上找到官方的电子教材：[官方教程](https://github.com/sarabander/sicp) 、[中文翻译版电子书](https://github.com/huyubing/books-pdf/blob/master/%E8%AE%A1%E7%AE%97%E6%9C%BA%E7%A8%8B%E5%BA%8F%E7%9A%84%E6%9E%84%E9%80%A0%E5%92%8C%E8%A7%A3%E9%87%8A%EF%BC%88SICP%E4%B8%AD%E6%96%87%E7%AC%AC2%E7%89%88%EF%BC%89.pdf) 、[视频教程带字幕](https://www.bilibili.com/video/av8515129/)、[SICP习题集答案](http://sicp.readthedocs.io/en/latest/)。这次我刷的是中文版，先立个flag后面有时间刷完中文版再看看英文官方教材。

> 这里在[github](https://github.com/zhaoyun4411/SICP_exercise)上面建立了一个代码仓库，用来存放书中对应的课后习题集。

<!--more-->

## 环境准备

学习任何一个东西，首先需要准备对应的开发环境，这里参考[下面回答](https://stackoverflow.com/questions/12322434/how-to-install-mit-scheme-on-mac/31601331#31601331)搭建SICP的学习环境。 MIT-Scheme是MIT实现的Scheme的解释器，Scheme是Lisp的一种方言，也是SICP使用的语言。

### 具体安装过程

  1. 根据答案给出的链接下载32位或者64位的版本。
  2. 双击击下载的dmg文件，将“MIT/GNU Scheme”文件拖到Applications的文件夹中。
  3. 对于32位的系统环境，运行下面命令：
```shell
sudo ln -s /Applications/MIT\:GNU\ Scheme.app/Contents/Resources /usr/local/lib/mit-scheme-i386
sudo ln -s /usr/local/lib/mit-scheme-i386/mit-scheme /usr/local/bin/scheme
```
    对于64位的系统环境，运行下面命令：
```shell
sudo ln -s /Applications/MIT\:GNU\ Scheme.app/Contents/Resources /usr/local/lib/mit-scheme-x86-64
sudo ln -s /usr/local/lib/mit-scheme-x86-64/mit-scheme /usr/local/bin/scheme
```
     在输入第一条命令后需要，输入您mac用户的密码。
  4. 这样即完成安装。现在您可以在使用MIT-Scheme通过在您的控制台中输入scheme，进入scheme命令环境。

### 环境使用

打开一个你最喜欢的编辑器，这里我采用的是Atom（安装Scheme插件可以实现语法高亮），编写一段Scheme代码并保存，可以没有后缀名。进入命令行，输入 scheme < {Scheme代码文件名}，即可执行程序。
