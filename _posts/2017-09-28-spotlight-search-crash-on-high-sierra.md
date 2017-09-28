---
layout: post
title:  "High Sierra Spotlight 输入闪退"
date:   2017-09-28 14:04 +0800
---

升级 macOS 到 High Sierra 之后，发现输入任何东西都会直接导致 Spotlight Search 崩溃，浮窗一闪而过，不能正常使用。

几番尝试之后，猜想可能跟我在 `~/Library/Dictionaries` 目录下新增了自定义的词典有关，也许是 High Sierra 不再兼容这些外来词典。

在 Dictionary.app 的偏好设置内**取消这些词典的勾选**之后，果然，Spotlight Search 正常工作了。

所以，如遇上述问题，可以尝试一下上述办法。