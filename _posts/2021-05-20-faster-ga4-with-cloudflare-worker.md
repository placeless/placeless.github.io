---
layout: post
title: '更快的GA4：通过 Cloudflare Worker 加速'
date: 2021-05-20 01:34 +0800
---

Google Analytics 在大概 10 个年头之后，终于从 Universal Analytics 大改至现在的 Google Analytics 4，它的前身是 App + Web，顾名思义，应该是希望通过一套统一的事件数据模型来实现跨平台的数据采集，解放用户在 Firebase for mobile 和 GA for web，在 Pageview 和 Events 之间反复横跳的错乱神经。核心思想，暂时可以简单归结为一句：一切皆事件。事件不再是 Category、Action、Label、Value 四件套，而是一个自由扩展的 Key-Value 对象。作为一个离开很久的回归用户，感觉变化很大，部署更灵活，门槛也更高了。

```js
gtag('event', 'view_item', {
  currency: 'USD',
  items: [
    {
      item_id: 'SKU_12345',
      item_name: 'jeggings',
      coupon: 'SUMMER_FUN',
      discount: 2.22,
      affiliation: 'Google Store',
      item_brand: 'Gucci',
      item_category: 'pants',
      item_variant: 'black',
      price: 9.99,
      currency: 'USD',
      quantity: 1,
    },
  ],
  value: 9.99,
});
```

老 GA 的一些功能，在 GA4 上基本都移植成了事件，比如 `first_visit`，`session_duration` 这种以前不需要采集端关注的维度或指标，页面浏览也有了专门的 `page_view` 事件，描述 pageview 语境的 `page_title`，`page_path`，`page_referrer` 等等，则都是作为 `page_view` 这一事件的参数，放在参数对象里面，形如 `gtag('event', 'page_view', {...})`。当然，GA4 的 SDK 把 pageview 这样的基本事件，都给我们集成好了，不需要自己去监控页面变化，去采集当页的 title，path，和 referrer 等。除此之外，GA4 也为一些常用场景所涉及的行为事件，做了模板，便于我们选用，比如电商监测，我们可以直接根据它的模板，实现对浏览商品、浏览商品列表、选择商品、浏览推广、选择推广等等电商场景的常见动作的数据收集。我们要做的，只是拿着模板找到触发位置，去接收对应的参数值即可，比如上面的 `view_item` 例子。

对于这个博客来说，需求很简单，只要采集 4 个东西：

