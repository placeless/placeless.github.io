---
layout: post
title:  "Atom 打开长行文件失去响应的解决方案"
date:   2016-07-03 14:15 +0800
---

> 这是一个一知半解的尝试。

Atom 编辑器在我的 MacBook Pro with Retina Display 上存在一个问题：打开一些压缩过的大串 JS，或者粘贴很长很大一行文本，稍做一些鼠标选择等操作，Atom 就会失去响应一段时间，甚至崩溃。

比如我想简单了解一下 GrowingIO 的 JavaScript SDK，浏览器打开 [http://dn-growing.qbox.me/vds.js](http://dn-growing.qbox.me/vds.js)，复制粘贴至 Atom，鼠标稍微在字符上动一动，Atom 就挂起了，直至程序崩溃，而放进 Sublime Text 就不会出现这样的问题。

会不会是两者的配置差异呢？

两个编辑器的字体都是 [Fira Code](https://github.com/tonsky/FiraCode)，但是 Atom 支持 programming ligatures，所以我在其 styles.less 内开启了`text-rendering: optimizeLegibility;`，于是我尝试将 optimizeLegibility 改为 auto，发现挂起问题消失了……

有没有办法既使用 optimizeLegibility，保留 programming ligatures，又解决挂起问题呢？答案是：有。

因为我发现，通过 Sublime Text 对上文压缩过的 vds.js 文件做 JavaScript Beautify，然后保存，再通过 Atom 打开编辑，是没有明显的挂起问题的，似乎是跟某些个 buffer 有关。所以尝试在 Atom Settings 内又勾选了`Soft Wrap`，做软换行，让 Atom 自动把大段的长行文件展开，惊奇发现：问题解决了，既不失去漂亮的 programming ligatures，又没有性能问题，两全其美……

总结：Atom 开启了`text-rendering: optimizeLegibility;`之后，如遇「长行文件失去响应」问题，可以再开启`Soft Wrap`。