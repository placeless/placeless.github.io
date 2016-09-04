---
layout: post
title:  "Mixpanel 的故事"
date:   2016-09-04 01:59 +0800
---

<img alt="YC Analytics Technologies" src="http://themacro.com/images/articles/YC_Stacks_3-9a905b72.png" width="80%">

<figcaption>在 YC 投资的公司当中，各数据分析工具的流行程度。数据来源：http://themacro.com/articles/2016/08/yc-tech-stacks/</figcaption>

在 638 家 YC 公司内，除 Google (Universal) Analytics 之外，Mixpanel 已经是第二受欢迎的数据监测分析工具，以下就是 Mixpanel 的故事。

主要来源：

> - [The Macro: Startup School - Mixpanel]
> - [湾区日报: Mixpanel 如何创造需求的]

## Mixpanel 是如何诞生的

2008 年，19 岁的 [Suhail Doshi] 在 Slide 找到了一份实习，写写 Python，工作努力，由于项目出色，逐渐受到公司关注，备受鼓舞。也正是在这里，他开始接触到当时 Slide 先进的精细化数据运营理念和手段。

当 Suhai Doshi 离开 Slide 之后，回过头来觉得有一件事实在有点难以理解：当市面上已经有那么多的方案，为什么 Slide 与其对手们却都还在搭建自己的数据分析工具？要知道，光是 Slide 就有 8-10 人的全职数据分析团队和大把的软硬件设施，加起来一年可能近百万美刀的投入。显然，数据分析市场上的供需双方之间是有 Gap 的。

所以，他想：既然他们都有需求，每年投入那么多钱，为什么我不自己做一套，然后卖给他们呢？一半的价格就好，他们似乎没有理由不买，而且还有那么多公司没有资源自己搭建呢。

在之后，Suhail 就在同学里找了一个 Cofounder -- [Tim Trefren]，一起开干。

那么问题来了，市面上已经有了 Google Analytics 这么优秀这么流行的工具，Mixpanel 要作出什么样的不同才能赢得客户呢？Suhail 在采访中很实诚的讲到，一开始，他们其实并没有想出一个多么复杂的商业模型，也并没有做多么完整的市场研究，他们只是抓住了一个点：不再追踪 Pageview。

因为其他工具已经都在做 Pageview 的监测，并且做的非常好，对于 Mixpanel，他们要思考的是：要追踪什么，才能带给客户什么别的差异价值。不然，公司好像也没什么活下去的理由。

最终，他们觉得随着 AJAX 和 Web 2.0 的兴起，「页面」在网站当中的地位一定会越来越弱：比如在 Facebook 上看看照片，在 Twitter 上发发评论，在 Pandora 上听听歌，这些都没有什么新页面的 Loading，只是图片的过渡和浮层的交互。那么，如果用户行为不再着重体现为页面浏览，应该追踪什么才能更好的衡量用户的使用情况呢？「正确的度量单位可能是 Action、Event、或者在页面上发生的什么事情。」他们是这样觉得的。而且，Suhail 已经在 Slide 见识过追踪事件交互的好处，这是一个值得试一试的方向。

## Mixpanel 是如何创造需求的

关于 Event Tracking，我们来简单复盘一下当时的情况：

- 2007 年 10 月 16 日，Google Analytics 在 eMetrics 峰会上公布了一个新的 Feature — [Event Tracking]，并向一小部分用户开放测试，[可能是业内第三个推出此功能的厂商]
- 2009 年 6 月，GA 宣布，Event Tracking 正式[面向所有用户开放]
- 而像 Gmail、Facebook、Twitter 等 Single Page Application 已经非常普遍

显然，单论 Event Tracking，Mixpanel 做的不算早，但完全基于 Event 模型来做数据采集与分析，当时，应该只此一家了。现在来看，非常好理解，但在当时，给你这样一个工具：在页面`</head>`前贴上代码后，打开系统一看，却默认什么数据也没有。相信你也不会轻易的选择它。

所以，如何培育市场，说服用户抛弃理所当然的 Pageview，转而接受 Event/Engagement Tracking 这一观念，又是摆在 Mixpanel 眼前的一道难题。

这一次，他们盯上了媒体记者和风投，反复灌输「公司们应该停掉那些垃圾度量、那些东西没什么用、说明不了什么实际问题，应该赶紧来点实际的……你们完全可以问问 LinkedIn，他们号称的几百万注册用户当中，到底有多少是真正活跃的……」。

记者和风投很在乎数据，在乎质量，只要教育好了这两者，他们就会进而主动的去追问那些创业者和企业：你们最核心的指标是什么，我们不再相信别的不相干的什么东西了。

风投为了获得质量更高的运营数据来评估业务价值，也会督促自己参投的企业，甚至推荐 Mixpanel 的产品。如此循环往复，让 Mixpanel 得以逐步渗透硅谷，进而搅动这个行业。

要知道，最开始，他们连前端界面也没有，一百块钱一个月，自己拉取数据，通过 Gtalk 服务着有限的几个客户。到申请并进入 YC，到筹集到 $77M 的风投，到 2012 年实现盈利，到现在每月分析 67 billion actions，以及惠及了许多后来者的[开源项目]，Mixpanel 正实践着他们承诺的使命：

- We've built the most advanced analytics product for mobile & web.
- UR MISSION IS TO HELP THE WORLD LEARN FROM ITS DATA.

[Suhail Doshi]: https://twitter.com/suhail	"Suhai Doshi Twitter"
[The Macro: Startup School - Mixpanel]: http://www.themacro.com/articles/2016/08/startup-school-mixpanel/?utm_source=wanqu.co&amp;utm_campaign=Wanqu+Daily&amp;utm_medium=website	"Startup School Mixpanel"
[湾区日报: Mixpanel 如何创造需求的]: https://wanqu.co/a/3830/2016-08-28-how-mixpanel-created-demand-the-macro.html?s=rss	"Mixpanel 如何创造需求的"
[Tim Trefren]: https://twitter.com/ttrefren	"Tim Trefren Twitter"
[Event Tracking]: https://analytics.googleblog.com/2007/10/exciting-announcements-at-emetrics.html	"Event Tracking Announcement"
[面向所有用户开放]: https://analytics.googleblog.com/2009/06/event-tracking-now-available-in-all.html	"Event Tracking Available in All"
[可能是业内第三个推出此功能的厂商]: http://cutroni.com/blog/2007/10/16/event-tracking-pt-1-overview-data-model/	"Event Tracking Overview by Cutroni"
[开源项目]: https://github.com/mixpanel	"Mixpanel@Github"