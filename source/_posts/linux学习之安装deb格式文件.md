---
title: linux学习之安装deb格式文件
tags:
  - linux
date: 2018-11-08 19:24:21
---

在使用debain的时候大部分软件都可以通过命令`sudo apt-get install XXX`进行安装管理，但是会存在部分的
软件（比如网易云音乐、stream客户端等）并没有包含在linux源里面，需要下载安装包自己手动安装。
这里对安装过程作一个记录如下。

<!--more-->

1. 下载`.deb`格式的安装包。
2. 打开terminal，进入下载目录。
3. 输入命令`sudo dpkg -i {安装包名称（比如这里我是 sudo dpkg -i steam_latest.deb）}`。
4. 出现下面的log表明有一些依赖缺失。
```
dpkg: dependency problems prevent configuration of steam-launcher:
 steam-launcher depends on python-apt; however:
  Package python-apt is not installed.

dpkg: error processing package steam-launcher (--install):
  dependency problems - leaving unconfigured
  Processing triggers for gnome-menus (3.13.3-9) ...
  Processing triggers for desktop-file-utils (0.23-1) ...
  Processing triggers for mime-support (3.60) ...
  Processing triggers for hicolor-icon-theme (0.15-1) ...
  Processing triggers for man-db (2.7.6.1-2) ...
  Errors were encountered while processing:
   steam-launcher

```
5. 这时候通过命令`sudo apt-get install -f`安装缺失的依赖。重复步骤3即可。
