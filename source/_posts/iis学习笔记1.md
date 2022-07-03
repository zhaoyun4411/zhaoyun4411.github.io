---
title: iis学习笔记1
date: 2019-06-23 21:03:32
tags:
  - iis
  - 面试
---

## IIS的架构介绍

微软Internet信息服务（Internet Information Service， IIS），是在Windows平台上提供的一款微软官方的web容器服务。iis支持HTTP/FTP协议的web应用程序；同Apache类似，也需要有Tomcat的参与才可以运行JSP技术的动态网站；IIS也需要ASP.net平台的配合才能运行ASP.net的web应用程序。

<!--more-->

IIS主要版本从IIS 5.0 到 IIS 8.5这几个版本。包含了如下重要的演进：

1. 持续改进的IIS工作进程： 在IIS5.0之前，Web网站的工作进程都是在IIS自身服务中进行的，当有一方出现严重错误的时候都会造成Web内容的崩溃。之后的版本对将IIS的服务进程与Web网站的工作进程做出了分离。（IIS 6.0 之后将IIS工作进程改为w3wp.exe）进程。
2. 配置文件增加可读性和移植性： 从IIS 6.0 开始 IIS的配置文件从二进制格式转化为Metabase.xml格式文件。从IIS 7.0开始XML文件变成三个XML文件，并将数据单独保存在三个单独的config文件中。使用XML格式保存配置项方便管理员阅读与排错。
3. 不断提高的Web网站运行性能：IIS 将网络I/O操作从用户态迁移到内核态。
4. 组件化设计和丰富接口使IIS越来越开放。

> 用户态和内核态：内核态是特权级最高的进程，应用程序大部分时间都是在用户态下运行的。

### 1 IIS相关知识介绍
#### 1.1 IIS应用程序文件夹

IIS服务是通过Windows服务器版本操作系统伤的服务器管理器添加web角色的方式安装的，IIS服务在操作
系统上安装完成后，会把程序文件都存放在 `%systemroot%\system32\inetsrv` 路径下。 inetsrv文件
夹中的内容由以下几个部分构成。

  1. IIS的配置文件和文件夹（类似华为平台代码文件目录config.json）；
  2. IIS的命令行管理工具（查看IIS应用程序池和我w3wp.exe进程ID之间的对应关系）；
  3. IIS的功能组件（如诊断组件，日志组件）；
  4. IIS管理控制界面。

#### 1.2 IIS web内容文件夹

iis会规划一个inetpub的文件夹来存放web网站内容。inetpub子文件夹作用：

| 文件夹名称 | 作用 |
| :-: | :- |
| custerr | 按照支持的语言种类存放默认的错误提示页面，即HTTP400以上的错误页面 |
| history | 用来保存IIS配置文件每次修改的历史记录，如果管理员发现IIS因为某次修改导致工作不正常，可以从这里回退到历史版本 |
| logs | 保存请求追踪日志和IIS日志文件夹 |
| temp | 用来保存应用程序池和临时数据 |
| wwwroot | IIS默认站点Web内容文件夹 |

除此之外IIS的HTTP错误日志保存在 `%systemroot%\system32\LogFiles\HTTPERR` 文件夹中。

#### 1.3 IIS相关的windows服务

| 服务名称 | 服务显示名称 | 描述 | 宿主程序 |
| :- | :- | :- | :- |
| AppHostSVC | Application Host Helper Service | 按照一定的时间间隔保存Web网站配置应用程序池的历史配置信息服务，即通过快照的方式保存需要配置的历史数据 | svchost.exe |
| FTPSVC | FTP Publishing Service | 在IIS7.0以后的版本中运行FTP协议的服务 | svchost.exe |
| IISADMIN | IIS Admin Service | 用来兼容IIS 6.0配置文件Metabase.xml模式的管理服务。这个服务在IIS6.0 以前的版本不存在，在IIS7.0 以后的版本中出现主要是为了兼容IIS6.0的管理工具而存在 | inetinfo.exe |
| MSFTPSVC | FTP Publishing Service 6 | 在IIS6.0 版本中运行的FTP服务 | inetinfo.exe |
| W3SVC | World Wide Web Publishing Service | IIS的核心服务，主要提供Web网站的发布及各管理功能 | svchost.exe |
| WAS | Windows Process Activation Service | 工作进程管理服务，负责工作进程的创建或者激活，资源和健康管理等功能 | svchost.exe |
| WMSVC | Web Management Service | 允许管理员远程管理IIS的服务 | svchost.exe |

### 2 IIS关键进程 InetInfo.exe和w3wp.exe

IIS服务中有两个进程十分关键：IIS服务自身进程Inetinfo.exe（负责按照配置文件描述启动和管理IIS的各个网站，IIS 7.0 之后这个服务不再存在）或WAS服务进程，以及运行Web网站所使用的w3wp.exe（运行网站代码的壳程序）。

在IIS 6.0中两个进程的关系是：Inetinfo.exe进程负责启动、关闭和管路w3wp.exe进程。Inetinfo.exe进程是w3wp.exe的父进程。Inetinfo.exe通过向w3wp.exe进程定期发送数据包的方式感知w3wp.exe是否工作良好。IIS7.0 之后这项内容交给了系统服务。

### 3 IIS架构探秘
#### 3.1 IIS内核层实现

在IIS6.0之后最基础的协议处理部分被封装成windows内核驱动，应用程序分别为tcp.sys和http.sys。

其中tcp.sys监控网络通讯，当请求的数据包含一个HTTP请求时，就会由tcp.sys转接给http.sys进行处理。http.sys检查数据是否合规。对于合规数据http.sys会生成HTTP的上下文对象，并把上下文对象放到网站对应的请求队列中排队等待处理。

#### 3.2 IIS应用层代码实现

http.sys在内核态处理完HTTP请求之后，IIS就会把HTTP请求对应的HTTP上下文转到对应的应用程序进程中，由对应的w3wp.exe进行处理。

#### 3.3 HTTP请求在IIS上处理的完整流程

1. 用户在客户端浏览器输入一个URL地址，向Web服务器发起HTTP请求，这个请求会首先被http.sys内核驱动进行处理。
2. http.sys驱动发送报文到WAS服务，从配置文件中获取网站相关配置信息。
3. WAS服务请求从配置文件中获取网站相关配置信息。
4. WWW服务器接收到网站相关的诸如应用程序池和站点配置等配置信息。
5. WWW服务器使用配置信息来配置http.sys内核驱动的行动，如请求队列等。
6. 当站点还没有工作进程为它服务时WAS服务按照网站应用池配置启动一个w3wp.exe工作进程。
7. 工作进程处理用户的HTTP请求，并将请求结果返回到http.sys驱动作为对该HTTP请求的响应。
8. 客户端浏览器接收到响应，渲染页面给用户查看。
