---
layout: post
title:  "电信劫持一例"
date:   2016-06-21 13:53 +0800
---

![Overwatch Animated Short Recall Screenshot](/files/2016/06/21/Overwatch_Animated_Short_Recall_Screenshot.jpg)

<figcaption>Overwatch Animated Short: Recall (https://www.youtube.com/watch?v=sB5zlHMsM7k)</figcaption>

我觉得，宽带运营商劫持的本质，就是「骗和偷」：骗用户和广告主，偷网站内容和位置。

由于「此路是我开，此树是我栽」，每个用户只要联网，就都要假道于它，因此，它号称了解每一位用户的一举一动，包含吃喝拉撒、衣食住行的每一个字节，时称「运营商大数据」。

其累累恶行，网络上已经有许多探讨，这里仅举其一例应用：链路劫持。它通过劫持我和目标网站之间的通信，篡改内容，偷梁换柱，强行插播垃圾广告，暗地里做着拦路打劫的勾当。平常我基本全局 VPN，浏览器装有广告拦截工具，访问的多数网站也都是 HTTPS 的，所以切身体会不到这个恶心，但前一阵做测试时，关闭拦截工具，开 Chrome 的无痕模式，却立马看到了浏览器右下角盖住了一块固定位置，虽然广告没有加载出来，但是我知道，上海电信开始喂我吃苍蝇了。

随意找个网站，比如 163 为例，劫持成功，表现如下图，注意右下角。整个劫持过程，跟文末「延伸阅读」中的各文所述基本一致。

![劫持广告](/files/2016/06/21/hijacked_ad.jpg)

下为 Wireshark 抓包图，开始访问 163.com 的 CDN 资源。25-27 号完成 3 次握手之后，28 号包开始索要 `/index.html`，但从随后的 29-32 号包来看，其 TTL 与 ID 均有明显问题：

- TTL 与原本的不一致（55 变 183）
- Identification 不自增

![ttl](/files/2016/06/21/ttl.png)

显然，又是运营商在中间抢答了，后面缓到的黑颜色的正确返回包，则只能变成 Out-Of-Order，被丢弃。那么，运营商抢在前面，偷偷给我返回了什么内容呢，看下图：

![假 HTTP 报头以及被劫持修改的 HTML](/files/2016/06/21/fake_http_header_and_html.png)

首先，圈出来的 HTTP Header 明显不正常，太对付了，内容部分展开如上图的右半部分，非常狠毒。整张页面都是重构过的，就是一个广告容器，大致主要做 3 件事：

- 通过 http://139.129.220.86/js/jsstyle-hh.js 去取库存广告
- 通过 http://120.27.28.39/hw/kd580082.html 去做统计
- 容器切割为 3 部分：`iframe#ife`放`kd580082.html`，高宽均为 1 像素；`iframe#mf`放原内容`163.com`，高宽撑满浏览器；`div#nnn`固定在顶层页面右下角，是广告展示区域，没有广告则放空

用户正经要访问的 163.com，被挤压到一个叫做`mf`的`iframe`内，抢答页面的其他部分，全部都是强插广告和统计代码用的，其心可诛。

另外几个有意思的发现是：

- 以上 2 个 IP 也经常变动，暂时看是挂在阿里云上
- 并不是库存有广告才劫持你，而是不间断的，大约每 30 分钟劫持一次，没有库存只是看不到广告，劫持依然存在
- `kd580082.html` 加载的一个叫做`tongji_v05.js`的文件，内容非常的无耻，如下图所示：1. 堂而皇之的函数名`hj_*`，这个 hj 是 hijack 没跑了；2. 内有一个过滤域名列表，字符反向过来之后，如下图最右所列，斗胆猜测，可能是自己或者敌人的刷量小联盟，所以排除在统计之外

![无耻代码](/files/2016/06/21/hj_filters.png)

之前，我很难想象人会这么坏，后来没有工作，经朋友介绍，出去聊了几回，才发现无耻的人，原来这么多。不少人和公司，都想要在大数据行业一展拳脚，言必称「运营商」大数据，从高到底，花式 Title，聊完，我敢说，在他们的脑袋里，对「用户私隐」、「人的尊重」这些词，是一个影儿都没有。

至于防备手段，普通用户无非是 Adblock 等插件加持，全局 VPN 等，投诉收效慎微，可期望的最佳手段是各大网站都启用 HTTPS，让流氓无路可拦。

最后，我赞同：

- 营销的本质建立品牌和用户之间的信任
- Web Banner 已死
- 运营商大数据骗人

  ​

​
延伸阅读：

- WooYun知识库
  - [流量劫持攻击之链路劫持剖析](http://drops.wooyun.org/tips/13661)
  - [也谈链路劫持](http://drops.wooyun.org/tips/11682)
  - [漫谈流量劫持](http://drops.wooyun.org/tips/15826)
- 腾讯安全应急响应中心
  - [链路劫持攻击一二三](https://security.tencent.com/index.php/blog/msg/10)
  - [某电商网站流量劫持案例分析与思考](https://security.tencent.com/index.php/blog/msg/81)
- Fenng 闲思录
  - [如何防止网站被电信运营劫持弹广告](http://dbanotes.net/security/iframekiller_anti_iframe_clicjacking.html)
- May the #! be with you
  - [绕过运营商HTTP劫持](https://onebitbug.me/2013/12/19/escape-isp-http-hijacking/)
- imweb 前端社区
  - [从运营商小广告到HTTPS](http://imweb.io/topic/565c71673ad940357eb99879)
