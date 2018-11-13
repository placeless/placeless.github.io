---
layout: post
title:  "广告拦截"
date:   2016-08-02 06:01 +0800
redirect_from:
  - 2016/08/02/adblocking-experience
---

拦截网页广告已经过十年，动力有三：去除干扰、加速装载、保护私隐。方法折腾过很多，大体分为四类：DNS，Hosts，独立程序，浏览器扩展。目前主要使用的还是浏览器扩展：Adguard for Safari，uBlock Origin for Firefox & Chrome。

## 1. DNS

只要上网，总要通过 DNS 去把字母域名解析为 IP 地址，所以可以在 DNS 段设置障碍，拦截屏蔽广告流量。这里讲的主要是远程的 DNS 服务，非电脑本地的 DNS Cache。

新近的代表产品是 [Optimal](http://optimal.com/network-ad-blocking-beta/) 出的，刚刚 beta，承诺永久免费。CEO 叫做 Rob Leathern，为什么有印象，是因为他之前发过一系列檄文，例举一系列数据，声讨网络广告的祸害。也不知道是受了触动，索性搞了这项服务，还是为了铺垫这项服务，才先立的靶子。总之，这项名为 [cross-browser ad blocker beta](http://optimal.com/network-ad-blocking-beta/) 的广告拦截服务，无需在电脑生安装什么东西，只需把 DNS 配置为 Optimal 提供的 IP 地址即可。暂时分两区：Eastern UAS or UK 和 Western USA。体验过服务之后，你可以选择参与他们一项 $5.99/month 的[月费计划](http://optimal.com/our-product/)，这个钱的税后七成，用于补偿内容平台广告被拦截的损失……

这种方法的优点是：无客户端，轻量的不能再轻量了；不需要自己维护，几乎啥事不用干。但缺点也很明显：国外的用不了，勉强用了也太慢；国内的你不敢用，而且估计提供这种服务还[犯法](http://www.saic.gov.cn/zwgk/zyfb/zjl/xxzx/201607/t20160708_169638.html)。

## 2. HOSTS

修改 Hosts 来屏蔽广告跟 DNS 法类似，只不过修改的是本机的 hosts 文件，把广告服务器、垃圾站点、监测工具的域名，全部映射到本地 127.0.0.1 或者 0.0.0.0，这样，机器就不会再向远程请求广告和垃圾内容。

代表作品有 [StevenBlack/hosts](https://github.com/StevenBlack/hosts)，融合了广告、跟踪、社交、色情、赌博等多个数据源，几万条记录，可自定义。

这种方法的优点是效率高，跨浏览器和其他程序，全局屏蔽。缺点是：

1. Domain 拦截，不能指定内容，这个域下的所有想要的不想要的内容都被屏蔽了
2. 有一点配置门槛，hosts 修改更新、清理缓存都有一点麻烦
3. 无通配符、正则表达式支持，自己整理规则特别痛苦，别人的又有冗余，国外的也不完全适合
4. 容易被绕过：ad server 配置为 IP 地址，hosts 没有办法；广告和内容放在同一服务器上，Hosts 屏蔽了广告也就屏蔽了内容

折腾下来，我觉得最好还是仅维护一个极小的常见 ad server hosts 文件，搭配着其他手段使用。比如把百度、阿里、京东等巨头名单整理出来，加入 hosts 即可。几万条杂七杂八，维护、查询都麻烦，不划算。

## 3. 独立程序

这种方法是在 PC 上安装一个软件，独立运行，管理网络流量。国内也有，比如 AdSafe 什么的，我没有用过。

我使用的最早的全局广告拦截工具是 [Ad Muncher](https://www.admuncher.com)，至今已经有 15 年历史了。它本身是个很小的程序，安装之后，长驻系统托盘，通过内置规则拦截往来流量。后来不用了的原因有三：1. 它是收费软件，破解的我信不过，而那时我又还没有花钱买软件的习惯和钱；2. 它的自定义规则对那时候的我，门槛还有点高；3. 主要需要做广告拦截的场景还是浏览器，Firefox 进化到 2005 年的样子，好像就有了扩展系统，并出现了 Adblock 扩展，不再需要外置的独立程序来拦截广告。

## 4. 浏览器扩展

浏览器扩展是最常见，比较笨重，但又最为灵活的广告屏蔽方式。优点是自定义程度非常高，可选规则很多，搭配灵活。主要的缺点是性能问题，因为像 ABP，要验证每一个请求，往每张网页里插入一张大 CSS 去调 Layout，确实需要消耗一些资源，另外不如上面哪些方式的是，非全局，必须给每一个浏览器都安装一个 ABP 类的扩展。

大约在 2010 年以前，我还一直是 Firefox 的忠实用户和铁杆布道者，尤其是在学校的时候，每一台我经手安装的系统，必装 Firefox，并讲解其诸般好处。之后在 Windows 平台上，因为 Google Chrome 扩展系统日趋稳定，又有 Google 账户的云端同步和轻量的外观加分，逐渐主力使用 Google Chrome。但进入 macOS 之后，反复对比，还是选择 Safari 作为默认浏览器，无它，性能不错又省电。

之前在 Firefox 上，一直使用 Adblock Plus，Chrome 和 Safari 早前并没有 Adblock Plus 落户，用的一些其他的 Adblock 名头的替代，有了 ABP 之后，也很快换到了 ABP。2015 年哪个时候，接触了 uBlock，很快倾倒，Firefox 和 Chrome 全面转为 uBlock Origin。

uBlock Origin 相对于 ABP 的优势，网上有很多讲法，[Github 项目主页](https://github.com/gorhill/uBlock/) 上有详细的数据对比。除了这些，我喜欢的是它给我提供了验证另一个思路的工具，那就是先完全屏蔽所有第三方请求，访问到特定网站出现渲染问题时，再随我自己心意，手动临时/永久允许特定请求通过。

不同于其他屏蔽方式，先列举好黑名单，然后放到工具里去屏蔽。我觉得网站千千万万，广告来源举不胜举，但经常访问的站点就那么几个，为什么不反过来，先完全关上大门，再择机放行呢。试用过一段时间之后，我的感觉是：杀得确实干净，但现代的网站都比较复杂，既有动静态分离，又有 CDN 加速，还难免要请求一些公共资源，很难在万花丛中快速找到正确的去放行，这个积累的过程还挺折磨人，有一点得不偿失。

但目前我的主力浏览器 Safari 没有 uBlock Origin 的支持，好在 WWDC 2015 Apple 发布了 Safari 平台上的 Content Blocker 接口，拦截性能大有提升，于是我找了一个通过这一 API 去做广告屏蔽的扩展 Adguard，效果还不错。Content Blocker 默认不能超过 50000 条规则，订阅了 English 和 EasyList China 两条规则之后，就超过了 5 万，但目前尚未发现有什么问题。

## 总结

当前倾向的方案是：

1. uBlock Origin 开 [Medium Mode](https://github.com/gorhill/uBlock/wiki/Blocking-mode:-medium-mode)，花一段时间去积累常用网站的通行规则
2. 允许 uBlock Origin 在浏览器私隐模式下运行，其他的临时网页访问，全部放到私隐模式去浏览
3. Safari 没有 uBlock Origin，选择一个支持 Content Blocker 的扩展，比如 Adguard，暂用。