- 文章的 Pageview
- 网站/页面性能参数（[Web Vitals](ftp://ftp16.eastgame.org:9922/2004/200408/08.06.04.Dawn.Of.The.Dead.2004.UNRATED-RiVER&TLF&DVDR@TLF.rar)）
- Dark Mode
- Ad Blocker

## SDK 还是 MP

以上是个人需求和 GA4 简要的事件逻辑梳理。使用工具，无论如何看文档是少不了的，其他细节可以自行去细看、动手玩。根据文档描述，我们可以这样把 GA4 事件分作两类：自动采集的、根据场景建议的（相当于提供一个模板）。注意这里的潜台词，获得这些功能的前提是使用 GA4 提供的 SDK，比如 gtag.js。如果使用 Measurement Protocol 而且又需要 GA4 默认报表的话，就需要拿着事件模板，自己去实现这些隐含信息的采集。

因此，我们加速工程的第一关，先面临 GA4 SDK 和 Measurement Protocol 的选择问题。SDK 体积大，可能加载慢，且涵盖了很多不需要的功能，线路基本被屏蔽；好处也很明显，省心。Measurement Protocol for GA4 好处是可以完全灵活自主，根本不需要走一条不通的路线去下载一个一百几十 K 的 js 文件（gzip 也有近 50K），但问题也很突出：要自己实现看得见看不见的背景功能。比如用户 ID、Cookie 机制、Session 机制、首访判断、互动判断等等一堆有的没的。而且需要注意，MP for GA4 现阶段还是 Alpha，原则上也是不建议用于生产。

## 下发中转

Anyway，对于个人试玩来讲，没那么讲究，但，这里还是选择用现成的 SDK，因为 MP 没太玩明白，虽然模拟了 Client ID、Session 切换、首访判断等，对比原版，还是差了意思，将来确定好了再回头走这条路。现在先用现成的，对于这个 js 文件的加速，当然就是大家想的那样：

- 直接下载 GA4 部署界面提供的、属于自己 Measurement ID 对应的 js
- 将其保存到网站的 js 目录里，将下载路径从 GA 域名改成自己的相对目录
- Cloudflare 开缓存

这里插一句，这个博客是 GitHub Pages，域名、DNS 等等都托管在 Cloudflare 上，用的也都是免费服务，这些配置这里就省略不细聊，基本就是登录 Cloudflare，进入这些功能页面，然后点几下鼠标的事情。

## 上传中转

GA 加速分两段，上边讲的是 SDK 的下发；现在进入到数据的上传。上传阶段的加速，对应的其实是各种浏览器拦截插件的屏蔽。基本思想是在 Client - GA 之间加一层中转，变成 Client - Server - GA。这里的 Server 我们用自己网站的地址，并在上边做两件事情，一是代理 GA 接收并解析上传的原 GA 请求，二是将其加工并转发到 GA 服务器。因为我们用的是静态博客，不方便直接做这两件事，所以这里的 Server 我们借助 Cloudflare 提供的 Worker 功能，有免费额度，目标也很契合。思路梳理出来是这样的：

- 在 Cloudflare 上做好域名解析
- 创建一个 [Cloudflare Worker](https://developers.cloudflare.com/workers/)
- 参考官方文档，写一个简单的转发 GA 请求的 Worker
- 为 Worker 映射一个自己网站地址的 route
- 更改 GA 的 `transport_url` 为自己网站域名
- 魔改 GA 的采集地址 `/g/collect` 为上面 route 路径

出来的结果，

```
v: 2
tid: G-ABCDEFG
gtm: 1234567
_p: 1039218207
sr: 2560x1440
ul: en-us
cid: 0123456789.9876543210
dl: https://placeless.net/blog/macos-dictionaries
dr: https://placeless.net/
dt: 《柯林斯双解》for macOS
sid: 1621444075
sct: 6
seg: 1
_s: 1

// Request Data

en=page_view
en=FCP&epn.value=1677&ep.metric_id=v1-1621444098204-8846853795459&epn.metric_value=1677&epn.metric_delta=1677&ep.metric_rating=good&up.color_mode=Dark&up.blocker=true
```

## 留意细节

注意四个细节，一是 GA4 不再支持自定义 `transport_type`，也就是说不能自主指定 xhr/image/beacon 三选一，系统完全接管了，优先 beacon，对于不支持的浏览器，系统会 fallback 到 Fetch POST，再不行才是最早先的像素图片 GET 形式；二是 `/g/collect` 改自定义路径，是在 gtag.js 里面，大致在 `var c=wj(a.s(E.Ba),"/g/collect")` 这个位置，请自行体会；三是 GA4 我目前没看到对 `uip` 参数的支持，也就是说不能像之前的 Measurement Protocol 那样追加 `uip` 参数，通过自行指定的 IP 地址，去覆写请求发起的源地址，比如这里的中转服务器地址，总之，通过我们上边的转发方式，所采集的用户地域分布，应该都是 Cloudflare 的 CDN 地域分布；最后，给 Cloudflare Worker 配置 route 时，对于 Jekyll 之类的静态生成器，请考虑贪婪匹配，比如 `/ga` 写成 `/ga*`，不然 Jekyll 可能先返回了 405 错误，因为静态服务器确实无法支持你的 beacon POST。

鄙人在下刚好也是一个极端的广告屏蔽者，看到这里的用户，可以将 `https://placeless.net/ma` 加入屏蔽规则，即可完全拦截上边提到的采集内容。除了上边提到的 4 条，GA4 还有一些预置逻辑，比如 10 秒算一个 Engagement 事件等。GA4 发请求现在支持 batch 模式，也就是短时间内触发了好几条事件，系统会把它们合并到一起，公共内容放在公用参数里面，Payload 里面装载事件和事件本身的描述参数，换行分割。

至于如何采集 Web Vitals 相关指标、Ad Blocker 标记、Dark Mode 适配等、以及 GA4 的请求参数有哪里多了哪些各自意义是什么，这里就不赘述了。

## 代码摘要

最后放参考代码，先是 worker 的部分，骨干部分非常简单，基本就是官方文档的复制粘贴：

```js
addEventListener('fetch', (event) => {
  // 可以追加一些请求过滤和回应的处理逻辑
  return event.respondWith(handleRequest(event.request));
});

async function readRequest(request) {
  const { url, headers } = request;
  const body = await request.text();
  const ga_url = url.replace(
    'https://你的域名/你的采集route',
    'https://www.google-analytics.com/g/collect'
  );
  const nq = {
    method: 'POST',
    headers: {
      Host: 'www.google-analytics.com',
      Origin: 'https://你的域名',
      'Cache-Control': 'max-age=0',
      'User-Agent': headers.get('user-agent'),
      Accept: headers.get('accept'),
      'Accept-Language': headers.get('accept-language'),
      'Content-Type': headers.get('content-type') || 'text/plain',
      Referer: headers.get('referer'),
    },
    body: body,
  };
  return new Request(nq);
}

async function handleRequest(event) {
  const newReq = await readRequest(event.request);
  event.waitUntil(fetch(newReq));
  return new Response(null, {
    status: 204,
    statusText: 'No Content',
  });
}
```

再是页面插码的部分：

```js
<script async src="/js/YOU-GA-SDK.js"></script>
<script>
  // function color_mode() {...}
  window.dataLayer = window.dataLayer || [];
  function gtag() { dataLayer.push(arguments); }
  gtag('js', new Date());

  gtag('config', 'GA4-Measurement-ID', { transport_url: 'https://YOUR-DOMAIN' });
  // gtag('set', 'user_properties', { color_mode: color_mode() });
</script>
```

## 推荐参考

- [使用 Cloudflare Workers 加速 Google Analytics](https://blog.skk.moe/post/cloudflare-workers-cfga/)
- [GoogleChrome/web-vitals](https://github.com/GoogleChrome/web-vitals)
