---
title: C语言printf函数一些说明
tags:
  - c
  - printf
date: 2018-07-21 15:58:07
---


C语言中有时会出现下面的描述`printf("%3d",data)`这里的`%3d`是什么意思了。

`%3d`可以通过下面的语法进行拆分描述：

 * `%`: 表示“在这里打印一个变量”。
 * `3`: 表示最少三个字节的长度输出结果。
 * `d`: 表示这个变量可以是一个整数。

参考资料：
 1. https://stackoverflow.com/questions/2806093/what-does-3d-mean-in-a-printf-statement
 2. http://www.cplusplus.com/reference/cstdio/printf/
