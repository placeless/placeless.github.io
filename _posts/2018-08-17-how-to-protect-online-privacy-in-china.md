---
layout: post
title:  如何保障线上数据隐私
date:   2018-08-17 15:38 +0800
---

分享了 @fenng 的一篇《[有些电商网站怎么诡异的猜到你要买什么东西的？](https://mp.weixin.qq.com/s/DjC0O0krAPbzICJFp78TAg)》到同学群里，于是有朋友在问：如何保障自己的数据隐私，你的做法是什么？

首先，我们搞明白敌人是谁，威胁形式都有哪些。就我个人的情况，主要是以下 4 个方面：屏蔽垃圾广告、屏蔽数据采集工具、躲开链路劫持与偷窥、保障穿墙的安全与通畅。

其次，再来对症下药。之前其实也零碎写过一些，比如：[电信劫持一例](/blog/china-telecom-hijack-internet-traffic.html#article)、[电信劫持·续](/blog/shame-on-you-china-telecom.html#article)、[广告拦截](/blog/adblocking-experience.html#article)、[uBlock Origin 的拦截模式](/blog/blocking-mode-in-ublock-origin.html#article)、[挖矿脚本一例](/blog/cryptojacking.html#article) 等等，这次主要做一个总结。

一般来讲，我们通常有两个上网方式/场景，如下图，一是在家里或是其他室内，通过有线或是 Wi-Fi，走宽带运营商连接网络；一是借助手机等移动设备，走移动运营商连接网络。在此，通过这两种通路分类，简述一下**非专业的个人经验**。

<div class="mermaid" style="text-align:center;">
graph LR
  subgraph 内网
  上网设备-->家用路由器
  end
  subgraph 外网
  家用路由器-->宽带运营商
  宽带运营商-->网络服务
  上网设备-->移动运营商
  移动运营商-->网络服务
  end
</div>
<script src="https://unpkg.com/mermaid@7.1.0/dist/mermaid.min.js"></script>
<script>mermaid.initialize({startOnLoad:true});</script>


## 上网设备及系统

- 尽量用 iOS/macOS/Linux，Windows 升级到 Win 10
- 不要关闭防火墙，微软自带的 Defender Antivirus 就不错
- 不要越狱，不要用什么助手、什么同步推
- 不用或者少用国产软件、App
- 尤其是各种管家、卫士、输入法
- 更加不要安装自己不了解的盗版、破解程序、电子书
- 不要随意插别人的 U 盘
- 下载文件要注意来源可信再打开

## 上网入口

- 严格限制软件、App 权限，不要图方便
- 浏览器切记只选 Safari、原版 Chrome、Firefox 等，杜绝国产安全、极速浏览器
- 浏览器使用 Adblock Plus，甚至可以考虑 uBlock Origin 搭配 Hard 模式
- 平时瞎逛，多用浏览器的隐私模式
- CNNIC 等证书要根除
- 如非确定，不点链接，不信短消息

## 路由器

- 首选国际大厂，如 Netgear 等，平摊到使用周期里面，贵不了多少钱。
- 国产路由器跟其他国产产品一样，藏了很多鸡贼的坑
- 比如标称千兆网络，实际是 Wi-Fi 5G，但 WAN 口才 100 兆等等
- 再比如擅自分享你的 Wi-Fi
- 更不用提其 ROM 的安全性和监守自盗的新常态
- 而且万一有刷 OpenWrt 搭梯子的需求，大厂路由也更容易适配

## 运营商劫持

- 这一点不好防，我的斗争经历见 [电信劫持一例](/blog/china-telecom-hijack-internet-traffic.html#article)、[电信劫持·续](/blog/shame-on-you-china-telecom.html#article)
- 尽量只访问 HTTPS 网站
- 通畅的前提下，自定义安全 DNS，比如 8.8.8.8， 1.1.1.1 等
- 抛弃 2G 网络

## 云服务

- 担心云上贵州，Apple ID、iCloud 都可以迁移出去
- 不要开通云短信备份
- 不要随意授权微博、微信的第三方应用获取自己的信息
- 不要信任需要上传通讯录的软件/App，害人害己

## 通向世界

- 不要用百度来的「代理」
- 有条件买一个服务翻墙服务
- 尽可能自建 VPN 或是 Shadowsocks
- 不然可能连原版 Chrome、Firefox 都下载不到
- 更要避免「问今是何世，乃不知有汉，无论魏晋」的尴尬
