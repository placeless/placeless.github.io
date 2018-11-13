---
title:  "AppStore Reporting Instructions"
layout: post
redirect_from:
  - 2013/05/17/app-store-reporting-intro
---

![AppStoreDashboard](/files/2013/05/17/AppStoreDashboard.png)

上图，是Apple为开发者提供的iOS App销售报告，登录 [iTunes Connect][iTunes Connect]，进入 Sales and Trends 即可查看。有Daily，Weekly， Monthly跟Yearly四种报告形式，但不能自由选择时间段。Dashboard人d可从Apps，In-App Purchases跟Updates三个维度，迅速了解各个App产品，在不同市场（中国，美国，台湾和香港等）上的用户购买情况。Sales则是更为详尽的销售信息（如下图）。

![AppStoreSales](/files/2013/05/17/AppStoreSales.png)

初次接触的用户，有几个地方需要注意：

### 报告时段
官方提供的文档称：Monthly report在上月结束5天之后提供；Yearly是6天；Weekly是次周一，周算法是周一至周日；Daily是当日查看前一天的报告，也许是跟帐号时区设置有关，今天17号，快19点了，却还只能看到15号的数据。此外，跟文档描述不同的是，Daily reports大约保存最近30天的数据，不是14天；Weekly约保存半年，不只13周；Yearly数据跟文档提到的一样，是一直存在的。Apple提供了 [Auto-Ingest Tools][Auto-Ingest Tools]，帮助用户自动下载这些报告。

### 怎么理解Units
最初百思不得其解。通常，我们接到的Brief都是，给我看一下某某App的下载量。Units是Downloads的意思吗，In-App Purchases（应用内购买）算Downloads吗，Updates呢？最终，在 [AppStore Reporting Instructions][AppStore Reporting Instructions] 的最后几页找到了相关描述。

> Units: The number of units sold for a given day or week.

这下有谱了，App的初次购买，应用内购买，退货（Units有负数的），肯定都是sales了，但是疑问依然存在：升级呢（应该也是，报告里面有体现），重装呢，同一Apple ID设备间的同步呢？不管怎样，将`Units`数据当作App的下载量，应该是不严谨的。或者，我们应该换个思路，把目光从下载量移到销售量上来。

### Product Type是什么意思
Sales报告会根据App名称（Title），版本（Version），Product Type Identifier（类型），市场价格等因素的变动，分行列出销售明细。那么报告当中的1T，1F分别代表什么呢？以下是各种ID对应App付费类型及描述。

Product Type Identifier | Type | Description
:-----------: | ------------: | ------------:
1 | Free or paid app | iPhone and iPod touch (iOS)
7 | Update | iPhone and iPod touch (iOS)
IA1 | In-App Purchase | Purchase (iOS)
IA9 | In-App Purchase | Subscription (iOS)
IAY | In-App Purchase | Auto-renewable subscription (iOS)
IAC | In-App Purchase | Free subscription (iOS)
1F | Free or paid app | Universal (iOS)
7F | Update | Universal (iOS)
1T | Free or paid app | iPad (iOS)
7T | Update | iPad (iOS)
F1 | Free or paid app | Mac app
F7 | Update | Mac app
FI1 | In-App Purchase | Mac app
1E | Paid app | Custom iPhone and iPod touch (iOS)
1EP | Paid app | Custom iPad (iOS)
1EU | Paid app | Custom universal (iOS)


[iTunes Connect]: https://itunesconnect.apple.com
[Auto-Ingest Tools]: http://apple.com/itunesnews/docs/Autoingestion.class.zip
[AppStore Reporting Instructions]: https://dl.dropboxusercontent.com/u/25292293/AppStoreReportingInstructions.pdf
