---
layout: post
title:  Onboarding 的两个含义
date:   2018-05-03 18:55 +0800
---

有的时候，新事物还没有现实的对应词汇来描述，所以另起一个新的名字，专门用来指代它；而有的时候，事物并没有什么大的改变，为抓人眼球，只能从包装上想办法，于是人们挪过来别的词汇套在它身上，半通不通的，还让你颇感韵味。

## 在线广告中的 Audience Onboarding

在线广告或是「大数据」行业屡屡提及的 Onboarding，我的理解，应该都是指 Audience Onboarding，即数据连接的意思。大致步骤是：

1. 你：上传用户或潜客名单到 FB、Twitter，或是第三方数据服务商，如 Acxiom、Liveramp 等
2. 平台：将会把你的名单与其自身数据库进行匹配，产出映射结果
3. 平台：匹配出来的用户，你认识，平台也认识，平台正是据此把广告投送给用户，Onboarding 主要就是为了做这么一件事

这些名单，通常是手机号、邮箱、Twitter ID 等，相当于在 Cookie Mapping 之外，多提供一条认识、联通离线用户的渠道。目的还是方便你花钱，方便你把在 Facebook、Twitter、Google 的小按钮安插不到的地方采集到的用户数据，能通过它们的网络进行「精准」投放。若是种子用户体量过小，当然还可以据此做 Lookalike，进行受众扩充。

![Audience Onboarding](/files/2018/05/03/audience_onboarding.svg)

## 产品使用引导中的 User Onboarding

产品设计与运营中常提到的 User Onboarding，含义又不同。为了吸引和留住用户，产品交到用户手上，一般需要考虑好两个问题：

1. 用户点开的第一时间，如何抓住他/她们
2. 后续的使用当中，如何适时、恰当的引导

引导，我的理解，即是此处 Onboarding 的含义。

日常生活中，「使用引导」在互联网产品上很常见。怎么实现？以 Firefox 为例。初次启动或是每当有功能升级，Firefox 都会有一个浮层弹出，引导用户了解和体验这些特色之处。比如下图 Let's get started 中的六个模块、旁边的图文介绍、右下角高亮出来的 CTA 按钮。通过这一过程，即能及时让用户去使用这些新功能，也能及时采集用户的反应，为后续的优化提供数据指导。

![Firefox Onboarding Screenshot](/files/2018/05/03/firefox_onboarding.png)

Firefox 的 Onboarding 策略可以通过 `about:config` 去了解：针对哪些功能点，以什么方式通知用户，用户完成的怎么样，这些都记录在 Config 里面，是一个很好的研究材料。

![Firefox Onboarding Configuration](/files/2018/05/03/firefox_config.png)

此外，有一位叫作 Julian Shapiro 的人，有一本在线增长手册，也专门有谈及 [User Onboarding](https://www.julian.com/learn/growth/user-onboarding)。