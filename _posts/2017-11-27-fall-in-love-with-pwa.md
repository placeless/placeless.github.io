---
layout: post
title:  "体验 PWA"
date:   2017-11-27 00:21 +0800
---

![低端人口](/files/2017/11/27/beijing.jpg)

我是一个 Open Web 的拥趸，但是移动时代，Native App 无论是在性能还是在功能上，都比 Web App 要好很多，所以还是老老实实在 App Store 下载应用。这虽不是一个无奈之举，但只要打开微信，进入到一个没有链接，却号称「生活方式」日常应用时，着实打心底丧气。微博也是一样的感觉，官方 App 接受不能，第三方 App 又倍受限制。

跟现实生活很像，要么你接受，要么被驱逐。

拿微信虽然没什么办法，但最近却发现，微博和饿了么的 PWA 应用完成度已经相当高了，完全可以取代 Native App，某一些方面体验甚至更好。比如微博，在我的常用网络环境，瞬时加载，几乎看不到左边的 App Shell，目前贴的 beta 标签，优雅便捷，没有广告，时间线正常，功能稍有欠缺，但基本是我用不到的，见图：

<div  style="text-align: center;">
<p>
<img src="/files/2017/11/27/weibo_app_shell.png" alt="weibo app shell" height="30%" width="30%" style="display: inline-block; margin: 0 5%;">
<img src="/files/2017/11/27/weibo.png" alt="weibo app shell" height="30%" width="30%" style="display: inline-block; margin: 0 5%;">
</p>
</div>

饿了么的完成度比微博更好，功能齐备，只加载稍慢了一点点，使用体验完全不输原生应用，实际使用当中，地址栏和底部工具栏会自适应上下滚动，进行缩放或隐藏，可用面积更大。

<div  style="text-align: center;">
<p>
<img src="/files/2017/11/27/ele_app_shell.png" alt="weibo app shell" height="30%" width="30%" style="display: inline-block; margin: 0 5%;">
<img src="/files/2017/11/27/ele_loading.png" alt="weibo app shell" height="30%" width="30%" style="display: inline-block; margin: 0 5%;">
</p>
</div>

上面的截图都是在 iPhone 完成的，但实际情况是，iOS 截至目前还并不完全支持 PWA，没有 offline support，没有 push notifications，没有 loading screen，没有 service worker 等等，上面展现的只是优化过的移动网页，或者说半截 PWA，并不是真正的 PWA 完全体。墙外有一个 [PWA Checklist](https://developers.google.com/web/progressive-web-apps/checklist) 列出了完美状态 PWA 功能和技术点。

手机屏幕有限，必需的 App 终究少数，能占据一小块桌面的 App，好比能舔到收藏夹内的网页，两者「入口」含义相同，组织方式也相差无几，但开放的 Web 更擅长于连接。

Web App 能贴到桌面，原生 App 也大把性能低下，产品和服务长成什么形式，原生还是 Web，可能逐渐不那么重要了。而态度上，不喜欢高墙花园，开放的 Web 真好。