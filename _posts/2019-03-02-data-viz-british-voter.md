---
layout: post
title:  "可视化作品推荐：分裂的选民"
date:   2019-03-02 05:50 +0800
---

2016 年，英国公投脱欧，去留两派 52% 比 48%，旗鼓相当的对立。2018 年底，时隔两年，人们对脱欧（Brexit）的看法可有改变？民调机构 YouGov 就此询问了 90,000 名英国成人。最近，《经济学人》基于这些数据做了一个人口统计模型，以计算个体对当初脱欧三选项的支持程度。这三个选项分别是：无协议硬脱欧（No Deal），梅姐的协议脱欧（May's Deal）、继续留在欧盟（Remaining in the EU）。

今天重点，不是这个瓜，而是这份数据的可视化作品：[Build a British voter](https://www.economist.com/graphic-detail/2019/02/22/profiles-of-a-divided-country "The Economist")

![remain supporter](/files/2019/remain_supporter.png)

## 阅读

10 个属性，其值交织搭配构成 2500 个细分画像组别（Profile），要落到上文提及的三选项的可能性强弱里。如何有效组织这些信息以便于读者提取呢？想想都难。多谢这些西方媒体和工作人员，开源了他们的数据和代码，让我们得以羡慕撮手、见贤思齐之余，还能细究思路和上手实践。

- [TheEconomist/graphic-detail-data](https://github.com/TheEconomist/graphic-detail-data/tree/master/data/2019-02-23_opinion-on-brexit "Github")

先看数据描述，性别、年龄段、性取向、家庭收入、教育程度、地区、房屋所有情况、政治兴趣、2015 投给哪党、2017 投给哪党，这是 10 个变量。pop 是组别人数，后面是 3 个选项的计算结果。

| Variable        | Description                                                  |
| --------------- | ------------------------------------------------------------ |
| sex             | female / male                                                |
| age_bucket      | age grouped into five buckets                                |
| lgbtq           | sexuality. straight; other                                   |
| inc_bucket      | total gross household income                                 |
| educ_n          | coded 1-5. 1 = no formal qualifications; 2 = secondary school; 3 = further educ; 4 = graduate; 5 = post-graduate |
| region6         | region of GB, split six ways                                 |
| tenure3         | housing tenure. own home outright; mortgaged home; rent      |
| poli_int_bucket | political interest: low; middling; high                      |
| ge15_5          | 2015 general election vote, split five ways                  |
| ge17_5          | 2017 general election vote                                   |
| pop             | population of group, rounded to nearest hundred              |
| pred_deal       | modelled probability of individual choosing "deal"           |
| pred_no_deal    | modelled probability of individual choosing "no deal"        |
| pred_remain     | modelled probability of individual choosing "remain"         |

作者采用了不那么常见的 Ternary Plot 这一形式，3 个选项对应三角形的 3 个顶点，各 Group 的倾向可能性以三元散点形式投射在这个三角形的内部，并辅以颜色区分可能性强弱和选项分类。

每个 Profile 的各变量连接起来就是一个故事，比如，一名 30-44 岁之间的直男，大学学历，家庭年收入 4 万英镑，住北部的抵押房屋，对政治有着浓厚兴趣，并于 2015 年和 2017 年都投票给工党。结合三元图，左上起，顺时针，Remain，No Deal，May's Deal，可能性为（0.81，0.12，0.07），他是一名典型的留欧支持者，以此类推。

![remain supporter](/files/2019/remain_supporter.png)

## 实践

开源仓库内提到的方案是基于 R 的，使用两个关键包，tidyverse 与 ggtern，前者管理数据，后者绘制三元图。这里的产出是未经修饰的静态图，不能实时交互。Python 上对应有 Matplotlib 以及基于其上的 Python-Ternary 等，看代码，功能和易用性上，目测还比不上 ggtern。交互库，典型的有横跨 R 与 Python 的 Plotly 等，但是似乎还不能实现这个例子中的面板翻转，都是屁股落地的三角形，用于此例效果不佳。

探测网页元素，预览 bundle.js，Web 端交互图表应该是基于 React 生态加一些第三方 JS 库来实现的。比如左边的下拉输入是 React-Select 组件，右边是 ReactDOM，颜色是 Chroma.js 等。

![react dom](/files/2019/react-tern.png)

数据新闻这个门类中，令人眼馋的可视化作品可真是太多了，推荐关注 [GIJN](https://gijn.org/ "Global Investigative Journalism Network") 的推，全世界的优秀脑洞，层出不穷，目不暇接。