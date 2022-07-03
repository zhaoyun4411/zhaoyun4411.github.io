---
title: mac升级系统后无法通过控制台使用git等命令问题分析
tags:
  - mac
date: 2018-10-20 14:28:00
---


最近我的macbook 升级了macOS Mojave，升级完成之后发现控制台里面许多命令
如make，git等都无法使用，在控制台中输入之后会弹报如下的error信息。

```shell
xcrun: error: invalid active developer path (/Library/Developer/CommandLineTools), 
missing xcrun at: /Library/Developer/CommandLineTools/usr/bin/xcrun
```

这个原因是因为重装了Xcode或者是应为升级了系统导致xcode某些工具缺失，可以通过下面命令修复：
```
xcode-select --install
```
