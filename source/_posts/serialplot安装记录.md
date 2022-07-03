---
title: serialplot安装记录
tags:
  - linux
  - serialplot
  - debug
date: 2018-08-04 23:13:36
---

## 前言

调试一些算法希望能够看到信号在时域上面的信号变化情况，安装serialplot实现这个能力，这里记录下serialplot的安装过程．

<!--more-->

## serialplot介绍

![serialplot](https://bitbucket.org/hyOzd/serialplot) 是一个基于QT的软件，用于实时绘制来自串口的数据．主要特性如下：
  * 通过串口获取数据．
  * 二进制格式数据(u)int8,(u)int16,(u)int32,float．
  * CSV格式数据．
  * 同步多通道．
  * 二进制或ASCII格式输出数据．
  * 保存数据截图或者以CSV格式保存数据．

依赖：
　linux 下面需要依赖QT5(包含SerialPort模块，QWT6.1)

## 安装QT5

可以直接通过命令行安装qt5.

```shell
sudo apt-get install qt5-qmake
```

或者参考![官方网站](https://wiki.qt.io/Install_Qt_5_on_Ubuntu)的安装指导，通过下面命令安装：

```shell
wget http://download.qt.io/official_releases/qt/5.7/5.7.0/qt-opensource-linux-x64-5.7.0.run
chmod +x qt-opensource-linux-x64-5.7.0.run
./qt-opensource-linux-x64-5.7.0.run
```
安装完成之后在命令行输入`qmake -v`发现无法查看版本号，这里需要修改qmake的默认路径．运行下面命令，修改默认路径：

```shell
cd /usr/lib/x86_64-linux-gnu/qt-default/qtchooser/
sudo vim default.conf   #将此文件第一行修改为你的qmake路径即可
```

## 安装serialplot

参考官网安装即可：

```shell
hg clone https://hyOzd@bitbucket.org/hyOzd/serialplot
cd serialplot
mkdir build && cd build
cmake ..
make
```

安装过程中出现了有一些文件找不到的问题，这里是因为一些依赖包没有安装的原因．以下面的报错为例说明解决的办法：

```shell
compiling ../../QWT/src/qwt_abstract_scale_draw.cpp
In file included from ../../QWT/src/qwt_abstract_scale_draw.h:13:0,
                 from ../../QWT/src/qwt_abstract_scale_draw.cpp:10:
../../QWT/src/qwt_global.h:13:21: fatal error: qglobal.h: No such file or directory
#include <qglobal.h>
```

通过![Ubuntu Packages Search](http://packages.ubuntu.com)搜索缺省的文件名`qglobal.h`得到的![结果](https://packages.ubuntu.com/search?searchon=contents&keywords=qglobal.h&mode=exactfilename&suite=trusty&arch=any)
．然后安装对应的包即可．


