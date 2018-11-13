---
layout: post
title:  "GA 期盼已久的 Content Groups 功能"
redirect_from:
  - 2013/12/14/google-analytics-content-group-introduction
---

一直以来, 在 Google Analytics 报告当中, 无论是想对内容进行分组统计, 还是对比分析, 都需要人工折腾一番, 耗尽心血得来的结果, 还不见得坦然.

比如一个电商网站, 想要做一些产品流量及销售的相关分析. 按性别, 分男女两类, 在此基础上, 再按年龄段, 产品线 (比如, T恤, 上衣, 裤子, 裙子等), 单品等, 做各个类别的流量销售数据的比对分析.

搁在以前, 要想得到一些一目了然的报告, 我们只从以下几处着手: 要么拆分出一些独立的 profiles, 要么创建一些高级细分, 对于URL不可读的, 像 /product.php?id=123 这种, 可能还要用到 Filter 查找替换为可读的字符串, 但是又失去了源页面 URL (因为已经被替换掉了), URL归类清理过的, 也需要人工搜索归类, 理出自己需要的数字.

现在, GA 官方发布了 Content Group 功能, 帮助分析人员, 按照自己的逻辑, 群组化自己的页面内容, 终于可以不用再面对着几千几万条千奇百怪的 URLs 发懵发怵了.

* [官方帮助文档]
* [Youtube 介绍视频]

## 如何应用 Content Group

要用上 Content Group 报表功能, 以下几个概念和逻辑, 需要理清, 光看文档, 可能会有一点摸不着头脑.

* Content Grouping & Content Group
* 需要做哪些部署
* 在 GA 的哪个地方能找到

### 什么是 Content Grouping & Content Group

Content Grouping 是用作 GA Primary Dimension的, 比如 Page, Landing Page, 在 GA 当中, 就都可以当作 Primary Dimension. 点开 Page 报告, 就能看到所有页面的名称和流量情况, 点开 Landing Page报告, 就能看到所有 Landing Page 的 URL 和 流量数据.

举个例子, 比如我们要做如下分类, 希望在 GA 系统内, 像点开 Landing Page 一样, 点开 "衣服", 即展开以下报告:

![Clothing Traffic](/files/2013/12/14/clothing.png)

这里隐含的 "衣服" 就是一个 Content Grouping, 像流量来源一样, 是一个大类名, 而 "男装", "女装" 则分别是 Content Group, 就像是 Direct 和 Referral 等. 男女装之于衣服, 就跟直接流量和引荐流量之于流量来源一样, 我们必须先设定一个叫做 "衣服" 的大筐子, 才好往里面放男女装等子元素.

### 需要做哪些部署

GA 一共提供了三种方式, 帮助我们创建 Content Group:

* 在部署监测代码时, 分组内容
* 通过正则表达式, 对内容进行分组
* 用简单规则编辑器定义内容群组

可以对以下三类内容进行 Group

* Page
* Page Title
* Screen Name

### 通过监测代码部署 Content Group

GA 一共支持5组 Content Grouping.

```js
// analytics.js
ga('set', 'contentGroup1', 'clothing');
ga('send', 'pageview');

// ga.js
_gaq.push(['_setPageGroup', 'Index Number', 'Group Name']);
_gaq.push(['_trackPageview']);
```

### 在 GA 的哪个地方能找到

目前只能通过自定义报告查看 Content Group 数据. 我们必须先在 GA 内找到并打开这么一个报告, 但是 GA 默认并没有 "衣服" 这一类的报告, 它是我们新创建的

[官方帮助文档]: https://support.google.com/analytics/answer/2853423?hl=en&ref_topic=1727167
[Youtube 介绍视频]: http://www.youtube.com/watch?v=auDIobdIeqs&feature=c4-overview&list=UUJ5UyIAa5nEGksjcdp43Ixw
