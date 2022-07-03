---
layout: linux
title: 退出telnet的方法.md
tags:
  - telnet
  - stm32
  - linux
date: 2018-05-24 21:05:39
---


近期在linux环境中开发STM32，需要使用openOCD创建仿真环境，这里需要telnet一个本地端口进行监听，这里先输入命令`^C + ]`。退出telnet环境，再在控制台中输入`quit`既可以关闭创建的端口。
