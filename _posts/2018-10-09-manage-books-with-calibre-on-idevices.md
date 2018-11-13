---
layout: post
title:  苹果平台上的书籍管理
date:   2018-10-09 15:01 +0800
redirect_from:
  - 2018/10/09/manage-books-with-calibre-on-idevices
---

书籍来源，无非三处，iBooks Store、Amazon、网络搜索。iBooks Store 国内已经关张，不谈。剩下的，粗略一分，大部分来自亚马逊，问题跟通过网络搜索得来的书一样，格式和排版我不喜欢，阅读体验差。

我的需求是这样的：我想通过 iBooks 阅读所有的书籍；能转换格式，排版能便捷调整。

为什么呢？两个原因：一是为跨平台，iBooks 在 Mac、iPad、iPhone 上同步方便（实际使用当中，有不少小问题），而 Kindle Mac App 的质量，那是简直了；一是很多书排版不佳，iBooks 支持的 ePub 格式，自己修改方便，想怎么来怎么来，也不麻烦，而 Kindle 的导入导出就远不如 iBooks 结合 iCloud Drive 的便捷了。

![calibre_library](/files/2018/calibre_library.png)

需要解决的问题，也有两个：一是去 [DRM](https://en.m.wikipedia.org/wiki/Digital_rights_management)，不然亚马逊购买的书籍无法再变格式再编辑；一是这样做合不合规，看了一些理由，结论是「管它呢」。

所用的工具有两个，Kindle Mac App 和 [Calibre](https://calibre-ebook.com/)，外加一个 Calibre 插件 [DeDRM](https://github.com/apprenticeharper/DeDRM_tools)。Kindle Mac App 用于把购买的书同步到本地，Calibre 用于编辑工作，编辑完了，直接拖拽到 iBooks 里。步骤和先后次序如下：

先安装 DeDRM 插件，切到 Calibre 偏好设置，在插件管理界面选择 Load plugin from file，选择 DeDRM_calibre_plugin 目录下的 DeDRM_plugin.zip 即可，重启 Calibre。

Kindle 的书籍位置，打开偏好设置，即可看到 Content Folder，一般在如下目录。

```
~/Library/Container/com.amazon.Kindle/Data/Library/Application Support/Kindle/My Kindle Content/
```

把其中的 .azw 或是 .mobi 拖到 Calibre 之内即可，插件会自动完成去 DRM 的过程。再往后，转换格式 ePub，修复错误，调整排版样式，随意编辑。

这里面有一些工作，可能是每本书都要做的，比如去除段首空格、调整行距、段距、边距、文字颜色、甚至重做目录等等，都可以在 Common Option 里面预先定义。这样，每次进行格式转换时，Calibre 就帮我们自动处理了这些重复工作。大多数时候，也无需再调。

至于微调细节、参数的具体解释和操作，就不赘述了，基本是一些 CSS 和正则表达式的内容。

![calibre_epub_common_options](/files/2018/calibre_epub_common_options.png)

Calibre 是个复杂庞大的书籍管理工具，具备很多功能，比如的编辑工具的 Reports 功能，这本书有哪些文件构成，内容词频如何，有多少个图表等等，一目了然。

这一套下来，需求是满足了，但也有一些问题。比如亚马逊的书籍更新功能，电子书好像一个软件，是可以通过发布更新，持续增补内容、修复错误的，我把它挪出了 Kindle 平台，也就失了这条链路。再比如 iCloud 的同步的可靠性，也比较玄乎，时灵时不灵，叫人捉摸不定。总之，「自由度」暂时战胜了上面种种的问题，让这个选择得以坚持。