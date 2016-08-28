---
layout: post
title:  "鼠须管生僻字过滤"
date:   2016-08-29 05:21 +0800
---

作为一个输入法，为避免「缺字」，鼠须管采用了大字符集，优先保障用户想要打的字都能打得出来。但这样做有一个副作用：由于多数的字体文件都只会覆盖一部分常用字，而不会针对生僻字耗费时间精力去设计字形，因此，会造成鼠须管的候选区内，总有不少难看的方框问号，像下图那样。

<img alt="uncommon character" src="/files/2016/08/29/uncommon_character.png" width="90">

避免这个问题有两个办法：

1. 安装超大字符集的字库，比如[「花园明朝」](http://fonts.jp/hanazono/)
2. 过滤掉这些生僻字，不让它们显示

在当前版本的鼠须管中，过滤生僻字的通用做法是：打开 `enable_charset_filter`，并在  `engine/filters` 内增加 `cjk_minifier`，即仅启用[ CJK 基本集](https://zh.wikipedia.org/wiki/中日韓統一表意文字)的两万九百多个汉字，来达到屏蔽生僻字的目的。

```yaml
patch:
  # double_pinyin_flypy.custom.yaml, 只对小鹤双拼生效
  # ...

  # 启用罕见字過濾
  engine/filters:
    - simplifier
    - uniquifier
    - cjk_minifier                        # 输入方案的 fitlers 内增加这一行
  translator/enable_charset_filter: true  # 并打开这个开关
  
  # ...
```

但是，在尚未发布的开发版中，这个方法似乎不再适用了，于是我自己摸索了一个方法：将字典由 `luna_pinyin` 改为 `pinyin_simp`，并启用 `zh_simp` 来做繁简转换。

```yaml
patch:
  switches:
    - name: ascii_mode
      reset: 0
      states: ["中文", "西文"]
    - name: full_shape
      states: ["半角", "全角"]
    - name: zh_simp                      # 启用罕见字過濾 ※1
      reset: 1
      states: [ 漢字, 汉字 ]
    - name: ascii_punct
      states: ["。，", "．，"]

  engine/filters:
      - simplifier@zh_simp                # 启用罕见字過濾 ※2
      - uniquifier
  translator/dictionary: pinyin_simp      # 启用罕见字過濾 ※3
```

这一办法着实去掉了讨厌的问号方块，但不足也有两处：

1. 繁简转换不够完美，比如：鎔
2. 还有很多繁体字、生僻字，比如：開、开

<div style="text-align:center;"><img src="/files/2016/08/29/rong.png" width="90" style="display: inline-block; margin-right: 30px;" ><img src="/files/2016/08/29/kai.png" width="90"  style="display: inline-block; margin-left: 30px;"></div>

后来，我提了一个 [issue](https://github.com/rime/squirrel/issues/120)，并且很快得到了答复：原来新版演化出了更为灵活的字符集过滤。于是参考范例，结合自己的需求，做了如下配置：

```yaml
patch:
  switches:
    - name: ascii_mode
      reset: 0
      states: ["中文", "西文"]
    - name: full_shape
      states: ["半角", "全角"]
    - name: simplification            # (※1) 繁简转换 
      reset: 1
      states: [ 漢字, 汉字 ]
    - name: ascii_punct
      states: ["。，", "．，"]
    - options: [utf8, gbk, gb2312]    # (※2) 字符集选单 
      reset: 1                        # 默认 GBK
      states:                         # 可以通过切功能键，切至 UTF8 等超大字符集
        - UTF-8
        - GBK
        - GB2312

  engine/filters:
      - simplifier
      - uniquifier
      - charset_filter@gbk            # (※3) 默认 GBK 
  translator/dictionary: pinyin_simp  # (※4) 并继续使用 pinyin_simp 字典 
```

它不会冒出太多生僻字候选字，也不会再出现问号方块，「鎔」等繁简转换也会正常显示为「镕」，也不会再有「開、开」同时存在，需要时，还可以随时切换至大字符集（在 pinyin_simp 字典框架内），输入更多罕见字，甚至可以极端至 GB2312 的 6700 多个常用字。

新版[「鼠须管」](http://rime.im)更好玩了。