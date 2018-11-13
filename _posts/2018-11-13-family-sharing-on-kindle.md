---
layout: post
title:  "家庭共享 Kindle 书籍"
date:   2018-11-13 14:53 +0800
redirect_from:
  - 2018/11/13/family-sharing-on-kindle
---

苹果的 App Store 有一个非常贴心的功能，叫作 Family Sharing（家庭共享），它使得购买了 macOS/iOS App 的用户无需再花一份钱，使得 App 可以直接共享给家庭成员使用，很人性，很数字，也很互联网。这一功能同样支持苹果的 iBooks 商店，只不过，这个书店，它如今跟 Google、Twitter 一样，在我们这不存在。

可惜，家庭共享，亚马逊中国，至今没有。

因此，如果你在亚马逊上买了一本电子书，好看，想把它分享给家人，一般来说，这是行不通的。

为什么说「行不通」，又为什么说「一般来说」呢？

说「行不通」，是因为亚马逊的电子书，基本都有 DRM 保护，刚从 Kindle A 拷贝到 Kindle B，没翻两页，它就会报错，并弹出未购买请删除之类的胡言乱语。

说「一般来说」，是因为「有没有 DRM」以及「能不能移除」，跟这本电子书的格式有关。`.epub` 格式亚马逊不支持，暂且不谈，以下，打算梳理一下 Kindle 平台上常见的 `.mobi` 以及 `.azw`。

## .mobi

出自一家名为 Mobipocket 的法国公司，2000 年就有了。期间，由于 Adobe 宣布不再出售电子书打包软件，于是 Amazon 打算自食其力，在 2005 年买下了 Mobipocket。

它有两种后缀形式：`.prc` 和 `.mobi`。现如今，据说多数 `.mobi` 格式的书籍都是 DRM free 的，也就是说，可以便捷分享，随意看。

## .azw

它是 `.mobi` 的后继，包括 `.azw`，`.azw3`，`.kfx` 等，其中 KFX 是 Kindle Paperwhite 3 之后引入的新格式，X 代表相应的数字，比如`.azw3` 也就是 `.kf8`。我自己的经验，国内亚马逊上买的书籍，大多是 `.azw3` 格式，它是不能直接分享的，必须去 DRM，这正是之前提到的 [calibre 结合插件 DeDRM](/blog/manage-books-with-calibre-on-idevices#article) 一文干的活，在此不再赘述。

## Family Sharing

除了跨账号作手工折腾，我目前在用的共享方法，是家人共用一个亚马逊账号，只不过这样的话，购买记录、阅读记录、笔记统统混淆到一起了，用时，有些不方便，但不失为一个较为省事的好办法，再说，还便于交流。屋里安装了 Kindle 阅读的设备，大概是 Kindle x2，iPad x2，Mac x2，iPhone x2，都是一个账号，随时随地，其实也没啥问题。