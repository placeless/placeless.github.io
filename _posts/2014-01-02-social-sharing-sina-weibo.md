---
layout: post
title:  "为文章添加新浪微博分享功能"
tags:
  - API
  - 笔记
---

时至今日, 新浪微博的[开放接口]已经数不胜数, 我这里暂时只是应 [@cloga] 要求, 为网站文章添加一个分享功能, 免去用户复制粘贴, 自己写文案的痛苦.

新浪微博官方提供的文档简单易用, 三步即可, 但是我不太喜欢.

```html
<!-- 1.在HTML标签中增加XML命名空间 -->
<html xmlns:wb=“http://open.weibo.com/wb”>

<!-- 2.在HEAD头中引入WB.JS -->
<script src="http://tjs.sjs.sinajs.cn/open/api/js/wb.js" type="text/javascript" charset="utf-8"></script>

<!-- 3.在需要部署微博发布器的位置粘贴WBML代码 -->
<wb:share-button addition="simple" type="button"></wb:share-button>
```

如上, 首先我的 jekyll 模板完全是基于 HTML5 的, 用的极简语法, 连 `<html>`, `<body>` 等标签都省去了, 让我突然再加一个 XML 命名空间, 实在是接受不能; 其次是我网站目的简单, 不想再多加第三方的 js 组件; 第三, 也非常重要, 新浪官方提供的图标实在是太难看, 放进来突兀的很.

### 怎么办呢

依稀记得以前参与的项目当中, 各单位的分享功能, 都是开发的同事们自己改编的, 可以找出来参考. 再看了看 ifanr, zhihu, jianshu, 等网站的源码, 得到一个简单的思路, 效果则如这个网站所示, 源码解析如下, 算是学习笔记:

#### 第一步先看看别人怎么做的
我们先拆解一条分享出去的文章链接 (微博分享弹窗地址栏内的内容)

```text
http://service.weibo.com/share/share.php
?appkey=1881139527
&title=I%20recommend%20%22%E4%B8%80%E6%88%B0%E8%A8%98%E6%86%B6%EF%BC%9A%E6%86%82%E6%80%9D%E5%9B%9E%E9%A6%96%22%20by%20%40Laughing_Jacky.%20via%20%40%E7%AE%80%E4%B9%A6.
&url=http%3A%2F%2Fjianshu.io%2Fp%2Fdebfe134613c
&searchPic=false&style=simple
&pic=http://upload.jianshu.io/notes/images/75803/weibo/image_bb1e545990ad.jpeg
```

可以得出以下几个字串:

* 微博分享接口: http://service.weibo.com/share/share.php
* appkey: 1881139527
* title: I%20recommend%20%22%E4%B8%80%...
* url: http%3A%2F%2Fjianshu.io%2Fp%2Fdebfe134613c
* searchPic: false
* style: simple
* pic: http://upload.jianshu.io/no...545990ad.jpeg

这样一拆解, 就有比较明了的印象了, 我们也只需要依葫芦画瓢, 构造出来这么一个东西, 当用户每次点击分享按钮时, 能触发此动作就好.

#### 第二步自己尝试重现整个过程
接口 URL 可以不用管, 哪里都一样; appkey 去新浪[申请]即可, 想要知道分享相关的数据, 最好还是自己申请一个. 我们的重点, 是后面几个参数, 一个一个来:

* title 可以通过 `document.title` 获得
* url 可以通过 `document.location.href` 获得
* searchPic 和 style, 看了不少例子, 我们不妨直接保留 `searchPic=false&style=simple`
* pic 稍微冷僻一点, 我们可以自己通过 `document.images[].src` 抓取页面内的图片地址, 比如 `document.images[0].src` 就抓取第一幅图片的源地址, 作为微博分享图片

需要特别注意的是, 不要忘记做一个: 文章是否有图片的判断, 以决定分享带不带图片, 不然代码容易出错.

#### 最终的结果
因为我这套模板的思路是: 语义相关的东西放 HTML 里; 样式放 CSS 内; 分享点赞什么的都算是额外的功能, 打算都放到 JS 里面, 便于区隔维护. JS 按各种小功能, 分成单独的小 js 文档, 编译的时候, 再合并到同一 all.js 之内, css 文件亦如此. 因此不便用 HTML 结构内添加 a 标签, 触发 onclick 事件这种方式.

好了, 以下是我的方案:

```js
var weiboBtn = document.getElementById('weibo');
weiboBtn.addEventListener('click', function() {
  var appkey = '988857141';
  var title  = document.title;
  var url    = document.location.href + '?utm_source=sinaWeibo&utm_medium=sharingBtn&utm_campaign=blog';
  var style  = '&searchPic=false&style=simple';
  var pic;

  if (!document.images[0]) {
    pic = style;
  } else {
    pic = style + '&pic=' + document.images[0].src;
  }

  ga('send', 'event', 'engagement', 'share', document.title);
  window.open('http://service.weibo.com/share/share.php?appkey='+appkey+'&title='+encodeURIComponent(title)+'&url='+encodeURIComponent(url)+pic,'_blank','toolbar=0,status=0,resizable=1,scrollbars=yes,status=1,width=440,height=430,left='+(screen.width-440)/2+',top='+(screen.height-430)/2);
});
```

文章底部, 还加了个*赞*功能, 大家有兴趣自己研究吧, 下次再讲基于 Github 的静态网页, 怎么利用 GA 做简单的计数统计.

> 注意: 以上代码, 并没有考虑老旧浏览器兼容!

[开放接口]: http://open.weibo.com/
[@cloga]: http://weibo.com/cloga
[申请]: http://open.weibo.com/
