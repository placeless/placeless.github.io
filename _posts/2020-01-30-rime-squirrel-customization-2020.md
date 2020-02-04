---
layout: post
title:  "鼠须管配置 2020"
date:   2020-01-30 02:33 +0800
---

有一段时间没有使用鼠须管了，由于自带输入法的小问题，今天又把它翻了出来，只在[鼠须管 2019](/blog/rime-squirrel-customization-2019)的基础上，做了几点微调。

## 鼠须管新版的获取

Release 渠道的版本更新，通常很慢，自己拉仓库下来编译又很麻烦，其实呢，一些新的 Commit 被提交之后，Bintray 上通常都有对应的编译文件可以下载体验的，比如新近提交的「使用 CapsLock 切换输入法」的支持。求新的话，可以从[这里下载](https://dl.bintray.com/rime/squirrel/)。注意它不是一个按新旧排列的有序列表，需要自己根据 Commit 编号去找对应的文件。

## 禁用英文模式

macOS 自带的 ABC 输入法是不能移除的，与其在 ABC、鼠须管中、鼠须管英三者之间切个头晕，不如禁用鼠须管的西文模式，实现使用自带中/英时的同样效果：按下快捷键，西文切到系统 ABC，中文切到鼠须管。

改动很简单，鼠须管 GitHub 仓库的 Wiki 就有[说明](https://github.com/rime/squirrel/wiki/禁用-Squirrel-英文模式，使用左侧-Shift-切换中英)，只是两处：一、将 `ascii_composer` 从输入方案的 `processors` 当中注释掉；解除 `app_options` 设定，以免固定西文模式的名单继续作用，导致这些程序内无法输入中文。

为了方便 VIM 和 HHKB，我把 CapsLock 改为了 Ctrl，输入法切换键是 Ctrl + Space 组合，删除了所有的默认西文设定（感觉不需要了），最近外置 4K 使用多，候选字号、间距略作调整，如此而已。

## 更换词库

从定制的 pinyin_simp 字词库换回到了鼠须管出厂配置的 [luna_pinyin](https://github.com/rime/rime-luna-pinyin)，原因无它，还是因为感觉 pinyin_simp 太弱了，虽然一番折腾，剪裁了很多生僻字，仅保留了 8,000 多个常用字，更小巧轻量，但回头再看再对比，luna_pinyin 词库的个人输入体验好多了。之前积累的自造词稍作调整，完全可以继续用，pinyin_simp.userdb.txt 改名为 luna_pinyin.userdb.txt，再打开，同样编辑一下头部的 @db_name，就可以走鼠须管同步功能，扩充自造词了。

## 生僻字的问题

之前选择定制 pinyin_simp 词库的原因之一，是解决生僻字豆腐块的问题，换到 luna_pinyin 词库也不想再折腾了，还是走安装字体的老路，思源宋体搭配[花园明朝](http://fonts.jp/hanazono/)，也算相得益彰。


如感兴趣，还是这个地址：[placeless](https://github.com/placeless)/[squirrel_config](https://github.com/placeless/squirrel_config)
