---
layout: post
title:  "Aria2 on Linux 断网的问题"
date:   2019-03-13 05:15 +0800
---

我的下载机是一部 11 年前的笔记本。自从切换至 Linux 并使用 Aria2 下载之后，它一直有一个问题：一旦 Aria2 的下载速度稍高，它就断网失联。还不是 WiFi，是以太网。掀开屏幕来看，不是睡眠问题，网络服务和 Aria2 的服务都正常，日志也没有什么痕迹，看不出啥毛病，就是突然没网络了，重启各项服务和网卡都没用，只能重启机器，然后速度起来，它继续断网，如此循环往复。

首先想到，处理器、内存、硬盘、键盘等很多部件都换过，一一排查，然而并不解决问题；又想到这是一部老国产，做工差，岁月摧残，散热很成问题，于是更新了硅脂和垫片，清理了风扇，结果还是老样子；再看系统，无论是 Arch 还是 Ubuntu，问题相同，更换最新内核也没用。

问题不在硬件上，不在散热上，不在系统上，也不在软件上，难道，在驱动上？

```
$ lspci -nnk | grep 0200 -A2

07:00.0 Ethernet controller [0200]: Broadcom Inc. and subsidiaries NetLink BCM5906M Fast Ethernet PCI Express [14e4:1713] (rev 02)
	Subsystem: Lenovo IdeaPad S10e [17aa:3a23]
	Kernel driver in use: tg3
```

这是一块博通的以太网卡 BCM5906M，使用的 tg3 驱动。

```
$ dmesg | grep tg3

[    1.541834] tg3.c:v3.137 (May 11, 2014)
[    1.558500] tg3 0000:07:00.0 eth0: Tigon3 [partno(BCM95906) rev c002] 
```

驱动版本是 v3.137，更新于 2014 年的 5 月。于是以 tg3 lost ethernet connection 为关键词 Google 了一下，果不其然，这是一个被讨论了很久的 [Bug](https://bugs.launchpad.net/ubuntu/+source/linux/+bug/1447664)，至今都还有人在报案。

## 尝试方案一

[问题根源可能是](https://bugs.launchpad.net/ubuntu/+source/linux/+bug/1447664/comments/13)：tg3 驱动使用了虚拟内存去做 DMA 传输。正确的做法应该是使用 `dma_map_page()` 将所有 DMA 传输重新映射到逻辑内存，以便 HIGHDMA 功能正常。

有个兄弟给出了自己的[变通方案](https://bugs.launchpad.net/ubuntu/+source/linux/+bug/1447664/comments/9)，他/她博客也有[专门介绍](https://lauri.võsandi.com/2016/02/fixing-broadcom-bcm5762-on-ubuntu.html)，我试了，问题并没完全解决，但大幅缓解。大半天下来，有两次断线，勉勉强强算是能挂机下载了。

方法说起来很简单，直接关闭 HIGHDMA 功能即可。持久关闭的方法是创建一个 /etc/udev/rules.d/80-tg3-fix.rules 文件，内容如下，注意替换其中的 ATTRS{device}，比如我的这块 BCM5906M，设备 ID 为 0x1713。

```
ACTION=="add", SUBSYSTEM=="net", ATTRS{vendor}=="0x14e4", ATTRS{device}=="0x1687", RUN+="/sbin/ethtool -K %k highdma off"
```

## 尝试方案二

在 Grub 配置里面添加一个启动参数：iommu=soft。跟方案一相似，有所缓解，依旧断线。

```
GRUB_CMDLINE_LINUX_DEFAULT="maybe-ubiquity iommu=soft"
```

## 尝试方案三

跟方案一类似，但关闭不是 HIGHDMA，而是 SG，它具体是啥，没看太明白。注意 enp7s0 是我的网卡，使用前请先 ifconfig 看一下网卡命名。已经使用半天，截至目前，运行稳定，相对于前两个方案，关闭 SG 似乎更稳定一些。可以参考方案一做持久化。

```
sudo ethtool -K enp7s0 sg off
```

以上方案均单独测试，没有混用。另外，重连无需重启，可以先 modprobe -r broadcom tg3 然后按顺序 modprobe broadcom；modprobe tg3 实现重新联网。