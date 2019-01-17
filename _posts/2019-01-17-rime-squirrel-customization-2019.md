---
layout: post
title:  "鼠须管配置 2019"
date:   2019-01-17 03:03 +0800
---

时隔 4 年，鼠须管输入法再次更新，重拾鼠须管，并非 macOS 自带输入法不好，只是忍不住这爱折腾的手。这一次，我的需求笼统来讲，还是两个：好看、好用。

<img src="/files/2019/squirrel_screenshot_1.png" alt="squirrel_screenshot_1" width="70%">

好看，这一点很好满足，新版鼠须管增加了一个名为沙漠夜的主题，兼容 macOS Mojave，我仅做了一点调整就很符合预期了。具体见上右图，只是去掉了高亮候选词的样式，微调了字号及间隔。

好用，则麻烦一点，涉及细节比较多，主要是两块：词库、习惯配置。

## 词库

我的鼠须管用途只有一个，简体+小鹤双拼+常用字，因此这里的词库，并非是指各种第三方的庞大语料库，而是指字符集以及预置词典的配置，实现简体、常用词组、无太多生僻字的目标，简言之，够用就好。

实现简体和常用字输出有很多方式，比如之前的 cjk_minifier filter，后来的 charset_filter，还有我此次使用的暴力字典修改法。相比前两个方式，字典修改法虽然麻烦，但额外好处是既能控制字符集大小，又能避免了字符集过滤导致的 emoji 无法输出。比如采用 charset_filter@gb2312 虽减少了单字，但却兼顾不了 utf-8 才能覆盖的符号和表情。

鼠须管自带「明月拼音简体」，亦名「袖珍简化字拼音」，使用的是 pinyin_simp.dict.yaml 这一字典，包含约 17000 个单字以及 48000 个词语，有繁有简。这是我定制的基础，一开始的做法包含两步：一是借助 Opencc 化繁为简；二是去重合并，并只取 Unicode \u3400 至 \u9fa5 范围内的汉字。

通过这两步，能有效消除候选字词中的繁体和部分罕见字。如此清理之后，剩下约 14200 单字，词语都是简体，还是 48000 左右。

但这样的做法有问题，会导致「乾」的单字变「干」这一类的问题。之后，从 2013 版的[《通用规范汉字表》](https://zh.wikipedia.org/wiki/%E9%80%9A%E7%94%A8%E8%A7%84%E8%8C%83%E6%B1%89%E5%AD%97%E8%A1%A8) 入手，重新整理自己的字典，包含且仅包含规范汉字表内的 8105 个字。这些单字结合 pinyin_simp.dict 中原有的词语，组合成 56800 多条目的个人字典。由于苹方、思源等字体皆完整覆盖这些规范字，因此候选框也不再存在缺字导致的问好问题，最重要的是实现了「够用就好」的目标。

最后，采用网络上其他人的方法，外挂了一个自定义词库，放置一些常用又不方便输入的自定义词组，比如输入 macos，出现 macOS Mojave、macOS High Sierra 等，共计 50 来条。

定制过的 pinyin_simp.dict，加上一个 custom_phrase，加上日常使用的个人用语积累，再加上简要配置即可使用的 emoji，一共四个部分，至此，词库调教完成。

## 习惯配置

<img src="/files/2019/squirrel_screenshot_2.jpg" alt="squirrel_screenshot_2" width="90%">

这里所说的习惯，是指一些中文输入状态下的便捷配置。比如 emoji、符合、网址输入等。罗列起来，有以下几项：

- 去掉了笔画反查
- 增加了候选词 emoji
- 调整了一些半角符号映射
- 优化了中文状态下代码和网址等输入

所有配置文件地址：[https://github.com/placeless/squirrel_config](https://github.com/placeless/squirrel_config)

## 实现过程

先是词库和双拼方案。新版鼠须管不再预置全部输入方案，需要自行通过 [東風破 /plum/](https://github.com/rime/plum) 项目进行选配，有一点操作门槛，实际使用也不如预期的顺利。手工方法是自行去对应的项目地址下载所需文件，简单又可控。比如我根据的需求，涉及的文件如下，至于自定义短语，请参考网上其他人的文章。

- [rime-double-pinyin/double_pinyin_flypy.schema.yaml](https://github.com/rime/rime-double-pinyin/blob/master/double_pinyin_flypy.schema.yaml)
- [rime-pinyin-simp/pinyin_simp.dict.yaml](https://github.com/rime/rime-pinyin-simp/blob/master/pinyin_simp.dict.yaml)
- [rime-emoji/opencc/*](https://github.com/rime/rime-emoji/tree/master/opencc)

其次是字典的定制。字典包含 3 个部分，字词、拼音、排序权重。规范汉字和拼音数据，我参考了 [mozillazg/pinyin-data](https://github.com/mozillazg/pinyin-data) 这一项目，主要是 kMandarin_8105.txt 这个文件。绝大多数的拼音和权重，均匹配自鼠须管的 pinyin_simp.dict，因为对比之下，它更全面，剩下约有 626 个字不在里面，是以沿用 kMandarin_8105.txt 的拼音，权重则填充为 0。

最后是几点注意，一是字典文件的字段分隔符为 tab；二是当心 Pandas 读取数据时，把拼音 nan 当作了 NaN 😂️；三是处理音调时注意转 ü 为 v/u 的取舍。

