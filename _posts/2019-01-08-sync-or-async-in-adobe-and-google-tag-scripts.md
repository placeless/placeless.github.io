---
layout: post
title:  "Adobe/GA Tag 的同异步问题"
date:   2019-01-08 16:22 +0800
---

朋友问个问题：网页埋码中 Sequential 和 Non-sequential 的区别。

这是 Adobe <a href="https://marketing.adobe.com/resources/help/en_US/dtm/" title="Dynamic Tag Management">DTM</a> 的术语，我的理解，这个问题跟 JavaScript 在 HTML 中的位置以及它加载的方式相关。位置，指的是放在 `<head>` 还是 `<body>` 标签内的地方；加载方式，指的是同步（无标记）、异步（async）、还是推迟（defer）。

概念比较多，要搞清楚上面的问题，还得从两个方面入手：

- HTML 页面是怎么呈现的
- JavaScript 是怎么影响页面呈现的

## HTML Document

一个网页包含了很多东西，有文本段落、表格表单等元素，也有图片、视频、CSS 以及 JavaScript 等资源，一大堆的东西层层叠叠，构成了一张网页，浏览器才网络上接收到这些信息之后，会逐步将其解析成为一个独特的数据结构，叫做 DOM。

<figure>
<img src="https://upload.wikimedia.org/wikipedia/commons/5/5a/DOM-model.svg" alt="DOM Model">
<figcaption>DOM 模型（<a title="Birger Eriksson [CC BY-SA 3.0 (https://creativecommons.org/licenses/by-sa/3.0)], from Wikimedia Commons" href="https://commons.wikimedia.org/wiki/File:DOM-model.svg">来源：Wikipedia</a>）</figcaption>
</figure>

组成网页的这些东西，格式各异，大小不一，加载的也有快有慢，浏览器内部有非常复杂的机制来管理它们各自的状态。总体上，一张页面是否就绪，浏览器会通过一个接口来告诉我们，这就是 `document.readState`。大致的过程是这样的：

- document.readyState == "loading"，即将开始解析
- document.readyState == "interactive"，DOM 就绪
- document.readyState == "complete"，全部内容完成，包括大图片等

