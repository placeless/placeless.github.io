---
layout: post
title:  "简单了解熊猫 TV 的观众体量"
date:   2017-10-02 22:40 +0800
redirect_from:
  - 2017/10/02/panda-tv-audience-counts
---

我看直播。经常在熊猫看 Dota 1 和 2 的比赛、对黑，今天想了解一下大概的观众人数。仅简单统计了直播列表的第一页，数据来自网页上显示的观众数值，不求探讨「系数」是多少。

晚 7:30 时看了一眼，第一页观众共计 873 万多，最热主播有近 106 万观众。到晚 10:30 左右，再次看了一下，观众居然更多了，到达 1307 万，如下图，最热主播还是之前排在榜首的「错觉老中医」，播的节目是「吃鸡」……🐔……

Winner winner, chicken dinner!

![panda tv audience counts](/files/2017/10/02/panda.tv.png)

简单梳理一下脚本：

数据获取。`$`、`$$`、`$x` 等符号，在主流浏览器里都可以直接调用，分别代表 jQuery、CSS、XPath 选择器，酌情选择趁手的一种即可。不同的选择器对后续数据整理的方式可能稍有影响，但结果都是一样的。

数据整理。上图使用的 `$` 方式，所以直接调用了 jQuery `.text()` 方法并通过 `.split('人')` 将字符串拆成数组，再利用 `.pop()` 方法刨掉末尾「人」字多产生的元素，得到一个观众列表 `["1172", "6.9万", "6.7万", "6.2万", "5.9万"...]`。如果不想用 jQuery，`$$` CSS 选择器也是类似道理，`$$('.video-number').map(function(x){return x.innerText})`，即利用 `map` 先取得 innerText。最后，通过 `map` 方法，对每个元素进行字符转数字，带「万」的乘以 10000，不带则保持不变。

数据汇总。最后通过 `reduce` 方法对数组内的所有数字进行累加，得到想要的总观众人数。

```
var n = $('.video-number').text().split('人');
n.pop();
n = n.map(function(x) {
  if (x.includes('万')) {
    return parseFloat(x.replace(/万/i, '')) * 10000
  } else {
    return parseFloat(x)
  }
});
var r = n.reduce(function(a, c) { return a + c });
r;
```

不过，似乎下面这一种，逻辑稍微清晰一点：

```
var n = $$('.video-number')
  .map(function(x) { return x.innerText })
  .map(function(x) {
    if (x.includes('万人')) {
      return parseFloat(x.replace(/万人/i, '')) * 10000
    } else {
      return parseFloat(x.replace(/人/i, ''))
    }
  });
var r = n.reduce(function(a, c) { return a + c });
r;
```

斗鱼：

```
var n = $$('.dy-num').map(function(x){return x.innerText}).map(function(x){if(x.includes('万')){return parseFloat(x.replace(/万/, '')*10000)}else{return parseFloat(x)}});var r = n.reduce(function(a, c){return a + c});r;
```

虎牙：

```
var n = $$('.js-num').map(function(x){return x.innerText}).map(function(x){if(x.includes('万')){return parseFloat(x.replace(/万/, '')*10000)}else{return parseFloat(x)}});var r = n.reduce(function(a, c){return a + c});r;
```

