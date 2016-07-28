---
layout: post
title:  "回顾 Google Analytics"
date:   2016-07-23 04:40 +0800
---

GA 已经是个 10 多岁老家伙了。

2005 年 4 月，Google 收购 Urchin，在 Urchin 的基础上，开始建造 Google Analytics，期望通过这一强力、易用的分析工具，帮助全球的网站开发者和营销人员，更好地理解和改进他们的业务。Urchin 继续小幅更新，并以 License 方式卖到 2012 年 3 月，版本停在 7.200。知道 GA 为什么有那么多 UTM 缩写吗？[UTM]([https://support.google.com/urchin/answer/28307?hl=en](https://support.google.com/urchin/answer/28307?hl=en)) = Urchin Tracking Monitor/Module

2005 年 11 月，正式带 Google 品牌的 GA 版本被推出，并且开放注册。不曾想，火爆空前，需求过大，一周之后就关停了注册。之后，随着服务器扩容，Google 采用抽奖的方式，分批发放邀请码。自此，后来 GA 的新功能，也总是先逐步开放给一小部分用户体验，再全面铺开给所有用户。

2006 年 8 月之前，在服务器性能容纳范围之内，Google 陆续送出了数批邀请码，到 8 月中，终于，再一次，GA 向所有用户开放。

2009 年 6 月，GA 推出 Event Tracking。当时我还不太明白，已经有了 Virtual Pageview，为啥还再搞个 Event。回过头来看，这其实一个很大的转变，现在很多工具已经完全抛弃了 pageview 这一实体，重点放在了 user 和 event 上。

2009 年 12 月，Google 推出了 Asynchronous Google Analytics，异步取代之前的同步加载，避免 GA Snippet 阻塞整个网页的性能。[官方博客说它](http://googlecode.blogspot.hk/2009/12/google-analytics-launches-asynchronous.html)更快，更准，更好看（不会再像以前，等待 GA 加载的时候，浏览器会有个小圆圈一直在那转啊转，提示用户网页还没有装载完成）。还有人说能采集到更多数据，因为更快了嘛，可以放到 `head` 标签内，异步加载，所以以前由于图片、Flash 太慢，等不到 GA tag 触发的访客，现在都能统计到了。当年 WA 必备工具 HttpWatch 还真去挨个[测试验证](https://blog.httpwatch.com/2010/07/29/asynchronous-google-analytics-is-better-but-not-faster/)了……

2011 年 8月，GA 新增 Multi-Channel Funnels & Attribution Modeling，这是一个高级功能，要用好还挺不容易，要用好，对渠道标记、Goal 的设定、数据源的整合、数据采集的实施，要求更高了。

2011 年 9 月，GA 向全体用户开放 Real-Time Reporting，很炫酷，对于 Campaign 上线初期的各项 debug 非常有帮助。

2011 年 12 月，API 3.0 以及后来的 Google Analytics Spreadsheet Add-on，用它们搞点自动化，对提高工作效率很有帮助，分析、报告更自由，更好玩。

2012 年 6 月，Google IO，发布 Mobile App Analytics，很重要，但没怎么用过。

2012 年 10 月，哒哒哒哒，Google Tag Manager，不用多说，意义重大。可以说这是一项让 WA 从业人员插上翅膀，翻身农奴得解放的革命性功能。只要会点 JavaScript，几乎再也不用对着美术、IA 递过来的线稿去脑补开发做出来的网站，然后边推敲埋点框架，边苦恼接下来他们的想法会变多少个样。GTM 要求网页代码足够标准，也不支持 Flash，所以当时还有一个念头：Flash 怎么还不死。

还是 2012 年 10 月，Universal Analytics in beta，从头到脚，全新的 GA。可以这样说，除了没有 Cookie Mapping，UA 比现在的各种 DMP 都要夯实可靠。

- Measurement Protocol
- Server Side Sessionization
- Offline Conversion Import
- Custom Dimensions and Custom Metrics
- Cost Data Import
- ……

2014 年 4 月，Universal Analytics 正式摘掉 beta 帽子。

2015 开始，GA 有了自己的 [Release notes](https://support.google.com/analytics/topic/6179391?hl=en)。
