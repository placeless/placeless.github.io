---
layout: post
title:  "SPA 埋点的两个问题"
date:   2017-10-13 20:59 +0800
redirect_from:
  - 2017/10/13/track-single-page-application-with-google-tag-manager
---

这几天，朋友打算上一个创业项目，我帮忙做一点数据采集的事情。遇到两个问题，一难一易，孰难孰易，一时到不易辨别。

这是一个 Single Page Application，前端是 Vue，后端是 PHP，整体接在微信公众号上。且先不提他的业务，以及我预想的数据运营框架，单说数据采集的实现过程。

我参与之时，已是即将上线之际。虽有 Slack 做总体的项目及沟通管理，奈何进入太晚，不宜兴师动众，贸然影响他人的既定节奏。所以，初期的想法是先请前端部署 Google Tag Manager Snippets，我独立远程思考、创作、埋点。

## 问题一：Single Page Application Tracking

第一个问题是如何把 Google Analytics 应用到 SPA 的数据采集上。

SPA 内容看起来页面流转不停，其实全是单页内的组件更替，并没有整页刷新。这个例子里面，产品列表、用户注册、订单管理等组件，都是 Vue 框架在操作 `#app` 这个容器，进行内容更迭。

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <!-- meta -->
  
  <title>placeless</title>
  <!-- Google Tag Manager -->
  <script>(function(w,d,s,l,i){w[l]=w[l]||[];w[l].push({'gtm.start':
  new Date().getTime(),event:'gtm.js'});var f=d.getElementsByTagName(s)[0],
  j=d.createElement(s),dl=l!='dataLayer'?'&l='+l:'';j.async=true;j.src=
  'https://www.googletagmanager.com/gtm.js?id='+i+dl;f.parentNode.insertBefore(j,f);
  })(window,document,'script','dataLayer','GTM-PLACELESS');</script>
  <!-- End Google Tag Manager -->
  
</head>
<body>
  <!-- Google Tag Manager (noscript) -->
  <noscript><iframe src="https://www.googletagmanager.com/ns.html?id=GTM-PLACELESS"
  height="0" width="0" style="display:none;visibility:hidden"></iframe></noscript>
  <!-- End Google Tag Manager (noscript) -->
  
  <div id="app"></div>
  
  <!-- scripts -->
</body>
</html>
```

### 方案：History Change vs MutationObserver

把前端仓库 `git clone` 回来，勉勉强强梳理了一下基本的路由逻辑，把本地环境跑了起来。斟酌之后，觉得，确实还是不要麻烦前端人员为好，我可以尝试通过 Google Tag Manager 的 History Change 事件来实现路由变更的跟踪。

经测试，确实可行。但有两个问题：一是 dataLayer 数量急剧增长，担心影响客户端性能；二是 History Change 发生之后，DOM 变更需要时间，响应的慢，往往是 GA 采集代码先执行了，导致抓不到正确的 Page Title，即不能正确采集到 Page Path 与 Page Title 的组合。这时候，要么设置 Timeout，要么设置一个别的 GTM Event 去触发 GA。前者既不优雅，又没法确定多长的 Timeout 最合适；后者的话，就不如照此思路，舍弃 History Change，另起一个新办法了。

这个新办法就是 MutationObserver。

| Feature       | Android | Chrome for Android                       | Edge  | Firefox Mobile (Gecko) | IE Phone | Opera Mobile | Safari Mobile                            |
| ------------- | ------- | ---------------------------------------- | ----- | ---------------------- | -------- | ------------ | ---------------------------------------- |
| Basic support | 4.4     | 18 [-webkit](https://developer.mozilla.org/en-US/docs/Web/Guide/Prefixes)    26 | (Yes) | 14.0 (14)              | 11 (8.1) | 15           | 6 [-webkit](https://developer.mozilla.org/en-US/docs/Web/Guide/Prefixes)    7 |

<figcaption>Mobile 端 MutationObserver() 支持情况</figcaption>

采用 MutationObserver API 解决了上面两个问题，但微信与非微信环境的 DOM 不太一样，而且页面上还有不少动态元素，需要一些琢磨时间，去判断 Observe 什么样的 Mutation 最合适。还好，与第二个问题相比，都是解决有望的小问题。

```html
<!-- custom html tag -->
<script>
  (function () {
    // 尝试控制 dataLayer 长度
    window.dataLayer = window.dataLayer || []
    if (dataLayer.length > 50) { dataLayer.splice(5, dataLayer.length - 5) }

    // MutationObserver
    var target = document.querySelector('#app.wx') || document.querySelector('#app header') ;
    if (target) {
      var observer = new MutationObserver(function(mutations) {
        dataLayer.push({
          'custom_page_title': document.title.trim(),
          'custom_page_path': document.location.hash.substr(1),
          'event': 'header_changed'
        });
      });
      var config = {
        attributes: true,
        childList: true,
        characterData: true
      };
      observer.observe(target, config);
    }
  })();
</script>
```



## 问题二：一九八四

没想到过这会是一个问题：gtm.js 请求阻塞了页面😱。

通过 Charles，限速、拦截、抓包，问题得到确定。我大清国情如此，又恰逢大会临近，虽然 gtm.js 是异步加载，不阻塞其他资源。但其他资源加载完毕之后，终归也还是要等它的。注意下图微信顶部的进度条👇。

![gtm loading](/files/2017/10/13/loading.jpg){:height="74px" width="250px"}

它对应的是下图，模拟 gtm.js 资源受限之时，其 HTTP request 的请求情况👆👇。

![gtm failed](/files/2017/10/13/gtm_failed.png)

即便不在微信环境里，也是一样的👇。

![gtm loading 2](/files/2017/10/13/loading2.jpg){:height="112px" width="250px"}

![gtm failed 2](/files/2017/10/13/gtm_failed2.png)

GTM 依托于 Google 强大的全球网络，本该是地球上最可靠的服务之一，不巧，我们这里却已是新话世界，乡愿社会，似无它的容身之所。由于朋友的业务特殊，服务的实时性和一致性要求很强，所以我们暂时做了如下的处理。

![detele ga](/files/2017/10/13/delete_ga.png){:height="50%" width="50%"}

接下来，我还会继续尝试其他的办法，比如 Server 端的 GA 实现，Measurement Protocol，以及其他的连接服务，比如 Segment。不为别的。

![Rick.and.Morty.S02E06](/files/2017/10/13/Rick.and.Morty.S02E06.jpg)