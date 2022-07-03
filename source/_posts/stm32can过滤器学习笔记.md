---
title: stm32can过滤器学习笔记
tags:
  - stm32
  - can模块
date: 2018-09-17 19:38:49
---


学习stm32的bxCAN 的IP核，有关bxCAN的过滤器的内容在参考手册中看的不是十分明白，这里就过滤器的
四种工作模式查阅资料，做出记录．

<!--more-->

在stm32的can协议里面，报文的标识符不代表节点的地址，而是和报文的内容有关。因此发送者需要用广播
的形式将报文发送给所有的接收者。节点在接收报文的时候，需要根据报文的标识符来决定是否接收这段报文；
如果需要，就将报文内容接收下来，如果不需要，就将报文丢弃。

bxCAN内核为应用程序提供了14个位宽可变、可配置的过滤器组（13～0），以便接收那些软件需要的报文。
硬件过滤的方法节省了CPU的开销，否则就必须由软件过滤从而占用一定的CPU开销。每个过滤器组由两个32
位的寄存器，CAN_FxR0和CAN_FxR1组成。

## 几个重要的概念

### 过滤器组

stm32总共提供14个过滤器组来处理can接收过滤器的问题，每个过滤器组包含两个32位的寄存器CAN_FxR0
和CAN_FxR1组成，在设置为屏蔽位的模式下，其中一个标作为标识符寄存器，另一个为屏蔽码寄存器。过滤
器组中的每个过滤器编号（叫做过滤器号）从0开始，到某个最大的数值（这时最大值并非13，而是取决与14
个过滤器组的模式和位宽的设置，当全部配置为位宽为16，且标识符列表模式时，最大编号为14*4-1=55）

### 过滤器

stm32提供两种过滤器模式：屏蔽位模式和标识符列表模式。

#### 屏蔽位模式

为了过滤出一个标识符，应该设置过滤器组工作在标识符列表模式。

在屏蔽位模式下，标识符寄存器和屏蔽寄存器在一起，指定报文标识符的任何一位，应该按照“必须匹配”或者
“不用关心”处理。

> 可以理解为在我们读取CANID中的某几位，如果这几位满足要求则可以进入否则不可以进入。

> 对于stm32来说，要执行这一个工作，可以通过如下方式执行：1）获取消息中携带的ID。2）只看获取到
的ID中的某几位，其他位忽略。3）将忽略后的结果与要求的值进行比较。4）比较的结果相同则通过，不同则
不能通过。

#### 标识符列表模式

为了过滤出一个标识符，应该设置过滤器组工作在标识符列表模式。

在标识符列表模式下，屏蔽寄存器也被当作标识符寄存器用。因此，不是采用一个标识符加一个屏蔽位的方式。
而是使用两个标识符寄存器。接收报文标识符的每一位都必须根过滤器标识符相同。

> 列表模式可以简单的理解为准备一张表，将所需要的CANID填写上去，将接收到的报文与表对比，如果接收
到的ID与表上的ID一致，则通过，如果表上没有，则不通过。

#### 过滤器的位宽

每个过滤器的位宽都可以独立配置，以满足应用程序不同的需求。根据位宽的不同，每个过滤器可以提供：

 * 1个32位的过滤器，包括： STDID[10:0]、EXTID[17:0]、IDE和RTR位；
 * 2个16位的过滤器，包括： STDID[10:0]、IDE、RTR和EXTID[17:15]位。

### 标准canID与扩展canID

	* 标准ID一般小于或者等于0x7ff。只包含了基本的ID
	* 扩展的CAN的低18位位扩展ID，高11位为基本ID

## bxCAN的过滤器的解决方案

> 注意在使用stm32cube固件库，有两句关键代码如下：
* sFilterConfig.FilterNumber = m; //选择某过滤器组进行初始化，并配置初始化过滤器，对于双can模块的产品，m的值为0～27，对于单can的产品，m的值为0～13.
* sFilterConfig.BankNumber = n; //针对双can的stm32产品，配置can2可以使用的过滤器组的起始编号，n取值范围为0～28.从编号为n的过滤器组到编号为27的过滤器组分配给can2使用。如果不配置这个寄存器默认值为14.即将编号为14起往后的过滤器组分配给can2使用。

