---
layout: post
title:  "GA SDK 的本地部署"
date:   2017-11-24 14:44 +0800
redirect_from:
  - 2017/11/24/deploy-google-analytics-sdk-on-local-server
---
上回说到 GTM 遭到拦截，导致网页加载和数据采集遇到困难。我们提到过一种思路，即 [采用 MP 来规避](/blog/across-the-firewall-with-measurement-protocol.html#article)。其中有一个遗留问题，即：自己实现数据采集方案，通过 MP 管道传递数据至 GA，只适用于少量维度，多了，实现复杂度成指数上升。所以一旦遇到复杂的行为数据采集需求，还是希望借助 GA SDK 来实现数据的自动采集。

因此，这一次，尝试在本地服务器端布署 GA SDK，打算实现两个目的：

- 既解决 Google 服务器遭到拦截的问题
- 又不失去 GA 提供的各项功能，避免过多的手工实现

## 获取 analytics.js

由于使用的是最新的 GA 布署规范，加之本次的 Web 应用需要电商监测功能，因此，实际需要从 Google 拖到本地的有三个 JS 文件：
- gtag（js?id=UA-XXXXXXXXX-Y）
- analytics.js
- ec.js

## 修改 gtag.js

将 `js?id=UA-XXXXXXXXX-Y` 重命名为 `gtag.js`，查找并修改其中涉及到 `analytics.js` 与 `ec.js` 调用的两处代码。

```
#  be("https://www.google-analytics.com/analytics.js"
👉 be("./YOUR_PATH/analytics.js"

#  m("require","ec","ec.js")
👉 m("require","ec","./YOUR_PATH/ec.js")
```

这里有一个比较鸡贼的小技巧，但并不推荐使用：`analytics.js` 文件名可以改为其它不在 EasyList 名单内的词汇，这样可以避免被订阅了这些规则的 Adblock 工具拦截。

## 网页部署

相对于之前的 Universal Analytics，新版 `gtag.js` 更形似 Google Tag Manager，Data Layer 优先，Tracker 语法也发生了改变，但布署的基本套路不变。一般来讲，还是将 GA 片段部署在 `<head>` Tag 之内，以保证 `dataLayer` 和 `gtag()` 函数的优先声明，需要做的改动，只是其中 `gtag.js` 的路径。

```
<!-- Global site tag (gtag.js) - Google Analytics -->
<script async src="https://www.googletagmanager.com/gtag/js?id=XXXXXXXXX-Y"></script>
<script>
  window.dataLayer = window.dataLayer || [];
  function gtag(){dataLayer.push(arguments);}
  gtag('js', new Date());

  gtag('config', 'XXXXXXXXX-Y');
</script>

<!-- 由👆 -->
<!-- 变👇 -->

<script async src="./👉YOUR_PATH/gtag.js?id=XXXXXXXXX-Y"></script>
<script>
  window.dataLayer = window.dataLayer || [];
  function gtag(){dataLayer.push(arguments);}
  gtag('js', new Date());

  gtag('config', 'XXXXXXXXX-Y');
</script>

```

我这里的情形比较特殊，这个 SPA 应用使用了 webpack + Vue，并且要调用微信接口，需要考虑两件事，一是不因数据采集而有损用户体验，二是采集到 SPA 实际的页面/屏幕浏览，所以稍做了一些调整。模版 HTML 加载时，设置 `send_page_view` 为 `false`，即不发送初始 pageview 请求，并统一设置 `transport_type` 为无需回应且不阻塞其他资源请求 `beacon` 模式，关闭了 `display_features`，并在上线初期提升了 `site_speed` 的样本量，gtag.js 则统一推到了 `</body>` 结束前。

```
<head>
  <!-- 省略 -->
  <!-- 👇 -->
  <script>
    window.dataLayer = window.dataLayer || []
    function gtag(){dataLayer.push(arguments)}
    gtag('js', new Date())

    gtag('config', 'XXXXXXXXX-Y', {
      'send_page_view': false,
      'transport_type': 'beacon',
      'site_speed_sample_rate': 50,
      'allow_display_features': false,
      'currency': 'CNY',
      'custom_map': { /*...*/ }
    })
  </script>
  <!-- 👆 -->

</head>

<body>
  <div id="app"></div>
  <script src="./YOUR_PATH/wxcode.js?t=20171010"></script>
  <!-- 👇 -->
  <script async src="./YOUR_PATH/gtag.js?id=XXXXXXXXX-Y"></script>
  <!-- 👆 -->
  <script type="text/javascript" src="./YOUR_PATH/bundle.js?03203691ef4384df5efc"></script>
</body>
```

最后，真正的 pageview 采集则放在 Vue 路由的变更之处。至于其它配置细项，电商以及其它的事件监测，与 GA 官方文档一致，则不再赘述。

```
router.afterEach((to, from) => { gtag('event', 'page_view', { /* ... */ }) })
```

注意：目前 Beacon 并不是所有浏览器都支持，尤其 Safari。这一标准的出现，主要是为了满足浏览器端分析和诊断数据的发送。而 GA 引入这一 Hit 发送模式，也正是为了解决其他两个模式（image & xhr）的固有问题：占用并依赖 HTTP 响应；受 `unload` 事件影响。这里的 Beacon 模式在不支持的浏览器内，会自动回退为兼容的旧式 Hit 发送模式，不用担心数据丢失。

在这次试验中，暂时还没有考虑通过 webpack 的 code splitting 和 lazy loading 等的方式去打包 GA，之后可以试试看。
