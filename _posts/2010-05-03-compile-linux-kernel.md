---
layout: post
title:  "曲线救国"
date:   2010-05-03 10:26:05
categories: jekyll update
---

> 历史已明，证科学的战胜非科学的，乃是一种必然趋势，所以，要救国，除了提倡科学精神外, 没有第二剂良药了

## 全线救国之---乌托邦的内核编译

编译环境：联想Y430笔记本， Windows 7 wubi安装的Ubuntu 10.04

参考来源：

* [http://forum.ubuntu.org.cn/viewtopic.php?f=97&t=110461](http://forum.ubuntu.org.cn/viewtopic.php?f=97&t=110461)
* [https://fanqiangti.appspot.com/berelent.blogspot.com/2009/10/howto-compile-bfs-kernel.html](https://fanqiangti.appspot.com/berelent.blogspot.com/2009/10/howto-compile-bfs-kernel.html)
* [http://hi.baidu.com/%E5%B0%8F%E5%B7%B7%E5%90%A7/blog/item/737cc036993bf33d0b55a9f5.html](http://hi.baidu.com/%E5%B0%8F%E5%B7%B7%E5%90%A7/blog/item/737cc036993bf33d0b55a9f5.html)
* [http://forum.ubuntu.org.cn/viewtopic.php?f=97&t=226956](http://forum.ubuntu.org.cn/viewtopic.php?f=97&t=226956)
* [http://ubuntuforums.org/showthread.php?t=1014190](http://ubuntuforums.org/showthread.php?t=1014190)
* [http://www.ubuntugeek.com/howto-install-nvidia-drivers-manually-on-ubuntu-10-04-lucid-lynx.html](http://www.ubuntugeek.com/howto-install-nvidia-drivers-manually-on-ubuntu-10-04-lucid-lynx.html)
* [https://fanqiangti.appspot.com/berelent.blogspot.com/2009/10/howto-compile-bfs-kernel.html](https://fanqiangti.appspot.com/berelent.blogspot.com/2009/10/howto-compile-bfs-kernel.html)

开始编译内核，刚有发到<a href="http://www.firefox.net.cn/forum/viewtopic.php?t=31271" target="_blank">Firefox中文社区</a>，知道也没人看

这是步骤：

s1. <a href="http://www.kernel.org/" target="_blank">下载内核源码</a>

s2. 下载传说中的<a href="http://ck.kolivas.org/patches/bfs/" target="_blank">BFS补丁</a>

s3. 然后稍稍装几个工具：

```bash
sudo apt-get install build-essential kernel-package libncurses5-dev fakeroot
```

s4. 解压源码，打上BFS补丁

```bash
tar -jxf linux-2.6.33.3.tar.bz
cd linux-2.6.31
patch -p1 < ../2.6.31-sched-bfs-316.patch
```

s5. 开始量身定做

```bash
make mrproper
make localmodconfig
```

s6. 差点忘了，再装点工具

```bash
sudo aptitude install libqt3-headers libqt3-mt-dev libqt3-compat-headers  libqt3-mt
```

s7. 好戏登场，看着教程慢慢选，参考一、<a href="http://linux.vbird.org/linux_basic/0540kernel.php" target="_blank">鸟哥的私房菜</a>; 参考二、<a href="http://lamp.linux.gov.cn/Linux/kernel_options.html" target="_blank">金步国</a>; 一般磨上2-4个小时, 慢慢来.

打了BFS补丁，这几个是必须注意的

* Processor type and features -> Tickless System (Dynamic Ticks) // 留空
* Processor type and features -> Preemption Model (Preemptible Kernel(Low-Latency Desktop)) -> 选 Preemptible Kernel (Low-Latency Desktop)
* Processor type and features -> Timer frequency -> 选1000Hz


```bash
make xconfig
```

s8. 清理，编译, CONCURRENCY_LEVEL=2 这里是因为我是双核，起到加速编译的作用，jed0.1换成你自己的

```bash
sudo make-kpkg clean
CONCURRENCY_LEVEL=2 fakeroot make-kpkg --initrd --append-to-version=jed0.1 kernel-image kernel-headers
```

s9. 赶紧安装

```bash
cd ../
sudo dpkg -i *.deb
```

s10. 激动人心的时刻，更新一下启动信息

```bash
sudo mkinitramfs -o /boot/initrd.img-2.6.33.3-jed0.1 2.6.33.3-jed0.1
1sudo update-grub
```

s11. 好内，速度重启

```bash
sudo reboot
```

## 接下来是Nvidia显卡驱动

s1. 先<a href="http://www.nvidia.com/Download/index5.aspx?lang=en-us" target="_blank">下载</a>

s2. 加几个黑名单

```bash
gksudo gedit /etc/modprobe.d/blacklist.conf
```

s3. 把下面这些贴到blacklist.conf的最后

```bash
blacklist vga16fb
blacklist nouveau
blacklist rivafb
blacklist nvidiafb
blacklist rivatv
```

s4. 删除旧驱动，并重启

```bash
sudo apt-get --purge remove nvidia-*
```

有错误提示时，选 "跳出去终端"
然后登陆，CD到存放新下载驱动的目录，比如

```bash
cd /home/jed/Downloads/
```

s5. 然后安装，记得利用好TAB键，不用记那么长名字

```bash
sudo sh NVIDIA-Linux-x86_64-195.36.24-pkg2.run
```

s6. 然后启动GDM

```bash
sudo service gdm start
```
