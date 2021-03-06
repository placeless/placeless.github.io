---
layout: post
title:  "如何衡量可视化的有效性"
date:   2017-10-20 17:34 +0800
redirect_from:
  - 2017/10/20/data-viz-effectiveness-measures
---

看到一篇 [GIJN](https://mobile.twitter.com/gijn) 转发的文章，很有意思，作者是 [Stephen Few](http://www.perceptualedge.com/blog/)，标题是《Data Visualization Effectiveness Profile》，[PDF 原文在此](https://www.perceptualedge.com/articles/visual_business_intelligence/data_visualization_effectiveness_profile.pdf)。

提到可视化，我们的言下之意，总不外乎：对信息进行加工处理，套以良好的视觉形象，便于人眼解读，便于信息的传递。但是，可视化过后的产物丰富多彩，不胜枚举，到底哪一个更有助于理解，又如何去衡量呢？

在日常生活和工作当中，大家都是凭各自的直觉和个人喜好，好像并无客观标准。所以，Stephen Few 针对这一议题，提出了自己的准则，并细分出了度量的维度，且做了范例解读。

他的准则是：可视化一定要有效。怎么才算有效呢？他提出了 7 个打分项。

- 信息量（Informative）
  - 实用性（Usefulness），图表有没有用
  - 完整性（Completeness），数据全不全
  - 辨识度（Perceptibility），阅读难不难
  - 真实性（Truthfulness），观点准不准
  - 直观性（Intuitiveness），形式常不常见
- 感染力（Emotive）
  - 美观度（Aesthetics），外观好不好看
  - 参与度（Engagement），是否吸引用户参与解析

上面的翻译不一定准确，每一项具体的解释，原文都有涵盖。虽看起来也都是一些主观看法，但一份可视化作品拿出来，它一定是已经怀有了特定目的，它一定是为了明确表述、传达某个观点的，所以，作为受众，还是可以据此打出自己的分数。当然，如果它没有什么目的，表达含糊，那这个图表它就是无用的。

他举的第一个例子，图表如下：

![example chart](/files/2017/10/20/example.jpg)

各项评分如下图。

![data visualization effectiveness profile](/files/2017/10/20/effectiveness.jpg){:width="80%"}

理由则是：

- Usefulness：销售收入诚然重要，但单独一个销售数据作用有限
- Completeness：因此，数据上它也是不完整的
- Perceptibility：3D 效果使人分神，柱状图也不如线条来的更显变化趋势
- Truthfulness：缺少历史数据的佐证，纵轴也不是从 0 开始，真实性大打折扣
- Intuitiveness：它是一看就懂的图，但纵轴更准（为 0 ）就更好
- Aesthetics：比较取悦眼睛，但柱子顶上挤到一起的数据标签有所失分
- Engagement：整个图表让人分神，使人忽略对数据的校验和解读，这块问题也大

之后，Stephen 又提到了[桑基图](https://en.m.wikipedia.org/wiki/Sankey_diagram)的始祖，[Charles Minard](https://en.m.wikipedia.org/wiki/Charles_Minard) 的知名大作：拿破仑进军莫斯科。其实这张图远远不只桑基图这么简单，它是一个时间序列，它是一张局部地图，它是一个完整的故事，它还布满了详尽的数字，它在二维空间里置入了军队人数、军队位置、行军方向、距离、时间、温度等多个变量，「它可能是有史以来最好的统计图形—《The Visual Display of Quantitative Data》」。

![Minard](https://upload.wikimedia.org/wikipedia/commons/2/29/Minard.png)

对于这幅可视化作品，除了 Intuitiveness，其他各项，Stephen 均给了满分，详情请参考原文。

上面是法文原版，当初只是一个双色的小海报，它的英译版可以在[这里](https://en.m.wikipedia.org/wiki/Charles_Joseph_Minard)找到。这次对俄作战，法军损失惨重。1812 年 6 月，拿破仑 42 万 2 千人从波俄边境的尼门河出发，形状的宽度代表军队体量，9 月抵达莫斯科，还剩下 10 万人。接下来转折的黑色代表撤军路线，天寒地冻，持续减员，到 Bobr 之时，仅剩 2 万人，但之前分派出来护卫侧翼的 3 万余部加入，队伍重新扩充至 5 万人，很快气温骤降，渡过 Studzionka 河后，又剩不到 3 万，最后 4000 人活着回到 Karnas 跟殿后的 6000 残部凑成 1 万，仓惶逃出俄国。

最后，今天看到一个图，可以作为眼力练习：[New York Historical Weather](https://bl.ocks.org/emeeks/raw/b57f4cc89dacd38fcdcd/)

更新：

- 10/27，今天看到 GIJN 官方编译的[中文版本](https://cn.gijn.org/2017/10/27/怎么标准化数据可视化之美？-数据新闻精选/)了，解读的比我好

