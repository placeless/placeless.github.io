---
layout: post
title:  "uBlock Origin 的拦截模式"
date:   2017-08-15 18:22 +0800
redirect_from:
  - 2017/08/15/blocking-mode-in-ublock-origin
---

uBlock Origin 有 5 个拦截模式：

- Very easy
- Easy
- Medium
- Hard
- Nightmare

前两个模式同其他 AdBlock 工具差不多，按照订阅的 3rd-party 过滤列单进行广告过滤，比如 EasyList、EasyPrivacy 等；最后一个噩梦模式，网页几乎没法看；所以，我的选择只在 Medium 和 Hard 之间作出。

## Medium 和 Hard 的区别

Medium 和 Hard 两个模式的唯一区别，只是 `3rd-party` 这一项勾选与否。

- [ ] 3rd-party
- [x] 3rd-party scripts
- [x] 3rd-party frames

与 `3rd-party` 相关的有 3 项，scripts 跟 frames 被单独拿出来，其他的比如 css、iamges 等则包括在第一项未指明的 `3rd-party` 内。也就是说，Medium 和 Hard 的区别，关键在于**是否默认拦截掉所有第三方请求**。

Medium 放过除 js、frame 之外的其他请求，让第三方的图片、CSS 等得以加载，尽可能维持网页完整显示；而 Hard 拦截所有的第三方请求，网页残缺可能性更高，这时就需要用户的干预，手动进行允许和拦截，比如 CDN 缓存、用户从各处插入的图片等，uBO 会将这些手动规则记录下来，这是 Dynamic Filtering。

Medium 的问题在于：闸口开的稍宽，虽对网页的破坏性较 Hard 模式稍好，但同时却容易漏过广告、数据监测、以及其他的一些幕后动作，往往需要额外引入一些 3rd-party filters 来捕捉漏网之鱼，比如 EasyList、EasyPrivacy、Malware Domians、Ad and tracking server list 等；此外，很多页面将 js 资源放在其他域下，Medium 模式并不能让这些页面圆满展示。

Hard 的问题在于：闸口开的很小，网页高概率破碎，基本离不开人工干预，需要琐碎的一关一关去放行，逐渐积累自己的黑白名单。

小结：两个模式都离不开人工干预（dynamic filtering）。因为，或多或少，直接拦截了当前站点的第三方资源引用，一遇到资源分流或是 UGC 站点，页面内容总会有所缺失，需要人工干预一次。好处是如果挑的足够细致，会是一个完全自定义的高效规则，毫无冗余。

所以不如直接上 Hard 模式。

但人工干预的层面只有 hostname 和 request type 两块，粒度较粗，所以这两个模式的官方文档都推荐订阅 3rd-party filters 来做最后一道防护。

## Dynamic filtering & Static filtering

订阅规则的好处是：巨细无遗，覆盖广，且灵活。Hostname、URL 等形式都支持；拦截、隐藏皆可配置；通配符、正则写法也随便用。

它的问题也很明显：源自于它的好处，成千上万的规则里面，既有你需要的，也有你不需要的（可能更多），影响匹配效率；装饰规则（cosmetic filters）需要操作 DOM，性能负担重。

直接订阅的第三方规则，以及自定义的符合 ABP 语法的 My filters，在 uBO 内，都被称作为 static filtering。

与之对应，uBO 内置的机制还有 dynamic filtering，即 advanced user 模式下的人工干预，包含 dynamic filtering rules 和 dynamic URL filtering rules。两者的[区别](https://github.com/gorhill/uBlock/wiki/Overview-of-uBlock's-network-filtering-engine:-details)是：前者应用范围广，对应的是整个网站或者整个请求资源类别；而后者只是匹配特定的 URL。

uBO 的拦截过程如下（引用自[官方文档](https://github.com/gorhill/uBlock/wiki/Overview-of-uBlock's-network-filtering-engine)）：

<img src="https://camo.githubusercontent.com/ed1f37e099febf7f9002c557b7bb679704f78095/68747470733a2f2f63646e2e7261776769742e636f6d2f676f7268696c6c2f75426c6f636b2f323834623466363264313761306333656266333637356130613637626533363339366661616633652f646f632f696d672f66696c746572696e672d6f766572766965772d706c61696e2e737667" width="50%">

关键：

- 订阅规则（static filtering，my filters 也算一种订阅）的判定优先级是最低的
- 非 block 资源，如 action 为 allow，进入绿色通道，直达 remote server
- 非 block 资源，如 action 为 noop，才往下进入下一个判定环节

也就是说，自定义 dynamic filters 时，除非笃定，否则不要轻易设定为 allow， 因为 allow 会导致后续的关卡完全失效，noop 是一个更保险的选项。noop 虽会多经一步 static filtering，但如果订阅规则精简，相信查询效率不会成为瓶颈。

期望：

- 拦截：干净、高效
- 规则：精炼、不冗余

倾向于：自己写一些必要的 ABP 规则，作为 static filtering 的补充，但不订阅推荐的哪些臃肿列表。

## 组合与取舍

结合访问习惯，再过一遍。内容可分两类：

- 经常访问的网站，不多：Twitter、Youtube、一些文档站点等
- 缘分站点：Tweet 内提及的链接、Google 的搜索结果等

应对拦截造成的页面破坏，如果是常访站点，手工挑一次动态规则，也许再补充一点 ABP 规则，工作量也不是太大。而缘分站点，也可以分两类：常见资源被拦截，比如 jQuery、在线字体、Youtube嵌入视频等，应对如上；个体的特殊情况，比如各式子域等，只能临时放开闸门。

那么，这时候的问题还是：需不需要为了这个情况，订阅推荐的几万条第三方规则，用作最后的关口？

如果，即便几万条 static filters，对性能和资源占用的影响也不大，那么加上也没关系，无非是多些冗余。但是，影响大不大，我还不知道。

## 人工干预的好处

Hard 模式造成的网页破碎，并非全无好处。在手工放行和拦截时，目睹一张网页背后关联的各式服务，好奇心驱使，发现了解了更多东西。

嗯，为了拦截泛滥的网络广告和恶意内容，反而认识到更多的用户服务形式和商业产品，这是人工干预的好处，是乐此不疲的动力。