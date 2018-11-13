---
layout: post
title:  "High Sierra Spotlight 输入闪退"
date:   2017-09-28 14:04 +0800
redirect_from:
  - 2017/09/28/spotlight-search-crash-on-high-sierra
---

升级 macOS 到 High Sierra 之后，发现输入任何东西都会直接导致 Spotlight Search 崩溃，浮窗一闪而过，不能正常使用。

几番尝试之后，猜想可能跟我在 `~/Library/Dictionaries` 目录下新增了自定义的词典有关，也许是 High Sierra 不再兼容这些外来词典。

在 Dictionary.app 的偏好设置内**取消这些词典的勾选**之后，果然，Spotlight Search 正常工作了。

所以，如遇上述问题，可以尝试一下上述办法。

增补：

我在 V2EX 的[这个帖子](https://www.v2ex.com/t/394270)上提供了上述建议，从回帖来看，结果得到验证，问题都源自「自增字典」。