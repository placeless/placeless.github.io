---
layout: post
title:  "将 GA 当作静态网站的数据库用"
redirect_from:
  - 2014/01/05/using-google-analytics-as-static-site's-database
---

> update: 此功能目前已移除

为文章添加新浪微博分享功能时，顺便加了一个 <i class="fa fa-thumbs-up"></i> 功能，以免孤零零一个微博图标在下面不好看。但是 Jekyll 生成的全是静态页面，没有数据库支持，怎么才能统计到点击数呢? 嗯，可以利用 Google Analytics Event Tracking，但是会有几个小问题：

* 用户虽有点击，却得不到结果反馈，体验完全没有 (不妨试点一下 <a class="fa fa-thumbs-up"></a> )
* 而且容易导致用户乱点猛点
* 虽然可以用 JavaScript 设置点击侦听事件，但是又不能保证 GA 已经统计到了点击数据

### Google Analytics Hit Callback

如果你已经升级到了 Google (Universal) Analytics，上面的问题就好说了，只需要了解一个东西

* [Hit Callback]：在 GA 监测请求已经成功触发之后，你再做某个动作

相信用过 HTTPFOX 的同学，肯定会有这种经历：多条 GA 监测代码过于接近的时候，有些请求会显示为红色，类似下图的样子，你以为有记录了，其实它反馈的是数据统计失败了:

![GA-Error](/files/2014/01/05/ga-error.png)

这种情况常见于点击某个按钮，跳转到同窗口的下一个页面时，这是由于跳转的发生的太快，按钮的点击监测代码没有足够的时间触发。有了 Hit Callback 功能之后，我们就可以在按钮监测请求已经成功发起之后，再做跳转，这样就不会造成数据丢失了。同样的，如果应用到我们题头所述问题上，我们则可以实现:

* 点赞的 GA 监测代码，成功触发之后，改变页面结构，让小手不可点，并且反馈给用户：已赞
* 万一 GA 由于网络或者 Adblock 等原因没有加载，则弹出错误提示

### 实例

Hit Callback 用法很简单，只需在 pageview 或者 event 原有参数之后，加上 `'hitCallback': function() {}` 即可，function 内部，我们则可以做任何我们想做的事情：

```js
// 设置按钮点击侦听事件
// 这里图简单，没有考虑 addEventListener 的浏览器兼容
// 老旧的 IE 浏览器可能有问题
var likeBtn = document.getElementById('like');
likeBtn.addEventListener('click',function() {

  // 如果 GA 的 analytics.js 正常加载了，我们才使用 hitCallback 功能
  // 不做判断的话，万一 GA 被屏蔽，没加载，我们好做另一手准备
  if (typeof(ga.getAll) == 'function') {

    // 开始 GA Event 监测
    ga('send', 'event', {
      'eventCategory': 'engagement',
      'eventAction': 'like',
      'eventLabel': document.title,

      // 把点赞次数放到 event value 里面, 当数据库用
      'eventValue': 1,

      // 重点开始了, 注意 hitCallback 的形式和用法
      'hitCallback': function () {

        // 点完赞, GA event 请求也成功触发之后
        // 让按钮变得不可点击, 并且告诉用户结果: 已赞
        // <i class.../i> 是在调用 font-awesome 字体, 就是那只小手
        likeBtn.innerHTML = '<i class="fa fa-thumbs-up"></i> 已赞';
      }
    });
  } else {
    // 如果 GA 的 analytics.js 没有正常加载, 则反馈: 出错了
    likeBtn.innerHTML = '<i class="fa fa-thumbs-up"></i> 出错了';
  };
});
```

有提交，有存储，有反馈，挺像那么回事是不是。Hit Callback 可玩性还是非常高的，举一反三，很多地方都用得到，以往遇到的不少问题，也可以通过它来解决。

* 干干净净，没有注释的代码请看这个 [Gist]
* 想看活生生的例子? 赶快拿起鼠标，点击文章下面的小手吧。


[Hit Callback]: https://developers.google.com/analytics/devguides/collection/analyticsjs/advanced#hitCallback
[Gist]: https://gist.github.com/placeless/8269417
