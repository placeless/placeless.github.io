---
layout: post
title:  "《柯林斯双解》与《朗文5》for macOS"
date:   2019-06-20 21:43 +0800
---

ESL 用户需要[学习词典](https://www.zhihu.com/question/20961302)，这类好词典有很多，比如柯林斯、朗文等，但 macOS 都没有内置，别人转制的质量又一般，于是我打算自己动手，移植两部。其中《柯林斯高阶英汉双解》已经差不多了，适合那些喜欢使用 macOS 词典，又不满足于自带那几本，又心仪柯林斯的水友。优点，应该就是更好看、更好用了，具体细节可以稍微往后翻一翻。另一部是《朗文5》，还在磨蹭中。注意以下事项：

<ul style="list-style-type:none;padding-left:0">
<li style="padding-left:0;">👮‍♀️&nbsp;&nbsp;需自购纸书或 App，以缓解版权不安</li>
<li style="padding-left:0;">🍎&nbsp;&nbsp;macOS Only，Mojave 测试通过</li>
<li style="padding-left:0;">🐈&nbsp;&nbsp;原则上，El Capitan (10.11) 及以上皆可</li>
<li style="padding-left:0;">👉&nbsp;&nbsp;拖拽字典到它旁边的 Dictionaries 目录即可完成安装</li>
<li>🤧&nbsp;&nbsp;发现一个 bug，下载稍等</li>
<!--
<li style="padding-left:0;">🦊&nbsp;&nbsp;自 Firefox Send 下载：<a href="https://send.firefox.com/download/787c9818ed9bd41c/#1NgKsxxssNvRioXLhAsqlw">文字版 ccald_mini.dmg</a>，<a href="https://send.firefox.com/download/149f3f48d1e3c2a1/#v5XMLQ0CUpuE-iNEp-0ZUQ">音图版 ccald_full.dmg</a></li>
<li style="padding-left:0;">🖼&nbsp;&nbsp;或自微软 One Drive 下载：<a href="https://1drv.ms/u/s!Anfwbyaw226qhihc1YH02W6L7e2L?e=I1i84e">点此，二选一</a></li>
-->
</ul>

文字版，~20M

```
SHA256: c764c872be5d7cf2156839e8a136adc786e1570473108a9e616f8b5fcc7128ee
```

音图版，~1.2G

```
SHA256: e5a1e2cfb3a230ea099354dac59e0a5284c08e67323733bbf4170862bf26c224
```




🎃 **TL;DR**

<hr>

![hero image](/files/2019/ccald/hero.jpg)

词典是 macOS 上用了就离不开的功能之一，唯一的遗憾，是内置词典都是简明释义型的，适合临时一瞥，了解个大概意思，不太适合想要深入了解语法和用例的学习者，学习者需要的，应该是既能「瞥」又能「细读」的学习词典（ESL / Learner's Dictionary），而这些都没有内置。

市面上口碑上佳的学习词典不少，我打算移植其中的两本：柯林斯高阶英汉双解学习词典（CCALD）、朗文当代高级词典（LDOCE）。自己移植的原因有二：一是这些词典不面向 macOS 集成，第三方的又但远不如系统词典体验好；二是工具转换的 MDX 等词典，勉勉强强，但内容存在许多错误和瑕疵，样式也没法看，必须要自己动手修正。

移植的目标很朴素，跟原生词典一样简单、好用。在看文章的各位，如果跟在下一样有类似的困扰，那么，这本移植字典应该也是你需要的。

## 一、CCALD

《柯林斯高阶英汉双解学习词典》的特点，网上有已经很多介绍，不再赘述，个人偏好两点，一是释义详细易懂，边解释单词边顺带演示用法；二是例句丰富，翻译得也好。 一言以蔽之，适合「读」。缺点也有，比如「详细易懂」的副作用就是啰嗦；再就是词汇收录量，对比其他学习词典，并不是特别出色。

<figure>
<img src="/files/2019/ccald/ff_win.png" alt="word fulfil">
<figcaption>@fearfare090807 所作 MDX 词典，Win10 + 欧路</figcaption>
</figure>

我使用的蓝本是网上找的，名为 `CollinsCOBUILDOverhaul+V+2-00.mdx` 的 Mdict，原作者应该是 *@fearfare090807*，下文简称 O 版，它内容齐全，有发音，有词性变化，口碑也不错，Mdict、GoldenDict、欧路词典、或是 Windows 用户，可以直接找来体验。上图是 Win10 虚拟机上的原作效果。我的初步目的，是将其移植成 macOS Dictionaries，修正错误，Mac 风格。所做改动，事后归结起来，大致三项：

- 重建索引
- 修补错误
- 新作主题

下文的目的，是尝试给想自己动手水友一点点参考，用到的工具大致罗列如下：

- XML / XSLT / XPath / HTML / CSS / JavaScript
- Python / PyGlossary / Lxml / ElementTree / NetworkX
- Regex / CLI / Xcode / macOS Dictionary Development Kit

### 重建索引

借助 [ilius/pyglossary](https://github.com/ilius/pyglossary) 把 O 版 `.mdx/.mdd` 当中的文本、图片、音频资源提取出来之后，制作 macOS 词典的主要工作就是处理 XML。展开来说，就是处理词头、索引、音标、释义、例句、语法标记、资源链接等等一系列数据的 XML 结构。之后再辅以 XSLT 和 CSS 控制输出内容和样式。

索引决定了你能搜索到哪些词汇及其变体。举个例子，「warm up」做名词时，译作「准备活动；热身活动」，它也可写作「warm-up」，中间多条短横线，类似这种情况很多，你当然希望搜索「warm-up」时也能自动定位到「warm up」，不仅如此，可能还有「warmup」、「warm-ups」等，不一而足。

在边操作边分析的过程中，发现 CCALD 这部词典，重建索引所针对的内容，暂时可以归结为四个部分，下面的名字是我随意起的：

- 链接词
- 引用词
- 短语词
- 形变词

**链接词**，指本身并无义项的词，只有一个链接。O 版是 MDX 字典，通过 `@@@LINK=X` 表示跳转链接，当你搜索词 A 时，MDX 兼容字典应该会自动跳转到 B，但是 macOS，它只是光秃秃显示一个 `A @@@LINK=B` 的映射关系。你可以把它替换成 macOS 字典能识别的链接格式，搜索到这样的词时，再点击链接，跳转至具体的义项词。但是，去除一些测试条目，O 版有效词条 114,953，其中近 7 万条是链接词，足足六成。因此，若做成链接的话，至少会导致两个问题：一是这类词汇占到总体的 60%，太多的链接要点，有点蠢；二是 A 词所链接的 B 词很可能也是链接词，然后 B 词所链接的 C 词可能还是链接词，你没法预料，可能会抓狂。

**引用词**，指释义部分仅包含一个 `→see: x` 引用的词汇。相对于链接词，它可能有音标、发音和词态变形等信息，但没有自己的释义，通过一个或多个 `→see: x` 的链接标记，释义全部指向其他词汇，或是其他词汇的某一条释义。一共 10,184 条，目测，多为单词变形和短语。不包括有义项但同时携带了 `→see: x` 引用的词汇。

**短语词**，类似于引用词，释义部分仅包含一个对 `phrasal verbs: xx` 的引用，光秃秃解释不了任何东西，甚至觉得可能是编辑失误，好在不多，只有 16 条，物书堂 App 的处理方式是把指向的短语也拉到同一界面显示。

**形变词**，指单复数、时态等形变。跟链接词一样，这一条也是多数转制词典的一个顽疾。表现为你能查 `want` 但查不了 `wanted` 这一类的情况，很是烦人。外观次一点，还能忍着用，常见变形查不了，就十分令人难受了。针对所有非链接词提取词性变化，获得 29,566 条记录。由于很多词的变形就是本体，加之部分变形有自己的义项，所以这个部分跟之前的内容会有重复。

再次提醒，上名的名称是我随意起的，可不是正经的学术分类。针对以上四类词汇，根据 macOS Dictionary Development Kit 来看，最好的办法是把它们筛选出来，统统做成对应目标词汇的索引 `<d:index>`，实现自动模糊查找，之后，再酌情删除这几类过渡条目当中的一部分冗余，优化字典体积和查词体验。

最开始只关注了链接词，没注意到其他情况。最直觉的尝试，是直接搜索 `@@@LINK` 标记，提取 Source，Target，然后挨个查找替换。但实际使用时，发现两个问题：一是链接可能很长，不只是 `A -> B` 这么一个简单关系，要用递归或是其他办法去查找终点；二是效率低，很慢，第一版好像跑了快 20 分钟🥵。当然，「慢」指定是跟我的程序水平有关系（鄙人文傻，见谅）。记录了一下细分过程的执行时间，发现耗时重点不在 XML 结点修改，主要还是搜索费时。

在琢磨改变数据结构、空间换时间等问题，尝试优化搜索的过程中，突然想到了一个点：词之间的这种关系，不正是一个有向图的网络结构吗，且借助现成框架来搜索，性能肯定不错，按照这个思路来抽象，似乎好清晰不少：

- 提取链接词（source）关系，获得一个个 tuple `(source, target)`
- 导入 NetworkX，获得一个有向图网络
- 找到没有 `out_dgree` 的顶点，根据顶点找出其所有祖先，获得列表 A
- 建立一个义项词（target）与其 XML Element 的字典，获得字典 B
- 挨个把 A 中 source 的祖先词转为 `<index>` 插入到 B 中对应的义项词内
- 最后清空原 root，把新 Element List `extend` 进去

基于这个想法，速学了一下 NetworkX 文档，在 Jupyterlab 里面作了测试，获得了不错的结果，并有好几个发现：一是数据结构明晰了，除占 60% 的链接词外，还能把引用词、形变词也都纳入管理，处理逻辑很清晰，不再纠结于 XML 元素树；二是效率提升，几秒钟就完成了，快速顶点/祖先查找 + 字典访问避开 ElementTree 遍历 + 新树整体 Append，应该是三大主要助力；三是查错，执行上述过程时，通过程序报错，能发现空指向、错误指向，并趁机纠正这些被忽视的问题。

<figure>
<img src="/files/2019/ccald/fulfil.png" alt="word fulfil">
<figcaption>单词「fulfil」被纠错后的关系图</figcaption>
</figure>

<figure>
<img src="/files/2019/ccald/warm_up.png" alt="word warm-up">
<figcaption>开头举例时提到的「warm up」</figcaption>
</figure>

Warm up 是 CCALD 中最长的网络路径了，下表，我们再八卦一下关系最多的词 Top10。

```
[('-way', 101),
 ('mind', 96),
 ('hand-', 70),
 ('eye', 66),
 ('time', 65),
 ('head', 54),
 ('-word', 52),
 ('life', 45),
 ('take', 43),
 ('heart', 41)]
```

那么，第一名的「-way」到底是何方神圣呢，抽取出来看一看：100 个单词/短语指向了「way」，然后「way」有 1 个 `out_degree` 指向「-way」，画出来如下图。

![way](/files/2019/ccald/way.svg)

如此，词的错综关系，通过有向图 DG = LINKS + REFS + PHRVBS + FORMS -> INDEX 得到了清理，增改删的效率也不再是瓶颈，方便快速迭代。需注意几处：一是这几类词的甄别规则不同，需要单独编写帮助方法；二是可以将引用当中 Hashtag 定位标记当作边（edge）的 attribute 传递进去，输出时，统一处理成可识别的 xpointer，既实现了跳转，还附带了高亮效果。

最后词条和索引的统计结果如下：

![entry counts](/files/2019/ccald/ccald_entries.svg)

O 版 MDX 转换过来，条目和索引是一样的，依据 macOS 字典规格，通过上面的步骤把条目压缩到 4.5 万出头，这里面包括了引用词和短语词，它们虽然和其义项词显示在一个界面上，实际是保留了各自条目的，不算这些，是 3.5 万。索引部分，由于增加了形变词的抽取，增加了 1.1 万余条，对搜索便利性应该是有不少提升的。

### 修补错误

这是一件极其琐碎的事情，基本上是边使用边发现边积累，纠错规则目前得有 100 来条了。大致归类的话，主要是：发音、图片、单词链接；一些 XML 标签和属性；一些内容的层次关系；一些多余内容的清除；以及中英混排间隔、中文标点等等。规则出发点主要是两个：一是纠错、去冗余；二是样式、内容分离。

纠错和去冗余好说，样式和内容分离是指 O 版 XML 内容当中存在很多样式层面的东西，比如，很多 span + class 的标签组合只是为了获得斜体、下划线、或是加粗；填充空格或是其他符号充作间隔；发音喇叭单带图形字体；偶尔出现的行内样式，以及数字序号等等。针对这些装饰性的东西，我觉得应该把它们从内容当中分离出来，让外联 CSS 统一管理即可。

分做了两个步骤来处理：TXT、XML。TXT 是把 MDX 转过来的 XML 内容当作纯文本来处理，查找替换其中的错误，或是增强/自定义部分内容。这里面的规则多数是正则表达式。事后来看，用大量正则表达式去修改 XML，说实话，这个方式有点蠢，有了经验，以后应该不会这么做了。这么说的原因有二：一是 XML/HTML 标签嵌套复杂，写匹配规则很麻烦，也很容易出错；二是极难维护，上下依赖，多了几乎就搞不清了，牵一发而动全身🥵。XML 则是通过 Lxml / ElementTree + XPath 去解析、查找以及调整元素和属性。

如何修复 macOS 字典制作当中的常见错误，网上已经有不少介绍，这里不赘述，这个部分，麻烦一点的是标点符号和间隔的问题。未知是因为 O 版 MDX 字典的文本内容由多方拼凑而成，还是因为原词典就有这个问题，间隔和标点比较混乱，不是大面积的，但是时不时能遇到，不是啥大问题，但着实令人挠头。作了部分修改，有多完美不知道，目前用着还行。

<details>
<summary>中文标点、间隔替换的临时规则如下：</summary>
<div class="highlight">
<pre>
<code>
def translate_marks(cn):
    rules = [
        (r'([\u4e00-\u9fa5]+)([A-Za-z0-9]+)([\u4e00-\u9fa5]+)', '\g<1> \g<2> \g<3>'),
        (r'([A-Za-z0-9%]+)([\u4e00-\u9fa5]+)', '\g<1> \g<2>'),
        (r'([\u4e00-\u9fa5]+)([A-Za-z0-9]+)', '\g<1> \g<2>'),
        (r' ?\( ?', '（'),
        (r' ?\) ?', '）'),
        (r' ?; ?', '；'),
        (r' ?! ?', '！'),
        (r': ?', '：'),
        (r'(\d)(：)(\d)', '\g<1>:\g<3>'),
        (r'"([^"]+)"', '「\g<1>」'),
        ('“', '「'),
        ('”', '」'),
        (r'\?', '？'),
        (r', ?', '，'),
        (r'(\d)(，)(\d)', '\g<1>,\g<3>'),
        (r' ?(\.{3}|…) ?', '……'),
        (r'([a-zA-Z0-9]+)……', '\g<1>\.\.\.'),
        (r'([\u4e00-\u9fa5]+)\.', '\g<1>。'),
    ]
    for rgx, val in rules:
        regex = re.compile(rgx)
        cn = regex.sub(val, cn)
    return cn
</code>
</pre>
</div>
</details>

一些内链调整，name 到 id，para 属性转成 id 锚点，一些 XML 标签的合并挪移等等琐碎问题，就略过了。此外，CCALD 内含各种语法标注标签。比如释义开头的 `[N-VAR]` 等，比如当中的 `[FORMAL]` 等，再比如例句末尾的 `[be VERB-ed + to]` 等等，形式很多。在这个方便，O 版有两个问题：一是标签内容不规范，有标注 V 的，也有标注 VERB 的；二是缺失这些标注的说明。

对于第一个，即标签内容没统一规范并对应至具体义项的问题，目前我还没有着手弄；标注说明，我倒是从购买的[物书堂的 CCALD](https://www.monokakido.jp/en/app/) 英英版 App 中抽取了帮助文件，简单整理之后，放进了 Front/Back Matter 目录中，你可以通过点击 macOS 字典的全局菜单栏、Go、Front/Back Matter 来访问这部分内容，由于取材自英英版，所以目前只是英语说明。

![front back matter](/files/2019/ccald/fbm.png)

最遗憾的是不够水平，没有时间、精力校对词典内容进行严肃一点的比对和校正。

### 新作主题

通过简单的链接修复和文件挪移，O 版的原 CSS 样式也是能工作的，弃之不用的原因有四：一是设计之初它可能是面向欧路、GoldenDict 等词典的主题，在那边可能很不错，但在 macOS 字典里面，反正我是接受不来；二是它有一些 JavaScript 交互设计，比如隐藏/展开等等，这是不被 macOS 字典支持的，对于我个人来说，也没必要；三是 macOS 查词路径多样，三指轻拍 / Force Touch 浮窗、Spotlight 搜索、词典程序本体等，自 Mojave 开始，还有了 Dark Mode，这些都是原样式无法适配的；四是外观这种东西，评价主观的很，我更想做成 macOS 其他内置词典的类似风格，总之，简单就好。

自己要修改的话，猜一猜标签和属性结构之后，也可以通过修改 macOS 字典文件内的 Resources 目录之下的 DefaultStyle.css 来实现自定义。需要注意的地方是，macOS 字典程序会把每个词当作一个 span，再套一层自己的不可见样式，因此，如果不幸发现光标 hover 时，有内容跳动，应该就是跟它的样式冲突了。

下面是几张截图，排版、字体、颜色等，基本上是向 macOS 其他内置字典看齐的，只对于一些它们没有的，CCALD 的特色内容作了一些发挥，比如扩充语法表格、引用区块等等。另外，对于 Lookup 浮窗、Spotlight 搜索栏、字典程序本身、以及 Dark Mode，都作了简单的适配。

有两点注意：一是黑暗模式，在 info.plist 内加入一个 `DCSDictionaryUseSystemAppearance` 布尔键，设值为 `true` 即可获得 macOS Dark Mode 的基本样式支持，然后在 CSS 样式文件内，结合 `@media (prefers-dark-interface)` 针对单个元素做个性化修饰；二是查词浮窗的范围选择，以 `html.apple_client-panel` 开头就行了，比如 `html.apple_client-panel > .word_entry`。

<figure>
<img src="/files/2019/ccald/dark_mode_dict.jpg" alt="screenshot: dictionary app">
<figcaption>macOS 词典程序</figcaption>
</figure>

<figure>
<img src="/files/2019/ccald/popup.jpg" alt="screenshot: force touch popup">
<figcaption>三指轻拍 / Force Touch 浮窗</figcaption>
</figure>

<figure>
<img src="/files/2019/ccald/spotlight.jpg" alt="screenshot: spotlight  window">
<figcaption>Spotlight 搜索框</figcaption>
</figure>


此外，借助 CSS / JavaScript / HTML 配合 XSLT，作了几个简单的自定义显示功能，一是为方便万一不想下载图片和发音的水友，用来关闭图片和喇叭的显示。因为整理之后的词典文本很小，但是发音+图片有 1GB 还多。二是为了方便偏好繁体中文的水友。繁简转换是通过 CSS 的 `font-variant-east-asian` 接口来实现的，不知道其兼容效果如何，至少 macOS Mojave 10.14.5 是没问题的。最后请注意，调整开关之后，可能需要重启词典程序或者相关 Lookup 进程才能生效。

- 中文简/繁选择
- 图片、注释、发音喇叭、扩充语法、词性说明文字的显示与否

以下截图，是这些功能开关与其影响的外观元素的对应。另外，在偏好设定的 About 部分，放了本文链接，方便将来查找更新；留了报 Bug 邮箱；您要是感觉不错，最近又阔，还可以尝试点击那个 Feel Good；若是碍眼，则可进入字典 bundle 文件，修改删除 prefs_html 里面对应的这部分内容。

<figure>
<img src="/files/2019/ccald/note1.jpg" alt="chinese, note, and picture">
<figcaption>Chines（繁 / 简）；Note（注释、引用等区域）；Picture（图片显示与否）</figcaption>
</figure>

<figure>
<img src="/files/2019/ccald/note2.jpg" alt="pronunciation speaker, word form labels">
<figcaption>Speaker（发音图标）；Word Form Label（单复数、时态等说明文字）</figcaption>
</figure>

<figure>
<img src="/files/2019/ccald/note3.jpg" alt="table: thesaurus, usage">
<figcaption>Grammar Table（同近义词、用法等表格）</figcaption>
</figure>

为了缩减字典体积，编译时兼容参数设定在 10.11，也就是 macOS El Capitan 及以上。这里唯有一点担心：部分 CSS 接口，不知道在旧机型上是否正确运行。因为调试环境一直都是 10.14.5，即 macOS Mojave 最新版，暂时没机会测试旧版系统上的兼容性。

<details>
<summary>CCALD 完整改动列表：</summary>
<ul>
<li>😝 先偷个懒</li>
</ul>
</details>

<hr>

## 二、LDOCE

江湖人称「一本就够」

[ 💻 WIP... ]


<hr>

最后放一段英文，方便大家测试。抱歉，记不起是从哪里复制过来的了。Good luck, have fun.

> Police have used violent tactics, tear gas, baton charges, pepper spray, to push crowd back onto Harcourt Road after they took Tim Wa Avenue. Mood here defiant and angry. Tear gas clouds visible either side of LegCo.

中文，大概是这个星球上为数不多的，使用人数在日益增长，本身却在日益萎缩的语言了。社交媒体越来越热闹，能用的数字、字词却越来越少；讲中文的人口虽越来越多，能直说的话却越来越少；自我审查的表单越来越长，因言获批似乎却越来越容易。仿佛「噤声」就是这个语境的终极目的，而「碍眼」就是你被问罪的一切根源。What a wonderful world.

<figure>
<img src="/files/2019/ccald/the_great_passage_hand.png" alt="screenshot from movie: the great passage, hand">
<figcaption>《编舟记》截图</figcaption>
</figure>

**EOF**

<details>
<summary>感觉不错：</summary>
<figure class="feel_good">
<img src="/files/avatar/i_am_rich.png" />
<figcaption>Just for fun, 丰俭由君。</figcaption>
</figure>
</details>


