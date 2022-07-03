---
title: modbus学习笔记
tags:
  - modbus
  - stm32
date: 2018-08-04 23:11:22
---

## 前言

ModBus 协议是施耐德电气公司在1979年开发的，是全球第一个真正用于工业现场的总线协议．协议定义了一个控制器能够认识使用的消息结构，而不管他们是经过何种网络进行通讯．它已经成为一种通用的工业标准，有了它，不同厂家生产的控制设备可以连接成工业网络，进行集中监控．
<!--more-->

## modbus简介

Modbus通信协议具有以下的几个特点：

  * Modbus协议标准开放，公开发表并且没有版税的要求．用户可以免费获取并使用Modbus协议．
  * Modbus协议可以支持多种电器接口，如RS232，RS485,TCP/IP等；还可以在各种介质上面传播．
  * Modbus协议消息帧格式简单，紧凑，通俗易懂．

Modbus通讯协议目前存在用于串行链路，TCP/IP以太网以及其他支持互联网协议的网络版本．大多数Modbus设备通信通过串口或TCP/IP物理层进行连接．对于Modbus串行链路连接，它们在协议细节上略有不同，主要区别是传输数据的字节表示上的不同．这两个变种包括RTU模式和ASCII模式．ModbusRTU模式是一种紧凑的，采用二进制表示数据的方式；而ModbusASCII模式是一种人类可读的，冗长的表示方式．

## Modbus事务处理

Modbus协议允许在各种网路体系结构内进行简单的通信，每种设备(包括PLC，HMI，控制面板，驱动程序，动作控制，输入输出设备)都能使用Modbus协议来启动远程操作．在基于串行链路和以太网(TCP/IP)的Modbus上可以进行相同通信．

ModBus是一种请求/应答协议，并且提供统一的功能码用于数据传输服务．Modbus功能码是Modbus请求应答PDU(protocol data unit,协议数据单元)的元素之一，所谓PDU是Modbus协议定义的一个与基础通讯层无关的简单协议数据单元．而在特定总线或者网络上面Modbus协议则通过ADU(application data unit,应用数据单元)引入一些附加域，以实现完整而准确的数据传输．

为了寻求一种简介的通讯格式，Modbus协议定义了PDU模型(功能码＋数据)；为了适应多种传输模式，在PDU的基础上增加必要的前缀(地址)和后缀(CRC)，形成ADU模型．

Modbus事务处理的过程如下：

主设备创建Modbus应用数据单元形成查询报文，其中功能码标识了向从设备指示将执行那种操作．功能码占用一个字节，有效的码字范围是十进制1~255(其中128~255为异常响应保留)．查询报文创建完毕，主机设备(或客户端)向从设备发送报文，从机设备接收报文后，根据功能码做出相应的动作，并将响应报文返回给主机设备．

在一个正确接收的Modbus ADU中，不出现与请求Modbus功能有关的差错，那么从机设备将返回正确的响应报文．如果出现与请求Modbus功能有关的差错，那么响应报文的功能码将包括一个异常码，主机设备能够根据异常码确定下一个执行的操作．

对于异常响应，从机设备将返回一个与原始功能等同的码值，但设置改原始功能码的最高有效为为逻辑１,用于通知主机设备．


## 参考资料

1. [MODBUS软件开发实战指南](http://img.duxiu.com/n/jpgfs/book/base/14200361/aac0e323cd234e3a9c2f3ff24ccffdba/a9984b52c5eef50f716c02c85ec86262.shtml?uf=1&t=4&time=2018080413&url=http%3A%2F%2Fbook.ucdrs.superlib.net%2FbookDetail.jsp%3FdxNumber%3D000016535273%26d%3DA7A2A220D76E1E7459730BA3AA515620%26rtype%3D1);
2. [MODBUS官方网站](http://www.modbus.org);
3. [Modbus应用开发指南](http://www.modbus.org/docs/Modbus_Application_Protocol_V1_1b3.pdf)
4. [串口Modbus应用开发指南](http://modbus.org/docs/Modbus_over_serial_line_V1_02.pdf)