这一过程，上一篇文章有比较详细的描述：[测量网页的加载时间](http://localhost:4000/blog/measure-navigation-timing#article)



[Mariko Kosaka](https://developers.google.com/web/resources/contributors/kosamari) 于 2018 年 9 月在 Google Developer 上发布了一系列 4 篇文章，简明解释了 Google Chrome 的内部工作原理。其中的第三篇主要解释 Renderer Process，即网页的呈现过程：

- Parsing 解析
- Style calculation 样式计算
- Layout 布局
- Paint 绘制
- Compositing 合成

我们这里只关注解析阶段，它包含 DOM 构建与子资源加载，平常所说的 JS 阻塞解析的问题就发生在这里。说到阻塞，有 Parser Blocking 与 Render Blocking，

## 网页中的 JavaScript



Parser blocking vs. render blocking

https://stackoverflow.com/questions/37759321/parser-blocking-vs-render-blocking

Imagine an HTML page has two `<script src="...">` elements. The parser sees the first one. It has to stop* parsing while it fetches and then executes the javascript, because it might contain `document.write()` method calls that fundamentally change how the subsequent markup is to be parsed. Fetching resources over the internet is comparatively much slower than the other things the browser does, so it sits waiting with nothing to do. Eventually the JS arrive, executes and the parser can move on. It then sees the second `<script src="...">` tag and has to go through the whole process of waiting for the resource to load again. It's a sequential process, and that's parser blocking.

CSS resources are different. When the parser sees a stylesheet to load, it issues the request to the server, and moves on. If there are other resources to load, these can all be fetched in parallel (subject to some HTTP restrictions). But only when the CSS resources are loaded and ready can the page be painted on the screen. That's render blocking, and because the fetches are in parallel, it's a less serious slow down.



------

\* Parser blocking is not quite as simple as that in some modern browsers. They have some ability to tentatively parse the following HTML in the hope that the script, when it loads and executes, doesn't do anything to mess up the subsequent parsing, or if it does, that the same resources are still required to be loaded. But they can still have to back out the work if the script does something awkward.

网页呈现的步骤

svg 动画



https://stackoverflow.com/questions/34289535/why-first-paint-is-happening-before-domcontentloaded

DOMContentLoaded means that the parser has completed converting the HTML into DOM nodes and executed any synchronous scripts.

That does not preclude the browser from rendering an incomplete DOM/CSSOM tree. In fact it has to be able to perform reflows anyway in case javascript queries computed CSS properties. And if it can do reflows on incomplete trees it may as well render them.

This is also relevant for large documents streamed from a server. The user can already start reading it before it has completed loading.

It is important to understand that the whole parsing / evaluation / rendering process is a stream processing pipeline with some parts even done in parallel / speculatively. The later stages of the pipeline do not wait for the earlier stages to finish, instead they take the outputs as they arrive and process them as soon as enough information is available to do the next increment.

E.g. the parser obviously cannot emit Element nodes before processing all of its attributes, but it can emit the node while still processing its child tree. And the renderer may render the node without its children. And it may be rendered with incomplete styles only to undergo a reflow later, e.g. when javascript inserts another style sheet or simply because the child nodes which have yet to be inserted affect how it will be rendered.

<http://www.html5rocks.com/en/tutorials/internals/howbrowserswork/#The_main_flow>

> It's important to understand that this is a gradual process. For better user experience, the rendering engine will try to display contents on the screen as soon as possible. It will not wait until all HTML is parsed before starting to build and layout the render tree. Parts of the content will be parsed and displayed, while the process continues with the rest of the contents that keeps coming from the network.

一般来说，行为采集工具的部署脚本包含两个部分：一是初始化一些设置，比如 `DataLayer`；一是创造条件，便于加载外部完整的 JavaScript 库。

```html
<script>(function(w,d,s,l,i){w[l]=w[l]||[];w[l].push({'gtm.start':
new Date().getTime(),event:'gtm.js'});var f=d.getElementsByTagName(s)[0],
j=d.createElement(s),dl=l!='dataLayer'?'&l='+l:'';j.async=true;j.src=
'https://www.googletagmanager.com/gtm.js?id='+i+dl;f.parentNode.insertBefore(j,f);
})(window,document,'script','dataLayer','GTM-XXXX');</script>
```

以上面的 <a href="https://developers.google.com/tag-manager/quickstart" title="Google Tag Manager">GTM</a> 代码片段为例，这是一个立即执行函数，它执行之时，网页的主要部位 `<body>` 都还没影（官方文档建议将其安插在 `<head>` 内）。

render-blocking

![Screen Shot 2019-01-08 at 21.23.07](/Users/jed/Desktop/Screen Shot 2019-01-08 at 21.23.07.png)

<figure>
<img src="/files/2019/asyncdefer.svg" alt="Async and Defer">
<figcaption>JavaScript Async & Defer</figcaption>
</figure>

整个 DOM 的构建，如上图，自上而下，一行接一行。至于数据采集的 JS 放在什么位置，Head 还是 Body，要不要自己做一些改写等问题，还需结合后文来分析。

## DOM 遇到 JavaScript

当一个 `<script>` 元素开始执行时，所有

```html
<!-- "https://eager.io/blog/everything-I-know-about-the-script-tag/" -->

<html>
  <head>
    <script>
      // document.head is available
      // document.body is not!
    </script>
  </head>
  <body>
    <script>
      // document.head is available
      // document.body is available
    </script>
  </body>
</html>
```



## 文档加载状态

一张网页从服务器下载到用户本地，有一个时间过程，在这个过程中，文档（document）的会有不同的状态，比如还在下载、这些好了那些还没好等，`readyState` 就是用来描述状态变化的一个属性，它可取以下三值之一：

- `loading`：页面还在加载
- `interactive`：页面加载完成，但页面内的图片、样式、JS 等还没有加载完成
- `complete`：页面和内嵌的子资源都加载完了

说到「加载」，似乎有一些笼统，实际 DOM（Document Object Model）树的生成由两个部分组成

## 同步、异步及推迟

埋点时，同步、异步提的比较多，通常是夹杂在「异步不会影响网站速度」之类的笼统语境中。为什么说笼统，是因为实际上同或异的概念不重要，重要的是采集工具加载的 JS 对 DOM 解析有什么具体影响，或者说是阻塞还是非阻塞的。那么同异、阻塞到底意味着什么，网页是如何解析生成的，我们的 JS 又会不会影响网页的解析呢，这个问题，Mozilla Hacks 上的这一篇文章阐述的非常详细了：[Building the DOM faster: speculative parsing, async, defer and preload](https://hacks.mozilla.org/2017/09/building-the-dom-faster-speculative-parsing-async-defer-and-preload/ "这篇文章详细解释了 DOM Building 的过程和 JS、CSS 在其中的影响，以及优化策略的演变")

<figure>
<img src="https://hacks.mozilla.org/files/2017/09/blocking-bold@2x-1.png" alt="Blocking Model">
<figcaption><a title="这篇文章详细解释了 DOM Building 的过程和 JS、CSS 在其中的影响，以及优化策略的演变" href="https://hacks.mozilla.org/2017/09/building-the-dom-faster-speculative-parsing-async-defer-and-preload/">图片来源：Mozilla Hacks</a></figcaption>
</figure>


简单来讲，无论是 JS 还是 CSS，都会影响 DOM 的解析，因为 JavaScript 可能通过 `document.createElement()` 或 `document.write()` 操作 DOM（比如添加一个段落，查询一个节点），所以 DOM 构建时，遇到 JS 会马上停下来，等待运行结果（如果是外调 JS，要先等待下载，再执行），不停下来结果无法预料。同样，尽管 CSS 不会修改 DOM，但如果 DOM 不停下来等待 CSS 的修饰结果，一旦遇到 JS 查询 DOM 的 CSS 样式信息，DOM 就不知道该怎么办。所以，页面解析到 JS 和 CSS 时，都会中断，等它们的加载、执行之后 DOM 再继续往下构建。

为了优化这一过程，人们引入了更灵活的配置策略：

| JS 策略     | DOM 构建                                |
| ----------- | --------------------------------------- |
| sync，同步  | 构建中断，等待 JS 加载并执行            |
| async，异步 | JS 加载时 DOM 构建不中断，JS 执行时中断 |
| defer，推迟 | 不中断，DOM 构建完成之后，JS 再执行     |

`sync`（同步）是老方案，它的意思是，一旦遇到 JS，则立即停下来等它加载执行；`async`（异步），只是加载不再阻塞 DOM 构建，一旦 JS 加载完成，则立即进入执行阶段，如果此时 DOM 构建还未完成，DOM 会停下来等 JS 执行；`defer` 则是在异步的基础上，将 JS 推迟到 DOM 构建完成之后才开始执行。

```html
<script src=""></script>
<script async src=""></script>
<script defer src=""></script>
```

## 窗口加载完成

上面说到文档（document）加载完成时，

DOMContentLoaded, window.onload, state complete

对应的，我们就可以根据需求，调整埋点策略。文档加载完成

<figure>
<img src="https://marketing.adobe.com/resources/help/en_US/dtm/graphics/DTMLoadOrder.png" alt="DTM Load Order in relation to JavaScript Load Order">
<figcaption><a title="这篇文档详解了 DTM JS 的加载是如何与 HTML DOM 对应的" href="https://marketing.adobe.com/resources/help/en_US/dtm/load_order.html">图片来源：Adobe DTM: Load Order for Rules</a></figcaption>
</figure>
## DTM Sequential & Non-sequential

Search Ads 案例

## Reference

- [HTML 5.2 Specification](https://www.w3.org/TR/html52/ "🤦‍♀️ W3C HTML 5.2 规范文档")
- [浏览器的工作原理](https://www.html5rocks.com/zh/tutorials/internals/howbrowserswork/ "👏 以色列 [Tali Garsiel](http://taligarsiel.com/) 的研究，详解了 Firefox、Chrome 等浏览器是怎么工作的")
- [Page lifecycle: DOMContentLoaded, load, beforeunload, unload](https://javascript.info/onload-ondomcontentloaded "页面的生命周期：DOM 完备、页面完备、离开页面")
- [EFFICIENTLY LOAD JAVASCRIPT WITH DEFER AND ASYNC](https://flaviocopes.com/javascript-async-defer/)
- [DTM: Load Order for Rules](https://marketing.adobe.com/resources/help/en_US/dtm/load_order.html) 
- [MDN: Document.readyState](https://developer.mozilla.org/en-US/docs/Web/API/Document/readyState)
- [Adding Third Party Scripts with Dynamic Tag Manager](https://www.cardinalpath.com/adding-third-party-scripts-with-dynamic-tag-manager/)
- [Building the DOM faster: speculative parsing, async, defer and preload](https://hacks.mozilla.org/2017/09/building-the-dom-faster-speculative-parsing-async-defer-and-preload/)
- [Everything I Know About The Script Tag](https://eager.io/blog/everything-I-know-about-the-script-tag/)
- [domInteractive: is it? really?](https://www.stevesouders.com/blog/2015/08/07/dominteractive-is-it-really/)
