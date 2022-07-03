---
title: linux环境下STM32开发环境搭建
tags:
  - linux
  - STM32
  - cubeMX
  - openOCD
date: 2018-05-27 18:04:35
---


近期将在linux环境上面进行stm32开发，这篇博客将记载开发环境的搭建、如何通过CubeMX创建一个工程、如何编译工程、如何将程序下载到单片机中、以及如何通过ST-link来进行硬件调试。

<!--more-->

## cubeMX的安装于使用

### CubeMX的介绍

cubeMX是一个用来配置STM32代码的工具，它将许多东西封装的比较好，比如stm32的用户手册，硬件的抽象层代码以及中间层的代码等等。同时CubeMX也是一个图形化的配置工具，可以自动生成和配置一些开发初期与芯片底层相关的一些初始化代码。CubeMX的特性如下：(更多关于CubeMX的介绍可以进入[官网](http://www.st.com/en/development-tools/stm32cubemx.html)查看)

	1. 直观的选择STM32微控制器的型号；
	2. 微控制器的图形化配置；
		* 自动处理引脚冲突
		* 动态设定时钟树
		* 可以动态确定参数设置的外围和中间件模式和初始化
		* 功耗预测
	3. C代码的工程生成器覆盖了STM32微控制器编译软件，如IAR KEIL GCC
	4. 可以独立使用或者作为 Eclipse 插件使用。


### 安装

首先需要在官方网站下载CubeMX的安装包，然后执行下面命令。

```
sudo apt-get install java-package     # 需要java环境
sudo apt-get install libncurses5
unzip "en.stm32cubemx(v4.19.0).zip"
file SetupSTM32CubeMX-4.19.0.linux
```

## 编译环境的搭建

因为在linux环境中st-link的支持要优与j-link，这里选择st-link来搭建仿真环境，进入[stlink项目网站](https://github.com/texane/stlink)，运行下面命令可以安装stlink。

```
git clone git@github.com:texane/stlink.git
# 进入文件夹
make release
cd build/Release; sudo make install
```

## 仿真环境的搭建

### 使用openocd建立调试链

```
openocd -f stlink-v2-1.cfg -f stm32l0.cfg -c init -c "reset halt"
```

打开另一个终端

```
telnet localhost 4444
```
可以在这个终端中通过 `flash write_image erase unlock PROJ.bin 0x8000000`烧写程序。

打开另外一个终端

```
arm-none-eabi-gdb
target remote localhost:3333
file $(project_dir)/(build)/$(project_name).elf
```
既可以开始调试代码。

### 安装gef

如果想要在控制台中调试，同时显示出代码的运行轨迹，可以选择安装gef来实现，参考 [项目readme](https://github.com/hugsy/gef)安装gef。安装完成后需要对`~\.gdbinit-gef.py`做下面的修改，消除调试stm32的报错。

```
@@ -1386,13 +1386,13 @@ class ARM(Architecture):

     all_registers = ["$r0   ", "$r1   ", "$r2   ", "$r3   ", "$r4   ", "$r5   ", "$r6   ",
                      "$r7   ", "$r8   ", "$r9   ", "$r10  ", "$r11  ", "$r12  ", "$sp   ",
-                     "$lr   ", "$pc   ", "$cpsr ",]
+                     "$lr   ", "$pc   ", "$xPSR ",]

     # http://infocenter.arm.com/help/index.jsp?topic=/com.arm.doc.dui0041c/Caccegih.html
     # return b"\x00\x00\xa0\xe1" # mov r0,r0
     nop_insn = b"\x01\x10\xa0\xe1" # mov r1,r1
     return_register = "$r0"
-    flag_register = "$cpsr"
+    flag_register = "$xPSR"
     flags_table = {
         31: "negative",
         30: "zero",
@@ -2607,7 +2607,7 @@ def is_arm(filename=None):
 @lru_cache()
 def is_arm_thumb():
     """Checks if `filename` is an ARM (THUMB mode) ELF."""
-    return is_arm() and is_alive() and get_register("$cpsr") & (1<<5)
+    return is_arm() and is_alive() and get_register("$xPSR") & (1<<5)


 @lru_cache()
```

完成了这些即可以完全通过terminal调试stm32了。
