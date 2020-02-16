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

基于 luna_pinyin.dict 重新定制单字和词库。之前搞的 pinyin_simp 字词库，还是太业余，打字表现羸弱，这次换回到鼠须管出厂配置的 [luna_pinyin](https://github.com/rime/rime-luna-pinyin)，还是结合汉字标准规范，使用 8105 个常用单字，繁简一起，跟 luna_pinyin.dict 对比，剪裁掉这个规范之外的所有生僻字词，由原来的 70670 行，砍去一大半，留下来 33518 个字词条目，依然小巧轻量，输入体验却比之前好多了。luna_pinyin.dict.yaml，跟其他补丁文件一起放到 ~/Library/Rime/ 目录下即可。

之后，再手工修改一下 opencc 的 TSCharacter 词典，删去那些从没见人用过的简体字，比如「侭、㓰、𢭏、𧹒、𫔭、𫔮」等，重新生成 TSCharacter.ocd，替换掉 /Library/Input Methods/Squirrel/Contents/SharedSupport/opencc/ 目录下的同名文件，以此回避精简单字之后依然冒出的罕见字。

之前积累的自造词，同步出来，稍作调整，将 pinyin_simp.userdb.txt 改名为 luna_pinyin.userdb.txt，再编辑一下头部的 @db_name，就可以走鼠须管同步功能，无缝扩充自造词到词库里面。

## 生僻字的问题

之前选择定制 pinyin_simp 词库的原因之一，是为了解决生僻字豆腐块、字符集过滤、emoji 等几个内容的冲突，让它们能同时存在。现在换到 luna_pinyin 词库，做了上面的一番工作之后，依然有效，不用担心字符集过滤导致 emoji 不能全部显示，也不用担心为了 emoji 去忍受过多的生僻字。


如感兴趣，还是这个地址：[placeless](https://github.com/placeless)/[squirrel_config](https://github.com/placeless/squirrel_config)，注意两点：这个配置，删除了大量不常用字，请当心使用；默认关闭了 emoji 选项，需要自行修改配置文件，解除相关部分的注释，开启 emoji。
