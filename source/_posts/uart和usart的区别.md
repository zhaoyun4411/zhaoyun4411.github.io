---
title: uart和usart的区别
tags:
  - 嵌入式
  - 串口
date: 2018-07-21 15:36:53
---


研究stm32的时候发现stm32的串口有uart和usart两种模式的库函数头文件，不太明白两者之间的区别，查阅资料通过这篇博客做个记录。

从名字上面看：

UART表示：universal asynchronous receiver and transmitter通用异步收/发器
USART表示：universal synchronous asynchronous receiver and transmitter通用同步/异步收/发器

这里可以看出usart在总线上面增加了同步功能，也就是usart增加了同步时钟输出的能力。
