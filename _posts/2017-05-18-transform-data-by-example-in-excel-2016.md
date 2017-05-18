---
layout: post
title:  "Excel 数据整理新工具"
date:   2017-05-18 16:51 +0800
---

今天微软推出了一个贴心的 Excel 小工具，名为 **[Transform Data by Example](https://store.office.com/en-us/app.aspx?assetid=WA104380727)**，支持 Window 和 Mac 平台上的 Excel 2016。一句话描述：

> Example-driven search engine to find functions for your data wrangling tasks

即，当你需要批量整理不规则数据时，你仅需手写几条预期结果的样例，这个工具会根据你的样例，搜索出来一些自动处理函数，你在结果当中选择合适的即可。



## 安装

1. 点击 Insert 标签下 Store 图标
2. 搜索「Transform Data by Example」关键词
3. 点击「Add 」即可完成安装

在 Ribbon 栏的 Data 标签的最右边，会出现一个显眼的「Transform Data by Example」图标。

![Transform Data by Example Icon](/files/2017/05/18/transform-data-by-example.png)



## 使用

点开图标之后，有一个简单的三步介绍，一目了然：

1. 在结果栏输入几条预期结果
2. 鼠标定位到下边的空栏位
3. 点击转换工具中的「Get Transformations」按钮
4. 之后在给出的搜索结果里挑选、尝试合乎预期的转换功能即可



比如，下表 A 列的日期形式各异，想要整理获得一个统一的格式，我们仅需在 B 列的头几行输入想要的结果，在后面的空行运行工具，选择函数，接下来它会自动完成输入输出。

| A                         | B                     |
| ------------------------- | --------------------- |
| Wed, 12 Jan 2011 11:10:41 | 2011-01-12, Wednesday |
| 2010-Nov-30               | 2010-11-30,  Tuesday  |
| 3/31/2013                 | 2013-03-31, Sunday    |
| 07/11/2017 14:33          |                       |



## 问题

看起来还不错，比 [OpenRefine](https://github.com/OpenRefine/OpenRefine) 还容易上手，简直不需要动脑筋。但实际拿自己的数据做尝试时，还是遇到了一些问题：

1. 数据安全：搜索需要上传数据
2. 结果验证：搜索结果描述有限，应用完之后结果验证少不了
3. 搜索准确性：复杂一点的需求找不到结果
4. 筛选效率：因为描述有限，筛选、试错的时间成本也不少

下表是我从 Google Analytics 导出的一些数据片段。我需要重组时间，合并 date、hour、minute，并将其转换为 Unix Timestamp 以备后续应用。以往是通过 Google Sheets 编写脚本来处理，截至目前的试用，Excel 的 Transform by Example 还不能轻易满足这个需求，可能不够普遍，线上还没有相关的 functions 可供搜索。

| uid  | date     | hour | minute | result              | ts         |
| ---- | -------- | ---- | ------ | ------------------- | ---------- |
| 1    | 20170412 | 14   | 58     | 2017-04-12 14:58:00 | 1491980280 |
| 2    | 20170504 | 22   | 37     | 2017-05-04 22:37:00 | 1493908620 |
| 3    | 20170511 | 22   | 46     | 2017-05-11 22:46:00 | 1494513960 |
| 4    | 20170329 | 14   | 15     | 2017-03-29 14:15:00 | 1490768100 |
| 5    | 20170426 | 18   | 41     |                     |            |
| 6    | 20170406 | 13   | 41     |                     |            |
| 7    | 20170425 | 13   | 44     |                     |            |

当然，根据介绍，Transform Data by Example 也是可以扩展的，你可以通过 Azure Function 贡献代码，也可以直接添加已有的 Web Service 到 Transform Data by Example，比如[介绍视频](https://www.microsoft.com/en-us/research/project/transform-data-by-example/)里提到的处理浏览器的 User Agent 字串，可以通过添加 [User Agent String API](http://www.useragentstring.com/?uas=UAS&getJSON=all) 来解决。



总之，Transform Data by Example 至少提供了一个，类似「点击两下」即能帮你在 Excel 中，处理好一些常见数据清理问题的方案，而且可扩展的，基于搜索的，随其知识库的扩充，肯定会越来越趁手。