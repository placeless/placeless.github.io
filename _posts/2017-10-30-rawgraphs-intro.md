---
layout: post
title:  "可视化工具：Rawgraphs"
date:   2017-10-30 13:16 +0800
---

数据有什么用？数据新闻是明证之一。[Global Investigative Journalism Network](https://gijn.org/)（[全球深度报道网络](https://cn.gijn.org)）上面有许多精彩报道，是通过数据分析结合可视化呈现出来的，严谨、优美、交互性又强，阅读起来令人陶醉。

比如今天看到的这一篇《[IT'S NOT SO ISIS](http://labs.densitydesign.org/ddxii/es01/group04/index.html)》，记者选择了 120 个恐怖组织，自 1998 年至 2015 年的活动数据，将其按意识形态派别分类，来描绘这 18 年以来恐怖袭击的演化情况。数据翔实公开，图表直观精美，逐步剖析，引发思考。

## 工具：Raw

不知道报道中的图表是如何实现的，没有看到相关介绍。以前面两张 Bump Chart 为例，Excel 显然是没有提供直接生成工具的，Python 的可视化库，比如 Matplotlib、Seaborn 等文档内也没有看到范例，Google 之后，找到一个叫做 [Rawgraphs](http://rawgraphs.io/) 的开源工具：

> The missing link between spreadsheets and data visualization.

产品即可以在线使用，也可以从 `clone` 到本地，自己架一个 localhost 来离线运行。到目前，一共提供了 21 类新颖图表的自动生成功能，并且支持有编码能力的人士去创建[自定义图表](http://rawgraphs.io/gallery/)。使用过程设计的非常简单，一看便会：

1. Load your data
2. Choose a chart
3. Map your dimensions
4. Customize your visualization
5. Download / embed

主要的难点，来源于对 Chart 的理解。即选用什么图表，心里得清楚一些前提：知道这个图表是展示什么东西用的，对数据的要求是什么，由哪些元素组成。

## 例子：Bump Chart

以开篇报道中的第一个 Bump Chart 为例。目的是展示 18 年以来，三类恐怖组织发起的袭击数量变化，是一个很好的排名起伏变化的 Bump Chart 应用场景。而 Bump Chart 有三个必要条件：Group、Date、Size。下载整理文中提供的数据，得到下表：

| group     | year | counts |
| --------- | ---- | ------ |
| religious | 1998 | 49     |
| religious | 1999 | 58     |
| ...       | ...  | ...    |

按照上述步骤，上传或粘贴数据到 Raw，选择 Bump Chart，将对应字段拖到对应栏位，调整图表大小、颜色等，最后或下载 png、svg 图片，或下载 json 数据，或直接嵌入 svg 代码。

![example1](/files/2017/10/30/example1.svg)

由于产出的是 svg，对图中不如意的地方进行修改也变得非常方便，比如上图的 1998，可以通过文本编辑器，或者 Sketch 等矢量编辑器，稍加调整其元素的 x 属性，让其往右移一点，得以完整显示。

## 例子：Alluvial Diagram

再看一个例子，题目是：图表展示哪些国家分别受哪些恐怖组织的什么样的方式袭击。这类 step by step 的关系，适合的图表是桑基图，Raw 恰好有桑基图的一种实现：Alluvial Diagram。

![example2](/files/2017/10/30/example2.svg)

制图过程同上，理解 Alluvial Diagram 的 Node、Step、Size 即可。

## 总结

[Raw](https://github.com/densitydesign/raw) 是一个直观、高效、几乎零基础上手、并且[极具弹性](https://github.com/densitydesign/raw/wiki/Adding-New-Charts)的可视化工具，它在常见图表之外，为一些特殊的、独到的图表提供了一套快速生成工具。

Raw 的帮助文档也不错，简明易懂，是一个学习、了解这些图表的好去处。由此再逐步去了解后面的 d3 等，应该更有画面。

Raw 不像 Matplotlib 及其衍生工具，对图表的 figure、axis 等方方面面，巨细无遗；它没有在前端提供那么多可调节的选项，但因此，可视化的实现也更快速，况且由于产出的是 svg，加个标题，微调下坐标，做一点标注，都不是很难修改的东西。