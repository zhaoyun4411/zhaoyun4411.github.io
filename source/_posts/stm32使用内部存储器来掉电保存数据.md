---
title: stm32使用内部存储器来掉电保存数据
tags:
  - 嵌入式
date: 2018-11-17 10:32:01
---

## 前言

stm32的flash是用来存储主程序的，st公司为了节约成本，并没有在片上加上eeprom，但是在许多场合下
需要掉电保存一部分数据的，这里st公司提供的IAP（在应用编程）的功能可以将stm32的flash当成EEPROM
来使用。

<!--more-->

存储器主要分为两种： **随机存取器RAM** 和 **只读存储器ROM**

```
存储器
├── RAM （Random Access Memory）随机存储器：掉电数据丢失，多用于内存等
│   ├── SRAM（静态RAM）存取速度快，价格昂贵。多用于CPU的一级缓存，二级缓存等
│   └── DRAM（动态RAM）
│        ├── EDORAM
│        ├── FPRAM/FastPage
│        ├── SDRAM
│        ├── DDR RAM
│        ├── RDRAM
│        ├── WRAM
│        └── SGRAM
└── ROM(Read Only Memory)只读存储器：掉电数据不丢失，非易失性存储器件
    ├── PROM
    ├── EROM
    ├── EEROM
    └── FLASH
         ├── NOR Flash
         └── NADN Flash
```

## stm32 flash 的简介

不同型号的stm32其stm32的容量也有所不同，根据容量大小，可以分为以下三种情况；

1. 小容量产品： Flash的大小为1-32KB（stm32f10x_LD）
2. 中容量产品： Flash的大小为64-128KB（stm32f10x_MD）
3. 大容量产品： Flash的大小为256KB以上（stm32f10x_HD）

stm32的存储模块由：*主存储器，信息块和闪存存储器接口存储器* 三个部分组成。

* 主存储器，该部分用来存放代码和数据常数（如const类型数据）。对于大容量产品，其被划分为256页，
每页2K字节。

* 信息块，该部分分为两个小部分，其中启动程序代码，是用来存储st自带的启动程序，用于串口下载代码。

* 内存存储器接口寄存器，该部分用于控制闪存读写。

stm32f4内部flash扇区的划分如下：

| name | block base addresses | size |
|:-:|:-:|:-:|
| sector 0 | 0x0800 0000 - 0x0800 3FFF | 16Kbytes |
| sector 1 | 0x0800 4000 - 0x0800 7FFF | 16Kbytes |
| sector 2 | 0x0800 8000 - 0x0800 BFFF | 16Kbytes |
| sector 3 | 0x0800 C000 - 0x0800 FFFF | 16Kbytes |
| sector 4 | 0x0801 0000 - 0x0801 FFFF | 64Kbytes |
| sector 5 | 0x0802 0000 - 0x0803 FFFF | 128Kbytes |
| sector 6 | 0x0804 0000 - 0x0805 FFFF | 128Kbytes |
| sector 7 | 0x0806 0000 - 0x0807 FFFF | 128Kbytes |
| sector 8 | 0x0808 0000 - 0x0809 FFFF | 128Kbytes |
| sector 9 | 0x080A 0000 - 0x080B FFFF | 128Kbytes |
| sector 10 | 0x080C 0000 - 0x080D FFFF | 128Kbytes |
| sector 11 | 0x080E 0000 - 0x080F FFFF | 128Kbytes |

> 注意最好不要使用sector0，因为bootloader和主程序在这个地方，操作后会导致程序跑飞，最好将数据
放在最后一个扇区。

## stm32 flash操作流程

stm32的flash操作已经属于嵌入式设备中很底层的操作了，直接对地址进行存取，简单来说，Flash操作大
致需要以下流程：

1. 确定要写入的Flash的首地址
2. 解锁Flash
3. 对Flash进行操作（写入数据）
4. 对Flash重新上锁

### 如何查找并选定要写入的Flash十六进制地址值的方法

想要选定安全的Flash地址进行读写，可以根据自己的STM32 MCU型号，查找数据手册，确定FLASH地址的
区域段，因为起始段会存储代码，所以一定要避开起始段，以避免数据错误。下面是在网上找的一个例子。


>我此次操作Flash使用的MCU是STM32103C8T6，所以以该型号MCU为例进行描述：
在数据手册中，可以看到STM32103C8T6的flash起始地址是0x0800 0000，
而STM32103C8T6的Flash大小为64K，可以计算出STM32103C8T6的Flash地址范围是：
0x0800 0000~0x0800 FFFF（计算方法参考另一篇博客：STM32内存大小与地址的对应关系以及计算方法）。
这里选取0x0800 F000作为读写操作的起始地址，对于C8T6这款MCU，操作这个起始地址应该算是很安全的范围了。

stm32flash擦除操作
```c
  HAL_FLASH_Unlock();

  static FLASH_EraseInitTypeDef EraseInitStruct;
  uint32_t SectorError = 0;
  EraseInitStruct.TypeErase = FLASH_TYPEERASE_SECTORS;
  EraseInitStruct.VoltageRange = FLASH_VOLTAGE_RANGE_3; //芯片供电电压
  EraseInitStruct.Sector = FLASH_SECTOR_11;             //从那个扇区开始擦除
  EraseInitStruct.NbSectors = 1;                        //一共擦除几个扇区
  if(HAL_FLASHEx_Erase(&EraseInitStruct, &SectorError) != HAL_OK)
  {
    while(1);
  }

  HAL_FLASH_Lock();
```

> 注意在对flash写入新数据的时候需要对其作擦除操作，flash只能写入0的值，无法将0写为1.


## 参考博客

1. [stm32F407 FLASH 操作笔记](https://blog.csdn.net/xiaogu0322/article/details/78593423)
2. [STM32操作访问flash](https://blog.csdn.net/Ace_Shiyuan/article/details/78196648)
