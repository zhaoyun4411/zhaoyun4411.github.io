---
title: scheme基本语法
tags:
  - scheme
  - guile
  - lisp
date: 2018-06-03 15:14:08
---


这里对前面学习SICP中使用的lisp的语法做一个总结。

<!--more-->

## 安装

可以选择安装MIT-scheme或者安装guile，安装MIT-Scheme可以通过home-brew安装`brew install mit-scheme`。安装guile也可以通过home-brew来进行安装`brew install guile`。

## 变量

### 定义变量

```
(define temp value)
```

### 修改变量的值

```
(set! temp value)
```

## 基本数据结构

* boolean。`#t`表示真 `#f`表示假。（not函数可以对boolean类型数据操作表示取反）
* number。包括整数（ `(define i 100)` ）、实数（ `(define i 3/4)` ）、有理数（ `(define i 3.14)` ）、复数（ `(define i 3+2i)` ）、二进制数（ `(define i #b10100)` ）、八进制数（ `(define i #o567)` ）、16进制数（ `(define i #x78a)` ）等等。
* char。以 ` #\ ` 开始一个字符。（`#\space`表示空格，`#\newline`表示换行）
* 枚举数据类型。`(define a 'xyz)`或`(define a (quote xyz))`
* string。字符串类型，可以通过`(string-length str)`获取字符串的长度、`(string-set! str pos char)`设置字符串某一个位置的字符。
* pair。`(cons a b)`定义成a.b，a为这个pair的car，b为pair的cdr。
* list。列表是在点对上的一种基础形式。
* vector。向量一种元素按照整型索引的过程`(define v #(1 2 3 4))`

## 基本运算

### 类型判断

```scheme
(boolean? *)
(char? *)
(integer? *)
(rational *)
(real *)
(number *)
(symbol? *)
```

### 比较

`eq?`是判断两个参数是否指向同一个对象，如果是才返回#t；`equal?`则是判断两个对象是否具有相同的结构并且结构中的内容是否相同，它用`eq?`来比较结构中成员的数量；`equal?`多用来判断点对，列表，向量表，字符串等复合结构数据类型。

### 算数运算

`+`、`-`、`*`、`/`和`expt`(指数运算)。

### 转换

```scheme
(number->string 123)  ; 数字转换为字符串
"123"
(string->number "456")  ; 字符串转换为数字
456
(char->integer #\a)   ;字符转换为整型数，小写字母a的ASCII码值为96
97
(char->integer #\A)  ;大写字母A的值为65
65
(integer->char 97)  ;整型数转换为字符
#\a
(string->list "hello")   ;字符串转换为列表
(#\h #\e #\l #\l #\o)
(list->string (make-list 4 #\a)) ; 列表转换为字符串
"aaaa"
(string->symbol "good")  ;字符串转换为符号类型
good
(symbol->string 'better)  ;符号类型转换为字符串
"better"
```

