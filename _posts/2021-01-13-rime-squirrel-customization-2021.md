---
layout: post
title:  "鼠须管配置 2021"
date:   2021-01-13 01:43 +0800
---

今年的主题是放飞自我，做更多尝试，实现更高效的输入，获得量身定做般的成就感。

<img src="/files/2021/squirrel_config_2021.png" alt="squirrel_screenshot_2021" width="50%">

先尝试了[小鹤双拼的形码方案](https://bbs.flypy.com/forum.php?mod=viewthread&tid=184)，发现并不适合自己。鹤形，在拼音的基础上，引入了类似五笔的拆字规则，以期通过双拼加形码来解决重码问题，这就要求我们需要熟记几乎所有单字和词组的音、形编码，输入过程拼音、五笔双重袭脑，相互角力，堪比一心二用，对于习惯于读音思考的我来说，起点实在有些高。玩起来很有意思，只是坚持不下去。

但是，这个过程加深了我对 Rime 输入法的理解，也启发了我实现一些定制点子的思路，再借助网上各位的分享和贡献，汇聚成以下的：鼠须管配置 2021。

## 主要特点

- 小鹤双拼
- 8105 简体字
- 长句模型
- 连贯输入
- 个性选词

## 基本套路

主要依赖项：

- 输入法平台：[鼠须管测试版](https://dl.bintray.com/rime/squirrel/)
- 双拼方案：[Rime 小鹤双拼](https://github.com/rime/rime-double-pinyin/blob/master/double_pinyin_flypy.schema.yaml)
- 汉字字符集：[通用规范汉字](https://github.com/mozillazg/pinyin-data)
- 基础词库1：[袖珍简化字拼音](https://github.com/rime/rime-pinyin-simp)
- 基础词库2：[简化字八股文](https://github.com/rime/rime-essay-simp)
- 配置管理：[东风破](https://github.com/rime/plum)
- 长句模型：[八股文语言模型](https://github.com/lotem/rime-octagram-data)

Python 生态，做前期数据处理，大致路径如下：

- 克隆下载方案和字词库
- 清理出规范汉字列表
- 与简化字八股文匹配，删除界外字词
- 与袖珍简化字匹配，删除界外字词
- 进入东风破，装配语言模型
- 成品参考：[placeless](https://github.com/placeless)/**[squirrel_config](https://github.com/placeless/squirrel_config)**

## 方案详解

placeless_flypy.schema.yaml

这个输入方案的基本前提：**一是仅用鼠须管输入中文**，ascii 或者说西文，直接 Ctrl + Space 切到 macOS 的默认 ABC；**二是尽量以长句为输入单元**，不打断整句的表达节奏。

```yaml
# Rime schema
# encoding: utf-8

schema:
  schema_id: placeless_flypy
  name: 小鹤双拼
  version: "0.1"
  author:
    - double pinyin layout by 鶴
    - Rime schema by 佛振 <chen.sst@gmail.com>
    - Mod by placeless
  description: |
    小鹤双拼自定义方案。

# 基本不用切换，因为不用扩展字符集，也不用 emoji
switches: 
  - name: full_shape
    reset: 0
    states: [ 半角, 全角 ]
  - name: ascii_punct
    states: [ 。，, ．， ]

# 只有简化字中文，所以
# 删掉了 ascii_composer
# 删掉了 ascii_segmentor
# 删掉了 filters
# selector 提前，方便;次选及'一选
engine:
  processors:
    - recognizer
    - key_binder
    - speller
    - selector
    - punctuator
    - navigator
    - express_editor
  segmentors:
    - matcher
    - abc_segmentor
    - punct_segmentor
    - fallback_segmentor
  translators:
    - punct_translator
    - script_translator

# 增加了 :./-=_+ 作为输入码
# 便于在打字中，不中断地输入 3.14、2:00-3:00 等
speller:
  alphabet: 'zyxwvutsrqponmlkjihgfedcba0987654321:./-=_+'
  algebra:
    - erase/^xx$/
    - derive/^([jqxy])u$/$1v/
    - derive/^([aoe])([ioun])$/$1$1$2/
    - xform/^([aoe])(ng)?$/$1$1$2/
    - xform/iu$/Q/
    - xform/(.)ei$/$1W/
    - xform/uan$/R/
    - xform/[uv]e$/T/
    - xform/un$/Y/
    - xform/^sh/U/
    - xform/^ch/I/
    - xform/^zh/V/
    - xform/uo$/O/
    - xform/ie$/P/
    - xform/i?ong$/S/
    - xform/ing$|uai$/K/
    - xform/(.)ai$/$1D/
    - xform/(.)en$/$1F/
    - xform/(.)eng$/$1G/
    - xform/[iu]ang$/L/
    - xform/(.)ang$/$1H/
    - xform/ian$/M/
    - xform/(.)an$/$1J/
    - xform/(.)ou$/$1Z/
    - xform/[iu]a$/X/
    - xform/iao$/N/
    - xform/(.)ao$/$1C/
    - xform/ui$/V/
    - xform/in$/B/
    - xlit/QWRTYUIOPSDFGHJKLZXCVBNM/qwrtyuiopsdfghjklzxcvbnm/

# 当前只有八股文简化字和袖珍简化字词库
# 其它词库，在 extended 中增加引用
# 繁体词库编译错误，需要修改前面的 switcher 和 filters
# 增加八股文简化字语言模型，优化长句输入
translator:
  dictionary: extended
  prism: placeless_flypy
  contextual_suggestions: true
  max_homophones: 7

grammar:
  language: zh-hans-t-essay-bgw

# 无前缀输入简单的预制表情和键盘符号
# 小鹤双拼没有 bq 和 kb 的编码
punctuator:
  import_preset: default
  symbols:
    "bq": [😂️, 😅️, 🎉, 🐂, 😱️, 👌, 😇️, 🙃️, 🤔️, 💊️, 💯️, 👍️, 🙈️, 💩️, 😈️ ]
    "kb": [⌘, ⌥, ⇧, ⌃, ⎋, ⇪, , ⌫, ⌦, ↩︎, ⏎, ↑, ↓, ←, →, ↖, ↘, ⇟, ⇞]
  half_shape:
    "\\" : "、"
    "#" : "#"
    "@" : "@"
    "~": "~"
    "/": "/"
    "'": {pair: ["「", "」"]}
    "[": "["
    "]": "]"
    "{": "{"
    "}": "}"
    "*": "*"
    "%": "%"
    "<" : ["<", "《"]
    ">" : [">", "》"]

recognizer:
  import_preset: default
  patterns:
    punct: "^(bq|kb)$"

# 💊️，只提供两个候选项
# 分号次选，空格或引号一选
# 首尾衔接，轮回翻页
menu:
  page_size: 2
  alternative_select_keys: "';"
  page_down_cycle: true

# Tab、Shift+Tab 上下翻页
# Control+p、Control+n 上下翻页
# Control+k = Esc，清除所有输入
key_binder:
  bindings:
    - { when: composing, accept: Tab,        send: Page_Down }
    - { when: composing, accept: Shift+Tab,  send: Page_Up }
    - { when: composing, accept: Control+k,  send: Escape }
    - { when: composing, accept: Control+p,  send: Page_Up }
    - { when: composing, accept: Control+n,  send: Page_Down }
```

### 字词库

extended.dict.yaml 

八股文简化字 + 袖珍简化字，个人暂时够用。如外挂其它网络词库，遇到繁简混合，可能会编译错误，需要在上边的方案里，加入对应的 switcher 和 filters 调用 opencc 进行繁简处理。

<img src="/files/2021/rime_octagram_1.png" alt="squirrel_octagram_screenshot_1" width="50%">
<img src="/files/2021/rime_octagram_2.png" alt="squirrel_octagram_screenshot_2" width="70%">

```yaml
# Rime dictionary
# encoding: utf-8

---
name: extended
version: "2020.12.30"
sort: by_weight  # original, by_weight
vocabulary: essay-zh-hans
use_preset_vocabulary: true

import_tables:
  - pinyin_simp
...
```

pinyin_simp.dict.yaml 

为了在长句中文中顺畅输入数字和常用符号，加入了一些土方。其中的..是双击句号输入全角句号。

```yaml
# Rime dictionary
# encoding: utf-8
#

---
name: pinyin_simp
version: "0.2"
sort: by_weight
...

# 个人土方
.	.	2
。	..	1
:	:	2
：	::		1
/	/	1
-	-	2
_	_	1
=	=	2
+	+	1
0	0	1
1	1	1
2	2	1
3	3	1
4	4	1
5	5	1
6	6	1
7	7	1
8	8	1
9	9	1
# zh_simp 精简 -> 8105
```



### 方案选单 

default.custom.yaml 

```yaml
patch:
  schema_list:
    - schema: placeless_flypy
  switcher/hotkeys:
    - "Control+grave"
```

### 微调主题

squirrel.custom.yaml

```yaml
patch:
  # 通知栏显示方式以及 ascii_mode 应用，与外观无关
  show_notifications_via_notification_center: true

  # 以下软件默认英文模式
  app_options: {}

  style:
    color_scheme: apathy
    horizontal: true # deprecated

  preset_color_schemes:
    apathy:
      author: "LIANG Hai"
      border_height: 6
      border_width: 4
      candidate_list_layout: linear # stacked | linear
      text_orientation: horizontal
      inline_preedit: true
      candidate_format: "%c\u2005%@\u2005"
      comment_text_color: 0x999999
      corner_radius: 5
      font_face: PingFangSC
      font_point: 17
      label_font_point: 1
      back_color: 0xFFFFFF
      text_color: 0x424242
      label_color: 0xFFFFFF
      hilited_candidate_back_color: 0xFFF0E4
      hilited_candidate_text_color: 0xEE6E00
      hilited_candidate_label_color: 0xFFF0E4
      # alpha: 0.95
      name: "冷漠／Apathy"
```

### 其它配件

剪裁过的字词库和语言模型文件。

```
- essay-zh-hans.txt
- pinyin_simp.dict.yaml
- zh-hans-t-essay-bgw.gram
```

### 成品参考

[placeless](https://github.com/placeless)/**[squirrel_config](https://github.com/placeless/squirrel_config)**

