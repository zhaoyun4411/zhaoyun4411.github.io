---
title: Intel-efi-相关启动流程解析
tags:
  - Embedded System
  - Intel
  - Boot process
date: 2022-08-28 17:01:37
---


调试Intel平台的启动脚本。对平台默认采用的EFI启动方式做如下的记录。

<!--more-->

EFI 是位于 OS 和 lowest level firmware之间的一层软件。具体位置如下图：

```
       Operating system
              ^
              |
              V
  Extensible Firmware Interface
              ^
              |
              V
          Firmware
              ^
              |
              V
          HardWare
```

对于Intel平台来说，当CPU上电会先通过NVRAM上的BIOS进行上电自检和初始化CPU及外围设备。然后会跳转到磁盘第一个分区的`/EFI/BOOT/bootx64.efi`。如果文件不存在，启动将跳转到bios中的UEFI shell。 如果文件存在，就会去寻找相应的启动配置。

当`/EFI/BOOT/bootx64.efi` 文件存在的时候。启动回去寻找loader文件夹中entries中的内容。并根据其中的内容加载文件到内存中。给出两个示例boot.conf 和 xen.conf 如下：

boot.conf ： 启动bootx64.efi之后， efi去load 根目录下 /EFI/XEN/bzImage ，即启动Linux。
```
title boot
linux /EFI/XEN/bzImage
options LABEL=Boot root=PARTUUID=64fb2cc9-9ecd-4f4c-9009-e686e43f788c  rootfstype=ext4  rootwait console=ttyS0,115200 console=tty0
initrd /EFI/XEN/microcode.cpio

```

xen.conf ：启动bootx64.efi之后，efi去load 根目录下 /EFI/XEN/xen-abb-intel.efi ，即启动xen。
```
title Xen Hypervisor
efi /EFI/XEN/xen-abb-intel.efi

```

另外对于调试efi。可以移除或者重命名`/EFI/BOOT/bootx64.efi` 进入shell后切换目录，手动执行efi文件。
