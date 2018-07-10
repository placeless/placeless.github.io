---
layout: post
title:  数据可视化：坐标系的尺度
date:   2018-06-26 18:34 +0800
---

![piano frequencies](https://upload.wikimedia.org/wikipedia/commons/2/2e/Piano_Frequencies.svg)

<figcaption><a href="https://zh.wikipedia.org/zh-hans/%E9%8B%BC%E7%90%B4%E9%8D%B5%E9%A0%BB%E7%8E%87">琴键与频率</a></figcaption>

> Don’t compare percentage change on a linear scale; use a log scale instead. -50% (0.5×) is as big a change as +100% (2×). 比较比例变化，不要在线性尺度上，而要在对数尺度上。因为减少了 50% 与增加了 100% 是一样大的变动。

上面的引文，是 5 月初 [Mike Bostock](https://en.m.wikipedia.org/wiki/Mike_Bostock) 的[一条推特](https://twitter.com/mbostock/status/991517711250305024)，但我当时并没有看明白他是啥意思，在心里一直留了一个疑问。它引发了很多的后续讨论，除了推上断断续续的松散对答，还有人花时间撰写了长文，比如 5 月 31 日至 6 月 14 日之间 [Lisa Charlotte Rost](https://twitter.com/lisacrost) 就在 Datawrapper Blog 上连作 3 篇关于「How to read a log scale」的文章阐述自己的看法，分别是 [Growth rate](https://blog.datawrapper.de/weeklychart-logscale/)、[The chart that can’t start at zero](https://blog.datawrapper.de/weeklychart-logscale2/)、[What Mike Bostock means](https://blog.datawrapper.de/weeklychart-logscale3/)。对于这些讨论，Mike Bostock 在 6 月 16 日又写了一篇文章，亲自阐述那条 tweet 的含义，名为：[Methods of Comparison, Compared](https://beta.observablehq.com/@mbostock/methods-of-comparison-compared)。

Log Scale，Linear Scale，Percentage Change，Rate Change，Growth Rate 等等大大小小提及的概念还不少，那么，他们到底在谈些什么呢？其实，我感觉上面两人讲的并不是完全是一个东西，归结起来大致是两项（还是有点绕）：

- 如何对比新旧数据之间**比例的变化**
- 数据可视化中的**坐标尺度**的选择

Lisa 行文更偏重于解释图表坐标里的「Log 刻度」，第一篇引用了新西兰游客人数逐年变化的例子，很直观讲了对数刻度是什么以及使用它有什么好处；第二篇则引用了摩尔定律涉及的晶体管数量变化，讲了使用对数刻度的场合和需要注意的情况，；



## 工具

Mike Bostock 是 D3 的作者，他演示用的代码都是 D3，操作平台也是自己的新项目 [Observable](https://observablehq.com/)。我还没有玩过 D3，以下内容如有涉及绘图，采用的将会是最近在玩的 [Altair](https://altair-viz.github.io/)。

## 比例变化

用比例形式去比较两个值，一般有[三个形式](https://www.mathsisfun.com/data/percentage-difference-vs-error.html)：

- 变化（Percentage Change）：新比老，老为参照值
- 误差（Percentage Error）：预估比实际，实际为参照
- 差异（Percentage Difference）：与均值做对比

上文争论的是第一个情况。比如 Mike Bostock 所提及的例子：全美酒精滥用致死情况，2014 vs. 1980。文中，他用了 4 种方式来展现二者的差异：

- 并排做两幅图
- 同一幅图展示两者差值 `difference(a, b) = b − a`
- 同一幅图展示两者相对变化 `relative change(a, b) = (a - b) / a` 
- 同一幅图展示两者比例变化 `ratio(a, b) = a / b` 

哪一个更好？他说，没有直接答案，因为方法并无高下之分，推荐大家根据自身的需求，去选择最合适的表现方式。

<details>
<summary>Code</summary>
<pre>
<code>
alt.renderers.enable('notebook')
alt.Chart(df).mark_line().encode(
    x='Date:T',
    y=alt.Y('Close')
)
</code>
</pre>
</details>

## 坐标尺度

这里的坐标指的是图表中的 `x`、`y` 轴

vega_datasets 缺 46102 county

## Linear vs. logarithmic

[Google](https://www.google.com/publicdata/explore?ds=d5bncppjof8f9_&met_y=sp_dyn_tfrt_in&hl=en&dl=en#!ctype=l&strail=false&bcs=d&nselm=h&met_y=sp_dyn_cbrt_in&scale_y=lin&ind_y=false&rdim=region&idim=country:CHN&ifdim=region&tdim=true&tstart=-300441600000&tend=1435161600000&hl=en_US&dl=en&ind=false) [Yahoo](https://finance.yahoo.com/chart/AAPL#eyJpbnRlcnZhbCI6ImRheSIsInBlcmlvZGljaXR5IjoxLCJ0aW1lVW5pdCI6bnVsbCwiY2FuZGxlV2lkdGgiOjEsInZvbHVtZVVuZGVybGF5Ijp0cnVlLCJhZGoiOnRydWUsImNyb3NzaGFpciI6dHJ1ZSwiY2hhcnRUeXBlIjoibGluZSIsImV4dGVuZGVkIjpmYWxzZSwibWFya2V0U2Vzc2lvbnMiOnt9LCJhZ2dyZWdhdGlvblR5cGUiOiJvaGxjIiwiY2hhcnRTY2FsZSI6ImxpbmVhciIsInBhbmVscyI6eyJjaGFydCI6eyJwZXJjZW50IjoxLCJkaXNwbGF5IjoiQUFQTCIsImNoYXJ0TmFtZSI6ImNoYXJ0IiwidG9wIjowfX0sInNldFNwYW4iOm51bGwsImxpbmVXaWR0aCI6Miwic3RyaXBlZEJhY2tncm91ZCI6dHJ1ZSwiZXZlbnRzIjp0cnVlLCJjb2xvciI6IiMwMDgxZjIiLCJyYW5nZSI6bnVsbCwic3ltYm9scyI6W3sic3ltYm9sIjoiQUFQTCIsInN5bWJvbE9iamVjdCI6eyJzeW1ib2wiOiJBQVBMIn0sInBlcmlvZGljaXR5IjoxLCJpbnRlcnZhbCI6ImRheSIsInRpbWVVbml0IjpudWxsLCJzZXRTcGFuIjpudWxsfV0sInN0dWRpZXMiOnsidm9sIHVuZHIiOnsidHlwZSI6InZvbCB1bmRyIiwiaW5wdXRzIjp7ImlkIjoidm9sIHVuZHIiLCJkaXNwbGF5Ijoidm9sIHVuZHIifSwib3V0cHV0cyI6eyJVcCBWb2x1bWUiOiIjMDBiMDYxIiwiRG93biBWb2x1bWUiOiIjRkYzMzNBIn0sInBhbmVsIjoiY2hhcnQiLCJwYXJhbWV0ZXJzIjp7IndpZHRoRmFjdG9yIjowLjQ1LCJjaGFydE5hbWUiOiJjaGFydCJ9fX19) 都提供了 Y 轴的 Log 刻度