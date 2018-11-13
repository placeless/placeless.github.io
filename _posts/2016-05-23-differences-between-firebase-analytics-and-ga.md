---
layout: post
title:  "Firebase Analytics 与 GA 有什么不同？"
date:   2016-05-23 16:31:10 +0800
redirect_from:
  - 2016/05/23/differences-between-firebase-analytics-and-ga
---

* TOC
{:toc}

Google I/O 2016 Keynote 末尾，提到一个之前未曾听过的分析产品：Firebase Analytics，有两点印象深刻：

  1. **Free & Unlimited**
  2. 对于 Mobile APP，官方推荐使用 Firebase Analytics 而不是 Google Analytics（GA）

## 首先 Firebase 是什么？

根据 [Wikipedia](https://en.m.wikipedia.org/wiki/Firebase) 描述，[Firebase](https://firebase.google.com/) 是加州的一家提供云与后端服务的创业公司，主要产品是一个实时数据库，提供一组 API，允许开发者跨客户端存储和同步数据。Firebase 于 2014 年 10 月被 Google 收购，目前定位为：App success made simple，为 APP 开发者提供好用的工具与基础设施，如下图。

![firebase-features](/files/2016/05/23/firebase.png)

## Firebase Analytics 与 GA 是什么关系？

[GA 官方博客](http://analytics.blogspot.com/2016/05/app-first-analytics-with-firebase.html)提到：FA 与 GA for mobile apps 是分开的产品，两者都出自同一个 team，就是为了确保 GA、GTM、GA 360、以及 FA 能无缝衔接。

## FA 与 GA 有和不同？

同为监测分析产品，但 Firebase Analytics 衍生自 Firebase Platform，是完完全全为 Mobile APP 而生的，默认状态下即可采集「首次打开」「应用内购买」等信息。

它既包含了我们熟悉的 GA APP 用户行为、状态等全部数据，更是集成了更加易用的 APP 用户来源采集，且自动关联用户行为与来源渠道。但还没完，依托于 Firebase 平台，Crash Analytics、Notification 通通都有，结合 Audience Segmentation 与 Remote Config（类比 GTM），远程玩出花来。比如过滤出某一个地区的某一类用户，灵活调整道具价格；专门为「听过 200+ 首古典音乐」的用户定制个性化的 APP 内体验等等。可观看[此演示视频](https://www.youtube.com/watch?v=tb2GZ3Bh4p8)，切身体会。

一句话总结：更易用、更好用了。具体对比，Google 提供了简要[文档](https://support.google.com/analytics/answer/2587087?hl=en)，表单摘录如下：

| Firebase Analytics                       | Google Analytics 360                     |
| ---------------------------------------- | ---------------------------------------- |
| Event-based data collection model, designed specifically for apps | Screenview/pageview data collection model |
| Free, unlimited event reporting          | Analytics 360 hit volume limits and pricing apply to all data that is sent to Analytics 360 from websites or apps. |
| Integrated feature of Firebase, Google's mobile developer platform | Standalone analytics product, part of Google Analytics 360 Suite |
| Automatic measurement of "first open", in-app purchase, and [other key events](https://support.google.com/firebase/answer/6317485) | Developer must explicitly initialize screenview tracking and manually instrument events in the app |
| No roll-up of multiple apps              | Roll-up properties (including roll-up of both web and mobile app properties) |
| Not covered under Analytics 360 SLAs     | Covered under Analytics 360 SLAs         |

注：[roll-up reporting](https://support.google.com/analytics/answer/6096167?hl=en) 是 GA 360 才有的功能，能把预设好的多个 GA propoties 的监测数据，聚合至一个 property，在一个地方进行多源的总览和分析。

## 该怎么选择？

个人觉得，新老 APP，都应该毫不犹豫，尝试并部署 Firebase Analytics，哈哈。由于 Firebase Analytics 是基于 Firebase SDK 的，所以 Firebase 的[收费机制](https://firebase.google.com/pricing/)和[文档](https://firebase.google.com/docs/analytics/)，最好事先有所了解。
