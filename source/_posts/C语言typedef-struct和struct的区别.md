---
title: C语言typedef_struct和struct的区别
tags:
  - C
date: 2018-11-17 10:31:48
---

## 前言
这里记录一下C语言中 typedef struct 和struct的区别，看代码的时候，一直很迷惑这两声明的区别。

<!--more-->

## typedef解释

C语言允许为一个数据类型起一个新的别名，就像给人起“绰号”一样。

起名字的目的不是为了提高程序的运行效率，而是为了编码方便。例如有一个结构体的名字时stu，这时候如果想要定义一个结构体变量。就需要写作如下：

```C
struct stu stu1;
```
这里可以看出struct似乎是多余的，但是如果不这样书写的话程序又会报错。这时如果这里为struct stu起一个别名 STU 书写起来就会简单很多。

这里可以使用关键字 *typedef* 来为类型起一个新的名字，语法格式如下：
```C
typedef oldName newName;
```

> typedef也可以给数组、指针、结构体等类型定义别名。这里来看一看给一个数组类型定义一个别名
的例子。
```C
typedef char ARRAY20[20];
```
这里表示的是ARRARY20是类型char[20]的别名。它是一个长度为20的数组类型。接着可以使用ARRAY20
定义数组。

### typedef 和 #define 的区别

`typedef` 在表现上类似于 `#define`，但是它和宏替换之间存在一个关键性的区别。正确思考这个
问题的方法就是将 `typedef` 看成一种彻底封装的类型，声明之后就不能再往里面添加别的东西了。

1. 可以使用其他类型的说明符对宏类型做一个扩展，但是对于typedef所定义的类型名却不能这样使用
```C
#define INTERGE int
unsigned INTERGE n;  //没问题

//但是不能如下方法使用
typedef int INTERGE;
unsigned INTERGE n; //错误的使用方法
```

2. 在连续定义几个变量的时候`typedef`能够保证所定义的变量均为同一个类型，而`#define`则无法
保证。

```C
#define PTR_INT int *
PTR_INT p1, p2;
```
编译后得到的其实是
```C
int *p1, p2
```
这使得 p1、p2 成为不同的类型：p1 是指向 int 类型的指针，p2 是 int 类型。
