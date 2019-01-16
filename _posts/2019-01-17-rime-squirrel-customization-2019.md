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

实现简体和常用字输出有很多方式，比如之前的 cjk_minifier filter，后来的 charset_filter，还有我此次使用的暴力字典修改法。相比前两个方式，字典修改法虽然麻烦，但额外好处是既能控制字符集大小，又能避免了字符集过滤导致的 emoji 无法输出。比如采用 charset_filter@gb2312 虽减少了单字，但兼顾不了 utf-8 才能覆盖的符号和表情。

鼠须管自带「明月拼音简体」，使用的是 pinyin_simp_dict.yaml 这一字典，包含约 17000 个单字以及 48000 个词语，有繁有简。这是我定制的基础，包含两步：一是借助 Node.js + Opencc 化繁为简；二是借助 Python + Pandas 去重、去 Unicode \u3400 至 \u9fa5 范围之外的汉字。通过这两步，有效消除了候选字词中的繁体和部分罕见字导致的「？」。如此清理之后，剩下约 14200 单字，词语都是简体，还是 48000 左右。

> 当心 Pandas 读取数据时，把拼音 nan 当作了 NaN 😂️

最后，采用网络其他人的方法，外挂了一个自定义词库，放置一些常用又不方便输入的自定义词组，比如输入 macos，出现 macOS Mojave、macOS High Sierra 等，共计 50 来条。

至此，词库调教完成，即定制过的 pinyin_simp_dict 加上一个 custom_phrase 一些个人习惯用语，慢慢积累即可。

## 习惯配置

<img src="/files/2019/squirrel_screenshot_2.jpg" alt="squirrel_screenshot_2" width="90%">

这里所说的习惯，是指一些中文输入状态下的便捷配置。比如 emoji、符合、网址输入等。罗列起来，有以下几项：

- 去掉了笔画反查
- 增加了候选词 emoji
- 调整了一些半角符号映射
- 优化了中文状态下代码和网址等输入

配置文件地址：[https://github.com/placeless/squirrel_config](https://github.com/placeless/squirrel_config)