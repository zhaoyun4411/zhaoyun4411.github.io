---
title: 和我一起写Makefile笔记
tags:
  - linux
  - Makefile
  - github
date: 2018-06-17 20:39:55
---


## 概述
Makefile定义了一系列的规则来指定，那些文件需要先编译那些文件需要后编译，那些文件需要重新编译，甚至进行更复杂的功能操作。

<!--more-->
在C或者C++的编译过程中，将源文件编译成为中间文件的过程叫做编译（compile），将大量的ObjectFile合成可执行文件的过程叫做链接。


## Makefile介绍

### make的工作方式

GUN的make工作时的执行步骤如下：
  1. 读入所有的Makefile；
  2. 读入被include的其他Makefile；
  3. 初始化文件中的变量；
  4. 推导隐讳规则，并分析所有规则；
  5. 为所有的目标文件创建依赖关系链；
  6. 根据依赖关系，决定那些关系要重新生成；
  7. 执行生成命令。

其中1-5为第一个阶段，6-7为第二个阶段。在第一个阶段中如果make的变量被使用了，make会将其展开在使用的位置。

### makefile的介绍

make命令执行时，需要一个makefile文件，用来告诉make命令如何去编译和链接程序。基本规则时，如果这个工程没有被编译过，那么所有为C文件都要被编译并被链接；如果这个工程的某几个c文件被修改，那么我们只编译被修改的c文件，并链接；如果这个工程的头文件被改变了，那么我们需要编译引用了这几个头文件的c文件，并链接目标程序。

make命令执行时，需要一个 makefile 文件，以告诉make命令如何去编译和链接程序。

首先，我们用一个示例来说明makefile的书写规则。以便给大家一个感性认识。这个示例来源于gnu的make使用手册，在这个示例中，我们的工程有8个c文件，和3个头文件，我们要写一个makefile来告诉make命令如何编译和链接这几个文件。我们的规则是：

```makefile
edit : main.o kbd.o command.o display.o \
		insert.o search.o files.o utils.o       /*注释:如果后面这些.o文件比edit可执行文件新,那么才会去执行下面这句命令*/
	cc -o edit main.o kbd.o command.o display.o \
		insert.o search.o files.o utils.o

main.o : main.c defs.h
	cc -c main.c
kbd.o : kbd.c defs.h command.h
	cc -c kbd.c
command.o : command.c defs.h command.h
	cc -c command.c
display.o : display.c defs.h buffer.h
	cc -c display.c
insert.o : insert.c defs.h buffer.h
	cc -c insert.c
search.o : search.c defs.h buffer.h
	cc -c search.c
files.o : files.c defs.h buffer.h command.h
	cc -c files.c
utils.o : utils.c defs.h
	cc -c utils.c
clean :
	rm edit main.o kbd.o command.o display.o \
		insert.o search.o files.o utils.o
```

### makefile规则

```makefile
target ... : prerequisites ...
  command
  ...
  ...
```
这里target是一个可以object file，也可以是一个可执行文件，还可以是一个标签；prerequisites，是要生成target所需要的文件或者是目标；command是make要执行的命令（任意shell命令）。其中当 prerequisites 中有一个以上的文件比target文件的内容要新的话， command中所定义的命令就会被执行。

### makefile里面有什么

Makefile里面主要包含了五个东西：显式规则、隐晦规则、变量定义、文件指示和注释。

	1. 显式规则。说明了如何生成一个或多个目标文件。这是由makefile的书写者明显指出，要生成的文件，文件的依赖文件，生成的命令。
	2. 隐晦规则。由于我们的make有自动推导功能，所以隐晦规则可以让我们比较简略的书写makefile，这是由make所支持的。
	3. 变量定义。类似与C语言中的宏。
	4. 文件指示。包含三个部分，一个是在一个makefile中引用另一个makefile，就像c语言中的include一样；另一个是指根据某些情况指定Makefile中的有效部分；还有一个就是定义一个多行命令。
	5. 注释。Makefile中只有行注释，和unix的shell脚本一样，其注释采用的是 `#` 。


### 引用其他的Makefile

make命令开始时，会寻找include所指出的其他makefile，并将其内容安排在当前位置。就好像C/C++的#include。如果文件都没有指定绝对路径或者说是相对路径的话，make会在当前目录下首先寻找，如果当前目录没有找到，那么make还会在下面的几个目录下寻找。

	1. 如果make执行时有“-I”或者“--include-dir”参数，那么make就会在这个参数所指的目录下面寻找。
	2. 如果目录 <prefix>，存在的话make也会去寻找。


## 书写规则

规则包含两个部分，一个是依赖关系，一个是生成目标的方法。

在makefile中，规则的顺序十分重要，因为Makefile只有一个最终的目标，其他的目标都是被这个目标连带出来的，所以一定要让make知道你的最终目标是什么。一般来说，定义在make中的目标可能很多，但是第一条规则中的目标被确立为最终生成的目标。如果第一条规则中的目标有很多个，那么第一个目标会成为最终目标。

如果命令太长，可以使用` \ `作为换行符。一般来说make会以unix的标准shell，也就是 `/bin/sh` 来执行命令。

### 文件搜寻

```shell
VPATH = src:../headers 
```
表示make会按照“src”和“../headers”的顺序搜索文件。

另外还有一个关键“vpath”

	1. vpath <pattern> <directories> : 为符合模式<pattern>的文件指定搜索目录 <directories>
	2. vpath <pattern> : 清除符合模式<pattern>的文件搜索目录
	3. vpath : 清除所有已经被设置好的文件搜索目录

### 伪目标

“.PHONY”来显式的指明一个目标是伪目标。

### 自动生成依赖关系

“-M”的选项，C/C++编译器提供，自动寻找源文件中包含的头文件，并生成一个依赖关系。

## 书写命令

每条规则中的命令和操作系统中的shell的命令行是一致的，make会按照顺序一条一条的执行命令，每条命令的开头必须以tab键开始。除非命令是在依赖规则的分号后面。在命令行中间的空格或者空行是会被忽略的，但是如果该空格或者空行是以tab键开头，那么make会认为其是一个空命令。

### 显示命令

将`@`放在命令的前面，这条编译命令将在运行的时候不会打印到控制台上。




## 参考资料

 [1]. [跟我一起写Makefile](http://wiki.ubuntu.org.cn/%E8%B7%9F%E6%88%91%E4%B8%80%E8%B5%B7%E5%86%99Makefile:MakeFile%E4%BB%8B%E7%BB%8D)
