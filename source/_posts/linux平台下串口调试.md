---
title: linux平台下串口调试
tags:
  - linux
  - uart
  - minicom
  - cutecom
date: 2018-05-28 23:47:30
---


近期工作需要在linux环境下做串口调试，折腾了一番串口调试工具，写下这篇博客作为记录。

<!--more-->

## minicom的安装与使用

### minicom的介绍

linux下面的minicom的功能与window下面的超级终端功能类似，可以通过串口控制外部的硬件设备，适用于linux环境下面通过超级终端对嵌入式设备进行行管理。同样也可以使用minicom对外部设备进行控制。

### minicom的安装

在debain 9的系统环境下`sudo apt-get install minicom`既可以完成对minicom的安装。

### minicom的使用

首先查看串口是否可以被使用，通过对串口发送数据比如对com1口，`echo helloworld > /dev/ttyUSB0`。查看USB设备通过命令`lsusb`，查看串口驱动：`cat /proc/tty/drivers/serial`。例如，在终端输入：`dmesg | grep tty`显示从系统启动到现在串口插入拔出的信息，让后我在电脑上插入一个usb转rs232线，再在终端输入：`dmesg | grep tty` ，会显示：usb 2-2.1:pl2303 converter now attached to ttyUSB0;可以通过此法确定我新插入的串口线对应的串口号。

在控制台下通过组合键Ctrl+A Z可以进入minicom菜单。组合键的用法是：先按Ctrl+A组合键，然后松开这两个键，再按Z键。另外还有一些常用的组合键。

* S键：发送文件到目标系统中;
* W键：自动卷屏。当显示的内容超过一行之后，自动将后面的内容换行。这个功能在查看内核的启动信息时很有用。
* C键：清除屏幕的显示内容;
* B键：浏览minicom的历史显示;
* X键：退出mInicom，会提示确认退出。
* choose: Enter
* CANCLE: ESC

其他的快捷操作参考帮助文档，或在线帮助。

## cutecom的介绍

如果想向win下的有图形操作界面，可以通过下面命令`sudo apt-get install cutecom`安装cutecom就可以像win下的串口助手一样的调试串口了。

