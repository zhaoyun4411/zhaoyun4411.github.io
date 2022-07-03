---
title: 'C语言中#ifdef和#ifndef的比较'
tags:
  - C
date: 2018-06-20 19:35:14
---


阅读C代码的时候会发现在有些地方会使用`#ifdef`有的地方会使用`#ifndef`，如何区分这两个。

`#ifdef`表示“if the following is defined”；`#ifndef`表示“if the following is not defined”。

根据上面的描述可以推断下面代码会输出："one is defined "

```C
#define one 0
#ifdef one
    printf("one is defined ");
#endif
#ifndef one
    printf("one is not defined ");
#endif
```