对于双can的stm32芯片，如果`sFilterConfig.BankNumber=0`，则将所有的过滤器都分配给can2使用
；如果`sFilterConfig.BankNumber=27`则表示将所有的过滤器都分配给can1使用，can2没有寄存
器使用。

对于stm32双can模块的芯片，can专用的SRAM访问是对外来数据的滤波接收功能，统一通过can1来进行使
能。因此，can1被称为master，can2被称为slave。换言之，can2对专用的SRAM访问和对信息滤波接收
必须借助can1来实现和管理。打个比方，就像你和某人一组共用一批设备各自做自己的课题，对方为组长
。原则上这些设备共用，但由组长管理。你需要使用某些设备时需对方的协助与支持，至于你到底做什么，
对方不干涉。反过来，组长需要使用那些设备时就不需要你的支持和协助，它自行使用和管理。

过滤器组【filter bank】，是配置过滤器的原始资源，由2个32位寄存器组成。单CAN芯片有14个过滤器
组，双CAN芯片有28个过滤器组，过滤器组编号由硬件拟定。一个过滤器组根据不同的过滤器模式【过滤方式
和过滤器宽度】可配置1-4个过滤器。显然，过滤器组并不等同于过滤器。就好像说渔网的编制线材不等同于
渔网一样的道理。过滤器组经过适当的配置后才能形成对信息具有过滤作用的过滤器。

### can过滤器配置

#### 32位列表模式

```C
static void CANFilterConfig_Scale32_IdList(void)
{
  CAN_FilterConfTypeDef  sFilterConfig;
  uint32_t StdId =0x321;				//这里写入两个CAN ID，一个位标准CAN ID
  uint32_t ExtId =0x1800f001;			//一个位扩展CAN ID

  sFilterConfig.FilterNumber = 0;				//使用过滤器0
  sFilterConfig.FilterMode = CAN_FILTERMODE_IDLIST;		//设为列表模式
  sFilterConfig.FilterScale = CAN_FILTERSCALE_32BIT;	//配置为32位宽
  sFilterConfig.FilterIdHigh = StdId<<5;			//基本ID放入到STID中
  sFilterConfig.FilterIdLow = 0|CAN_ID_STD;			//设置IDE位为0
  sFilterConfig.FilterMaskIdHigh = ((ExtId<<3)>>16)&0xffff;
  sFilterConfig.FilterMaskIdLow = (ExtId<<3)&0xffff|CAN_ID_EXT;	//设置IDE位为1
  sFilterConfig.FilterFIFOAssignment = 0;			//接收到的报文放入到FIFO0中
  sFilterConfig.FilterActivation = ENABLE;
  sFilterConfig.BankNumber = 14;

  if(HAL_CAN_ConfigFilter(&hcan1, &sFilterConfig) != HAL_OK)
  {
    Error_Handler();
  }
}
```

#### 16位列表模式

```C
static void CANFilterConfig_Scale16_IdList(void)
{
  CAN_FilterConfTypeDef  sFilterConfig;
  uint32_t StdId1 =0x123;						//这里采用4个标准CAN ID作为例子
  uint32_t StdId2 =0x124;
  uint32_t StdId3 =0x125;
  uint32_t StdId4 =0x126;

  sFilterConfig.FilterNumber = 1;				//使用过滤器1
  sFilterConfig.FilterMode = CAN_FILTERMODE_IDLIST;		//设为列表模式
  sFilterConfig.FilterScale = CAN_FILTERSCALE_16BIT;	//位宽设置为16位
  sFilterConfig.FilterIdHigh = StdId1<<5;	 //4个标准CAN ID分别放入到4个存储中
  sFilterConfig.FilterIdLow = StdId2<<5;
  sFilterConfig.FilterMaskIdHigh = StdId3<<5;
  sFilterConfig.FilterMaskIdLow = StdId4<<5;
  sFilterConfig.FilterFIFOAssignment = 0;			//接收到的报文放入到FIFO0中
  sFilterConfig.FilterActivation = ENABLE;
  sFilterConfig.BankNumber = 14;

  if(HAL_CAN_ConfigFilter(&hcan1, &sFilterConfig) != HAL_OK)
  {
    Error_Handler();
  }
}
```
