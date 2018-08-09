---
layout: post
title:  微笑曲线
date:   2018-08-09 14:28 +0800
---


## DAU/MAU

DAU/MAU 是一个常见的度量，用于衡量一个应用的用户黏度，即，用户的使用到底有多频繁。其计算方法是`日活/月活`，得到一个介于 0-1 之间的结果，可以直接使用小数，也可以化作百分比，总之，数字越高说明用户越活跃，超过 0.2 已经是相当不错，若是达到了0.5，那就是世界级的成就了，比如 Facebook，自面世以来，它常年雄踞在 0.5 以上，也正是因为 Facebook，这个度量才变得如此流行。

<p style="text-align: center; margin: 3em auto;">
<span style="background: #D5E1EC; color: #3d4351; padding: 6px 10px;">(#) Daily active users</span> 
<span style="background: #5C6B78; font-weight: 500; padding: 6px 10px;">/</span> 
<span style="background: #D5E1EC; color: #3d4351; padding: 6px 10px;">(#) Monthly active users</span> 
<span style="background: #5C6B78; font-weight: 500; padding: 6px 10px;">=</span> 
<span style="background: #8dc63f; padding: 6px 10px;">(%) DAU/MAU Ratio</span>
</p>

那么，日活除以月活，怎么就衡量了用户的黏度呢？是这样解释的，首先，要定义清楚何为「活跃」，比如是启动一次应用，还是登录一次帐号，还是触发某一个特定的动作等等。清楚定义了「活跃」，再进入具体的计算，比如说我们的应用自今日回滚一个月，陆陆续续的活跃用户汇总有 100 个，今天我们要看 DAU/MAU 用户黏度，从用一次就再不回来的下限，到爱不释手天天要用的上限，可能有三种情形：1. 天天要用，100 个人都回来了；2. 还不错，部分回来了，比如 40 个；3. 一次性使用，因为没有回头客，相当于每天的活跃用户（DAU）等于每天的新用户，DAU/MAU 的结果完全取决于每日新增，我们假设用户增长已经趋于平静，每天一样多。那么，有以下 3 个结果：

- DAU/MAU=100/100=100%
- DAU/MAU=40/100=40%
- DAU/MAU=DAU/(DAU*30)=1/30=3.33%

也可以反过来理解，3.33% * 30 天 = 1 天，即，一个月中，用户只有 1 天是活跃的。虽然有一点绕，但它以一个相对独立单纯的指标，即实现了应用的年度评估，所以应用广泛。

## DAU/MAU 的不足

DAU/MAU 固然好用，但它也不是没缺点：1. 许多高质应用，属性使然，一周只需要用个一两次，比如一些购物、娱乐应用等，再比如 LinkedIn，很多人也只有在找工作时才刻意打开它，DAU/MAU 低了，不见得是产品不好；2. 它是一个聚合维度，无法反映出人群的差异，比如不少 DAU/MAU 低的产品实际上还是有忠实的日用群体（Power User）


## Power User Curve

所谓 Power User，简言之，即忠实的活跃用户，几乎天天在使用我们产品的一群铁杆。区别于 DAU/MAU，Power User Curve 图是把月活（MAU）按活跃天数分布，摊开成一条曲线，如果你的产品存在高频的忠实用户，那么在30 天曲线的尾巴部分，它一定会扬起来，作😊状（如下图），这是标题「微笑曲线」的由来，详细探讨，请参考 [Andrew Chen 原文——The Power User Curve: The best way to understand your most engaged users](https://andrewchen.co/power-user-curve/)。

![curve](/files/2018/08/09/the_smile_curve.svg)

相对于 DAU/MAU，它有几个优势：

- 展示出了忠实用户群体（有或无）
- 展示出了从轻到重每一级的活跃比例（活跃天数）
- 还可以结合 Cohorts 来洞悉曲线的迁移（比如八九十月曲线叠一起）
- 也可以将这个「Power」挂靠到某个具体到核心动作/任务上（比如成单）
- 再综合一些细分/过滤条件，更是一个良好的洞察手段

## 小结

微笑曲线相当于把 DAU/MAU 的用户黏度摊开了看，把用户按照活跃天数分了群组，分别展示出各组的活跃占比，方便大家解读、洞悉其中的问题与机会，它是一个不错的分析方法，但如果你是在找关键指标（KPI），曲线还是不如维度方便运营和传播的。