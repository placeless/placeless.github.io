---
layout: post
title:  "Sublime Text Adaptive Theme 定制"
date:   2017-05-22 22:22 +0800
---

2018-05-07 更新：

Sublime Text 发 3.1 了，再玩，发现「定制」主题不用这么麻烦，[官方文档](https://www.sublimetext.com/docs/3/themes.html#customization)写的很详细。比如，想要给 Adaptive 主题打个补丁，实现标题栏与编辑区域同色，获得前文的 Frameless 体验，只需在 `~/Library/Application Support/Sublime Text 3/Packages/User` 目录下新建 Adaptive 主题的同名文件：`Adaptive.sublime-theme`，然后加入相关配置即可（代码范例如下），相当方便，无需其他插件。具体哪些配置项可调，可参考官方的主题文档，也可从 `/Applications/Sublime Text.app/Contents/MacOS/Packages` 目录下，拷贝主题文件 `Theme - Default.sublime-package`，更改后缀为 zip，解压后即可查阅、参考 JSON 格式原主题配置。

比如修改黑色标题栏为 Mariana Color Scheme 背景色：

```json
[
    {
        "class": "title_bar",
        "fg": ["background", 255, 255, 255, 0.7],
        "bg": ["background", 52, 61, 70, 0.4]
    }
]
```



旧文：

----

一直都羡慕 Atom、VS Code 编辑器更「现代的」的界面，稍作调整，即可实现自 macOS Yosemite 引入的 Frameless 窗口，整个观感非常的一体，很有 Google Chrome 浏览器那个简洁美。

![atom editor](http://github-atom-io-herokuapp-com.global.ssl.fastly.net/assets/screenshot-main@2x-f5f56d18fa8896b3d987d24fc903d03f.png)



虽然定制灵活，但 Atom、VS Code 都基于 Eletron，在性能和内存消耗上，目前还比不上 Sublime Text，因此在日常文本编辑器上的取舍上，一直有点纠结。

最近看变更日志，发现 Sublime Text 3 自 [Dev 3127](https://www.sublimetext.com/3dev) 开始，新增了一个叫做 Adaptive 的界面主题，它不同于以往的地方在于：主题配色不再是固定的，而是会根据 Color Scheme 的不同，动态呈现出不同的窗口（包括标题栏、边栏、浮层等）颜色，非常的有趣。

![default theme of sublime text 3](/files/2017/05/22/default.png)

<figcaption>这是 Sublime Text 3 的默认主题</figcaption>

![adaptive theme of sublime text 3](/files/2017/05/22/adaptive.png)

<figcaption>上图是 Adaptive Theme 效果</figcaption>

通过 Shift + CMD + P 然后输入 Select Theme 可以应用 Adaptive 主题，上图的标题栏就是根据代码高亮的 base16-materia 这个 Color Scheme 自适应出来的。较之上上图的默认标题栏，整体效果上已经有改善，但是还是有一点割裂感，我自己是希望主题色与代码背景色完全一致。

做了一点尝试，发现这个想法完全是可以实现的，便于修改 Adaptive 主题文件，需要用到一个叫做 [PackageResourceViewer](https://packagecontrol.io/packages/PackageResourceViewer) 的插件。它的作用就是解开、查看、编辑 Sublime Text 插件里面的各种文件。我一般用它来小幅修改一些 Color Scheme 和一些 AutoCompletion Snippet，比如删除自带 JavaScript 补全脚本后面的分号。

定位至 Shift + CMD + P → PackageResourceViewer: Open Resource → Theme Default → adaptive → Adaptive.sublime-theme，根据个人喜好的 Color Scheme，酌情修改 Adaptive.sublime-theme 文件内的 Title Bar、Side Bar、Panel、Views 等部件的各项属性即可。Sublime Text 的主题部件文档，可参考 [https://www.sublimetext.com/docs/3/themes.html](https://www.sublimetext.com/docs/3/themes.html)，以下是我搭配 [base16-materia](https://chriskempson.github.io/base16/) 高亮配色的修改结果。

![customized adaptive](/files/2017/05/22/customized_adaptive.png)

<figcaption>Adaptive Theme 配置文件预览，Color Scheme 为 base16-materia，Tab Bar & Status Bar 均已习惯性隐藏。</figcaption>

另附 User Preferences Settings，仅供参考，切勿照搬。

```json

{
	"auto_complete_commit_on_tab": true,
	"auto_complete_cycle": true,
	"auto_complete_triggers":
	[
		{
			"characters": ".",
			"selector": "source.python"
		}
	],
	"auto_complete_with_fields": true,
	"binary_file_patterns":
	[
		"*.jpg",
		"*.jpeg",
		"*.png",
		"*.gif",
		"*.ttf",
		"*.tga",
		"*.dds",
		"*.ico",
		"*.eot",
		"*.pdf",
		"*.swf",
		"*.jar",
		"*.zip"
	],
	"caret_style": "phase",
	"color_scheme": "Packages/Base16 Color Schemes/Themes/base16-materia.tmTheme",
	"draw_shadows": false,
	"enable_tab_scrolling": false,
	"folder_exclude_patterns":
	[
		".svn",
		".git",
		".hg",
		"CVS",
		"node_modules",
		".sass-cache",
		"_site"
	],
	"font_face": "SF Mono",
	"font_options":
	[
		"gray_antialias"
	],
	"font_size": 12.2,
	"highlight_line": true,
	"hot_exit": false,
	"ignored_packages":
	[
		"Vintage"
	],
	"line_padding_bottom": 2,
	"line_padding_top": 2,
	"show_encoding": true,
	"theme": "Adaptive.sublime-theme",
	"theme_font_options":
	[
		"gray_antialias"
	],
	"translate_tabs_to_spaces": true,
	"trim_trailing_white_space_on_save": true
}

```

