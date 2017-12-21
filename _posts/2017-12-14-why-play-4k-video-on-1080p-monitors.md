---
layout: post
title:  在 1080p/2K 屏幕上观看 4K 视频的意义
date:   2017-12-14 21:15 +0800
---

大约在 12 年前，1080p/i HD 刚刚兴起时代，有过在宿舍花一个月时间慢慢拖 40G 《最终幻想：灵魂深处》的经历，当时的主流硬盘配置是 80G，下载回来还要花很多时间去翻帖子，找到最合适的 Decoder 搭配，不然放不动。回想起来，真是甜美而忧伤。

现在 4K HDR 的 UHD 资源已经比较常见，作为一个持续高清追求者，蠢蠢欲动的心却遇到正反两股拉力，一边是手边的 MacBook Pro 已经 HEVC Ready，一边却是没有 4K 屏幕的尴尬。15 吋 MBP 分辨率只是 2800 x 1800，离 UHD 的 3840 x 2160 还有一段距离，也不想外接显示器或者电视，因为买了也再无其它用途。 🔪🍎

> … And 10-bit HEVC hardware acceleration comes standard on MacBook Pro, which will let you take even more advantage of the boosts in 4K video compression and streaming performance in macOS High Sierra.

想了想，其实要缓解这个尴尬，只要回答好一个问题就行：在分辨率不足 4K 的屏幕上看 4K 视频，有无额外的好处？

主观感受好验证，从 Youtube 上找一个 4K 视频，同时打开两个浏览器窗口，Quality 一个选 1080p，一个选 2160p，都全屏，细细对比，很容易得出结论：即便在不足 4K 的小分辨率屏幕上，仅凭肉眼感知，4K 的视觉效果依然明显好过 1080p。很好，剩下的问题是，为什么？为什么即便 4K 视频缩放到 1080 大小，视觉效果依然会好过原来 1080 p 的视频源？

搜索了一些知识库，也下载了一些资源做对比，目前找到 2 个可靠的理由：

- HDR
- Chroma Subsampling

## HDR

我的理解是，通过这项当红技术，画面当中的明暗范围更大，过渡更平滑，更接近人眼视觉对光线强弱的适应性处理，所谓「更自然」。下 2 图的上半部分截取自 1080p，下半部分截取自 2160p HDR，从明暗的层次、过渡上，个人喜好更倾向于 HDR 画面。

HDR 跟分辨率没有直接关系，但目前好像只有 4K 视频会得到 HDR 的加持，所以这个理由放到这里来说明 1080p 屏幕上看 4K 的好处，应该也算合情合理。HDR 对硬件规格有要求，移动端的 iPad Pro 新款是有 HDR 标记的，但 MBP 上没有，虽然参数上支持 10-bit 色深、P3 广色域、HEVC 硬解等特性，但屏幕亮度只有 500 nits，不满足桌面 HDR 的规格（1000 nits）。没关系，这一点缺憾能接受。

<p style="text-align:center;">

<img src = "/files/2017/12/15/drt.png" alt="1080 vs 4k" height="45%" width="45%" style="display: inline-block; margin: 0 1%;">
<img src = "/files/2017/12/15/war.png" alt="1080 vs 4k" height="45%" width="45%" style="display: inline-block; margin: 0 1%;">
</p>

## Chroma Subsampling

计算机在视频颜色处理上，并没有采用 RGB Color Space，而是 [Y'CbCr](https://en.m.wikipedia.org/wiki/YCbCr)，Y' 负责亮度，Cb 与 Cr 负责具体的颜色信息。Y'CbCr 表示为 `J:a:b`，所有像素的颜色信息全部保留计作 4:4:4，一般，视频使用 4:2:0 模式。如图，即每 4 个像素保留 4 个亮度 `Y`，但共享一个 `(Cb, Cr)` 的颜色信息，也就是说节省了 75% 的颜色信息。更多信息，可以通过 Wikipedia [Chroma Subsampling](https://en.wikipedia.org/wiki/Chroma_subsampling) 词条了解。

![different chroma subsampling schemes](http://lea.hamradio.si/~s51kq/subsample.gif)

<figcaption>常见 Y'CbCr 模式，来自：http://lea.hamradio.si/~s51kq/subsample.gif</figcaption>

至于为什么常见视频不采用保留全部像素颜色信息的 4:4:4 模式，是因为人眼对亮度的感知，比对颜色区隔要更敏感。在这个敏感度范围内，像上图一样，尽量保留所有像素的亮度，丢弃掉一些临近像素的颜色信息，既可以几乎无损颜色识别，又可以达到大幅减小文件体积和加快像素处理的目的。比如，1080p 拥有 1920 x 1080 = 2,073,600 个像素，2160p 更是它的 4 倍，达到 8,294,400 个像素，这样的取舍看起来是合情合理的。

关键的问题来了，这跟缩放到 1080p 的 4K 效果好于原来的 1080p 有什么关系？跟上面的 4 倍很有关系，直观解释来源于这个 [YouTube](https://www.youtube.com/watch?v=kIf9h2Gkm_U) 视频：这一缩放过程，相当于把 4 个 4:2:0 的区域合并为一个 4:4:4 的区域，缩放的 1080p 相对于原来的 1080p，颜色细节要丰富的多了，所以效果更好了。


当然，高清的规格跟其他的技术规格一样，是在不断进步和完善的，相信不出几年，支持 12-bit 的 4:2:2 就会成为主流，可以看 Wikipedia 词条，了解更多当前[ H.265/HEVC](https://en.m.wikipedia.org/wiki/High_Efficiency_Video_Coding) 编码的规格。

最后，好玩的。看下面两个文件的统计，同一部视频，不同压制组出品，4K UHD 资源是 1080p HD 的 200% 体积，但仅就视频部分而言，HEVC 编码的 4K 比 AVC 编码的 1080p 只大了 46%，其中巨大的差异部分，是 4K 资源内置的高规格无损音频，这对于我们只有两到三个喇叭的笔记本用户来说，就相当鸡肋了😅。所以说，完美的 4K 体验，音响的问题，可千万不能忽略。

```bash
...
File size                                : 7.95 GiB
...
Codec ID                                 : V_MPEG4/ISO/AVC
Duration                                 : 2 h 3 min
Bit rate                                 : 8 597 kb/s
Width                                    : 1 920 pixels
Height                                   : 792 pixels
Display aspect ratio                     : 2.40:1
Frame rate mode                          : Constant
Frame rate                               : 23.976 FPS
Color space                              : YUV
Chroma subsampling                       : 4:2:0
Bit depth                                : 8 bits
Scan type                                : Progressive
Bits/(Pixel*Frame)                       : 0.236
Stream size                              : 7.39 GiB (93%)
...
Audio
Compression mode                         : Lossy
Stream size                              : 564 MiB (7%)
...
```

```bash
...
File size                                : 15.6 GiB
...
Codec ID                                 : V_MPEGH/ISO/HEVC
Duration                                 : 2 h 3 min
Bit rate                                 : 12.6 Mb/s
Width                                    : 3 840 pixels
Height                                   : 1 580 pixels
Display aspect ratio                     : 2.40:1
Frame rate mode                          : Constant
Frame rate                               : 23.976 (24000/1001) FPS
Color space                              : YUV
Chroma subsampling                       : 4:2:0 (Type 2)
Bit depth                                : 10 bits
Bits/(Pixel*Frame)                       : 0.086
Stream size                              : 10.8 GiB (69%)
...
Audio #1
Compression mode                         : Lossless
Stream size                              : 4.16 GiB (27%)
Audio #2
Compression mode                         : Lossy
Stream size                              : 564 MiB (4%)
...
```

MacBook Pro 的扬声器在 PC 当中已经很有竞争力了，但实际对比之下，感觉 iPad Pro 的四个喇叭，还要更加的环绕立体。新款 iPad Pro 是完全支持 4K HDR 硬解的，且 iOS 9.3 开始，都内置 DTS 音效支持，花 30 块人民币买一个 [nPlayer](https://itunes.apple.com/us/app/nplayer/id1116905928?mt=8)，通过千兆局域网 SMB 播放高清视频，体验也相当不错。

<div id="main" class="main" role="main">
  <style>
    .section-speakers {
        overflow: hidden;
        background-color: #f6f6f6;
        padding-top: 60px;
    }
    .section-speakers .speakers-animation-container.activated .image-speakers-images .image-speakers-internal {
        opacity: 0
    }
    .section-speakers .image-speakers-hero {
        width: 344px;
        height: 202px;
        left: 50%;
        margin-left: -172px;
        position: relative
    }
    .section-speakers .image-speakers-hero .image-speakers-images,.section-speakers .image-speakers-hero .speakers-animation-renderers {
        position: absolute;
        width: 100%;
        height: 100%;
        top: 0;
        left: 0;
        pointer-events: none
    }
    .section-speakers .image-speakers-hero .image-speakers-images {
        z-index: 1
    }
    .section-speakers .image-speakers-hero .image-speakers-images .image-speakers-image {
        position: absolute;
        top: 0;
        left: 0
    }
    .section-speakers .image-speakers-hero .image-speakers-images .image-speakers-external {
        z-index: 1;
        width:344px;
        height: 202px;
        background-size: 344px 202px;
        background-repeat: no-repeat;
        background-image: url("https://images.apple.com/v/macbook-pro/n/images/overview/speakers_small_2x.jpg")
    }
    .section-speakers .image-speakers-hero .image-speakers-images .image-speakers-internal {
        z-index: 2;
        will-change: opacity;
        -webkit-transition: opacity 1s ease-out;
        transition: opacity 1s ease-out;
        width:344px;
        height: 202px;
        background-size: 344px 202px;
        background-repeat: no-repeat;
        background-image: url("https://images.apple.com/v/macbook-pro/n/images/overview/speakers_before_small_2x.jpg")
    }
    .section-speakers .image-speakers-hero .speakers-animation-renderers {
        z-index: 2
    }
    .section-speakers .image-speakers-hero .speakers-animation-renderers .speakers-animation {
        position: absolute
    }
    .section-speakers .image-speakers-hero .speakers-animation-renderers .speakers-animation.speakers-small {
        top: 108px;
        -webkit-transform: scale(1.5);
        -ms-transform: scale(1.5);
        transform: scale(1.5);
        -webkit-filter: saturate(0.4);
        filter: saturate(0.4)
    }
    .section-speakers .image-speakers-hero .speakers-animation-renderers .speakers-animation canvas {
        -webkit-transform:scale(.4);
        -ms-transform: scale(.4);
        transform: scale(.4)
    }
    .section-speakers .image-speakers-hero .speakers-animation-renderers .speakers-animation.speakers-small {
        top: -144px
    }
    .section-speakers .image-speakers-hero .speakers-animation-renderers .speakers-animation.speakers-large {
        top: -204px
    }
    .section-speakers .image-speakers-hero .speakers-animation-renderers .speakers-animation.speakers-left-small {
        left: -200px
    }
    .section-speakers .image-speakers-hero .speakers-animation-renderers .speakers-animation.speakers-left-large {
        left: -55px
    }
    .section-speakers .image-speakers-hero .speakers-animation-renderers .speakers-animation.speakers-right-small {
        right: -200px
    }
    .section-speakers .image-speakers-hero .speakers-animation-renderers .speakers-animation.speakers-right-large {
        right: -55px
    }
    html.no-webgl .section-speakers .image-speakers-hero .speakers-animation-renderers .speakers-animation,html.no-js .section-speakers .image-speakers-hero .speakers-animation-renderers .speakers-animation {
        -webkit-transform:scale(0.4);
        -ms-transform: scale(0.4);
        transform: scale(0.4)
    }
    html.no-webgl .section-speakers .image-speakers-hero .speakers-animation-renderers .speakers-left-small,html.no-js .section-speakers .image-speakers-hero .speakers-animation-renderers .speakers-left-small {
        width: 500px;
        height: 500px;
        background-size: 500px 500px;
        background-repeat: no-repeat;
        background-image: url("https://images.apple.com/v/macbook-pro/n/images/overview/wave_fallback_top_left_large.png")
    }
    html.no-webgl .section-speakers .image-speakers-hero .speakers-animation-renderers .speakers-left-large,html.no-js .section-speakers .image-speakers-hero .speakers-animation-renderers .speakers-left-large {
        width: 700px;
        height: 700px;
        background-size: 700px 700px;
        background-repeat: no-repeat;
        background-image: url("https://images.apple.com/v/macbook-pro/n/images/overview/wave_fallback_bottom_large.png")
    }
    html.no-webgl .section-speakers .image-speakers-hero .speakers-animation-renderers .speakers-right-small,html.no-js .section-speakers .image-speakers-hero .speakers-animation-renderers .speakers-right-small {
        width: 500px;
        height: 500px;
        background-size: 500px 500px;
        background-repeat: no-repeat;
        background-image: url("https://images.apple.com/v/macbook-pro/n/images/overview/wave_fallback_top_right_large.png")
    }
    html.no-webgl .section-speakers .image-speakers-hero .speakers-animation-renderers .speakers-right-large,html.no-js .section-speakers .image-speakers-hero .speakers-animation-renderers .speakers-right-large {
        width: 700px;
        height: 700px;
        background-size: 700px 700px;
        background-repeat: no-repeat;
        background-image: url("https://images.apple.com/v/macbook-pro/n/images/overview/wave_fallback_bottom_large.png")
    }
    .section-speakers .gl-speakers-replay-button {
        display: block;
        text-align: center;
        max-width: 50px;
        margin: 0 auto;
        cursor: pointer;
        color: #d0d0d0;
    }
    html.no-js .image-speakers-internal {
        opacity: 0
    }

  </style>
  <section class="section section-speakers" data-component-list="LoadingComponent">
    <div class="section-content">
      <div class="speakers-animation-container" data-component-list="SpeakersComponent" data-engaged='{"inViewThreshold":0.2, "timeToEngage":500}'>
        <figure class="image-speakers-hero progressive-image">
          <div class="image-speakers-images">
            <div class="image-speakers-image image-speakers-internal"></div>
            <div class="image-speakers-image image-speakers-external"></div>
          </div>
          <div class="speakers-animation-renderers">
            <div class="speakers-animation speakers-left-small speakers-left speakers-small"></div>
            <div class="speakers-animation speakers-left-large speakers-left speakers-large"></div>
            <div class="speakers-animation speakers-right-small speakers-right speakers-small"></div>
            <div class="speakers-animation speakers-right-large speakers-right speakers-large"></div>
          </div>
        </figure>
        <p class="speakers-image-description">
          <p class="js-speakers-replay no-gl gl-speakers-replay-button">重放</p>
        </p>
      </div>
    </div>
  </section>
  <script src="https://images.apple.com/v/macbook-pro/n/built/scripts/head.built.js" type="text/javascript" charset="utf-8"></script>
  <script src="https://www.apple.com/ac/ac-films/5.1.0/scripts/ac-films.built.js" type="text/javascript"></script>
  <script src="https://images.apple.com/v/macbook-pro/n/built/scripts/main.built.js" type="text/javascript" charset="utf-8"></script>
</div>

<div>
  <style>
    #speakers {
    -webkit-transition: -webkit-transform 0.6s cubic-bezier(0.86, 0, 0.07, 1);
    transition: transform 0.6s cubic-bezier(0.86, 0, 0.07, 1);
    text-align: center;
    margin: 2em;
}

  </style>
<div id="speakers" class="neutral">
  <svg id="animation" class="animation" width="293" height="199" data-medheight="179" data-medwidth="264" data-smheight="199"
    data-smwidth="293" viewBox="0 -8 295 204">
    <circle fill="none" stroke="#333333" stroke-linecap="round" stroke-linejoin="round" stroke-miterlimit="10" cx="46.2" cy="94"
      r="1.3"></circle>
    <path fill="none" stroke="#333333" stroke-linecap="round" stroke-linejoin="round" stroke-miterlimit="10" d="M147.5,23c51,0,92.9,0,93,0c0.1,0,0.4,0.1,0.5,0.1c0.1,0,0.3,0.2,0.4,0.2c0.1,0.1,0.2,0.2,0.3,0.4s0.1,0.3,0.1,0.4c0,0.1,0,0.2,0,0.3c0,0.1,0,31.4,0,69.6s0,69.6,0,69.6c0,0.1,0,0.2,0,0.3c0,0.1-0.1,0.2-0.1,0.4c-0.1,0.1-0.2,0.3-0.3,0.4c-0.1,0.1-0.3,0.2-0.4,0.2c-0.1,0-0.3,0.1-0.5,0.1c-0.1,0-42,0-93,0s-92.8,0-92.9,0c-0.1,0-0.2,0-0.3,0s-0.2-0.1-0.4-0.1c-0.1-0.1-0.3-0.2-0.4-0.3c-0.1-0.1-0.2-0.3-0.2-0.4c0-0.1-0.1-0.3-0.1-0.5c0-0.1,0-31.5,0-69.7s0-69.6,0-69.7c0-0.1,0.1-0.4,0.1-0.5c0-0.1,0.2-0.3,0.2-0.4c0.1-0.1,0.2-0.2,0.4-0.3c0.1-0.1,0.3-0.1,0.4-0.1c0.1,0,0.2,0,0.3,0S96.5,23,147.5,23z"></path>
    <circle fill="none" stroke="#333333" stroke-linecap="round" stroke-linejoin="round" stroke-miterlimit="10" cx="248.7"
      cy="94" r="3.9"></circle>
    <polygon fill="none" stroke="#333333" stroke-linecap="round" stroke-linejoin="round" stroke-miterlimit="10"
      points="244.8,172.2 245.8,172.2 246.7,172.2 247.5,172.2 248.2,172.1 248.8,172.1 249.5,172 250.1,172 251.1,171.7 252.1,171.3 253,170.8 253.8,170.1 254.5,169.3 255,168.4 255.4,167.4 255.6,166.4 255.7,165.8 255.8,165.2 255.8,164.5 255.8,163.8 255.8,163.1 255.8,162.2 255.8,161.1 255.8,26.9 255.8,25.8 255.8,24.9 255.8,24.2 255.8,23.5 255.8,22.8 255.7,22.2 255.6,21.6 255.4,20.6 255,19.6 254.5,18.7 253.8,17.9 253,17.2 252.1,16.7 251.1,16.3 250.1,16 249.5,16 248.8,15.9 248.2,15.9 247.5,15.8 246.7,15.8 245.8,15.8 244.8,15.8 50.2,15.8 49.2,15.8 48.3,15.8 47.5,15.8 46.8,15.9 46.2,15.9 45.5,16 44.9,16 43.9,16.3 42.9,16.7 42,17.2 41.2,17.9 40.5,18.7 40,19.6 39.6,20.6 39.4,21.6 39.3,22.2 39.2,22.8 39.2,23.5 39.2,24.2 39.2,24.9 39.2,25.8 39.2,26.9 39.2,161.1 39.2,162.2 39.2,163.1 39.2,163.8 39.2,164.5 39.2,165.2 39.3,165.8 39.4,166.4 39.6,167.4 40,168.4 40.5,169.3 41.2,170.1 42,170.8 42.9,171.3 43.9,171.7 44.9,172 45.5,172 46.2,172.1 46.8,172.1 47.5,172.2 48.3,172.2 49.2,172.2 50.2,172.2"></polygon>
    <g>
      <defs>
        <path id="mask" d="M48.3,176.2l-0.9,0l-0.8,0l-0.7,0l-0.8-0.1l-0.9-0.1l-1.5-0.4l-1.5-0.6l-1.5-0.9l-1.3-1.1l-1.1-1.3l-0.9-1.5l-0.6-1.5l-0.4-1.5l-0.1-0.9l-0.1-0.8l0-0.8l0-0.8l0-0.8l0-2l0-136.2l0-0.9l0-0.8l0-0.7l0.1-0.8l0.1-0.9l0.4-1.5l0.6-1.5l0.9-1.5l1.1-1.3l1.3-1.1l1.5-0.9l1.5-0.6l1.5-0.3l0.9-0.1l0.8-0.1l0.8,0l0.8,0l0.8,0l2,0l196.5,0l0.9,0l0.8,0l0.7,0l0.8,0.1l0.9,0.1l1.5,0.3l1.5,0.6l1.5,0.9l1.3,1.1l1.1,1.3l0.9,1.5l0.6,1.5l0.4,1.5l0.1,0.9l0.1,0.8l0,0.8l0,0.8l0,0.8l0,2l0,136.2l0,0.9l0,0.8l0,0.7l-0.1,0.8l-0.1,0.9l-0.4,1.5l-0.6,1.5l-0.9,1.5l-1.1,1.3l-1.3,1.1l-1.5,0.9l-1.5,0.6l-1.5,0.4l-0.9,0.1l-0.8,0.1l-0.8,0l-0.8,0l-0.8,0l-0.9,0L48.3,176.2z M-13.4,211.7h321.9V-23.7H-13.4V211.7z"></path>
      </defs>
      <clipPath id="clip">
        <use xmlns:xlink="http://www.w3.org/1999/xlink" xlink:href="#mask" overflow="visible"></use>
      </clipPath>
    </g>
    <g id="topLeft">
      <circle clip-path="url(#clip)" fill="none" stroke="#333333" stroke-width="0.7" stroke-linecap="round" stroke-linejoin="round"
        stroke-miterlimit="10" cx="39.2" cy="30.9" r="28.94751826048538" class="wave bass" style="opacity: 0.213712;"></circle>
      <circle clip-path="url(#clip)" fill="none" stroke="#333333" stroke-width="1.1" stroke-linecap="round" stroke-linejoin="round"
        stroke-miterlimit="10" cx="39.2" cy="30.9" r="24.47117575388012" class="wave treb" style="opacity: 0.349358;"></circle>
      <circle clip-path="url(#clip)" fill="none" stroke="#333333" stroke-width="0.7" stroke-linecap="round" stroke-linejoin="round"
        stroke-miterlimit="10" cx="39.2" cy="30.9" r="19.16970800422734" class="wave bass" style="opacity: 0.510009;"></circle>
      <circle clip-path="url(#clip)" fill="none" stroke="#333333" stroke-width="1.1" stroke-linecap="round" stroke-linejoin="round"
        stroke-miterlimit="10" cx="39.2" cy="30.9" r="13.24684744404871" class="wave treb" style="opacity: 0.689489;"></circle>
      <circle clip-path="url(#clip)" fill="none" stroke="#333333" stroke-width="0.7" stroke-linecap="round" stroke-linejoin="round"
        stroke-miterlimit="10" cx="39.2" cy="30.9" r="6.9302062830936455" class="wave bass" style="opacity: 0.880903;"></circle>
      <circle clip-path="url(#clip)" fill="none" stroke="#333333" stroke-width="1.1" stroke-linecap="round" stroke-linejoin="round"
        stroke-miterlimit="10" cx="39.2" cy="30.9" r="35.90229845246923" class="wave treb" style="opacity: 0.00296065;"></circle>
      <circle clip-path="url(#clip)" fill="none" stroke="#333333" stroke-width="0.7" stroke-linecap="round" stroke-linejoin="round"
        stroke-miterlimit="10" cx="39.2" cy="30.9" r="34.77505407642333" class="wave bass" style="opacity: 0.0371196;"></circle>
      <circle clip-path="url(#clip)" fill="none" stroke="#333333" stroke-width="1.1" stroke-linecap="round" stroke-linejoin="round"
        stroke-miterlimit="10" cx="39.2" cy="30.9" r="32.426712191876106" class="wave treb" style="opacity: 0.108281;"></circle>
    </g>
    <g id="botLeft">
      <circle clip-path="url(#clip)" fill="none" stroke="#333333" stroke-width="0.7" stroke-linecap="round" stroke-linejoin="round"
        stroke-miterlimit="10" cx="39.2" cy="157.1" r="28.94751826048538" class="wave bass" style="opacity: 0.213712;"></circle>
      <circle clip-path="url(#clip)" fill="none" stroke="#333333" stroke-width="1.1" stroke-linecap="round" stroke-linejoin="round"
        stroke-miterlimit="10" cx="39.2" cy="157.1" r="24.47117575388012" class="wave treb" style="opacity: 0.349358;"></circle>
      <circle clip-path="url(#clip)" fill="none" stroke="#333333" stroke-width="0.7" stroke-linecap="round" stroke-linejoin="round"
        stroke-miterlimit="10" cx="39.2" cy="157.1" r="19.16970800422734" class="wave bass" style="opacity: 0.510009;"></circle>
      <circle clip-path="url(#clip)" fill="none" stroke="#333333" stroke-width="1.1" stroke-linecap="round" stroke-linejoin="round"
        stroke-miterlimit="10" cx="39.2" cy="157.1" r="13.24684744404871" class="wave treb" style="opacity: 0.689489;"></circle>
      <circle clip-path="url(#clip)" fill="none" stroke="#333333" stroke-width="0.7" stroke-linecap="round" stroke-linejoin="round"
        stroke-miterlimit="10" cx="39.2" cy="157.1" r="6.9302062830936455" class="wave bass" style="opacity: 0.880903;"></circle>
      <circle clip-path="url(#clip)" fill="none" stroke="#333333" stroke-width="1.1" stroke-linecap="round" stroke-linejoin="round"
        stroke-miterlimit="10" cx="39.2" cy="157.1" r="35.90229845246923" class="wave treb" style="opacity: 0.00296065;"></circle>
      <circle clip-path="url(#clip)" fill="none" stroke="#333333" stroke-width="0.7" stroke-linecap="round" stroke-linejoin="round"
        stroke-miterlimit="10" cx="39.2" cy="157.1" r="34.77505407642333" class="wave bass" style="opacity: 0.0371196;"></circle>
      <circle clip-path="url(#clip)" fill="none" stroke="#333333" stroke-width="1.1" stroke-linecap="round" stroke-linejoin="round"
        stroke-miterlimit="10" cx="39.2" cy="157.1" r="32.426712191876106" class="wave treb" style="opacity: 0.108281;"></circle>
    </g>
    <g id="topRight">
      <circle clip-path="url(#clip)" fill="none" stroke="#333333" stroke-width="0.7" stroke-linecap="round" stroke-linejoin="round"
        stroke-miterlimit="10" cx="255.8" cy="30.9" r="28.94751826048538" class="wave bass" style="opacity: 0.213712;"></circle>
      <circle clip-path="url(#clip)" fill="none" stroke="#333333" stroke-width="1.1" stroke-linecap="round" stroke-linejoin="round"
        stroke-miterlimit="10" cx="255.8" cy="30.9" r="24.47117575388012" class="wave treb" style="opacity: 0;"></circle>
      <circle clip-path="url(#clip)" fill="none" stroke="#333333" stroke-width="0.7" stroke-linecap="round" stroke-linejoin="round"
        stroke-miterlimit="10" cx="255.8" cy="30.9" r="19.16970800422734" class="wave bass" style="opacity: 0.510009;"></circle>
      <circle clip-path="url(#clip)" fill="none" stroke="#333333" stroke-width="1.1" stroke-linecap="round" stroke-linejoin="round"
        stroke-miterlimit="10" cx="255.8" cy="30.9" r="13.24684744404871" class="wave treb" style="opacity: 0;"></circle>
      <circle clip-path="url(#clip)" fill="none" stroke="#333333" stroke-width="0.7" stroke-linecap="round" stroke-linejoin="round"
        stroke-miterlimit="10" cx="255.8" cy="30.9" r="6.9302062830936455" class="wave bass" style="opacity: 0.880903;"></circle>
      <circle clip-path="url(#clip)" fill="none" stroke="#333333" stroke-width="1.1" stroke-linecap="round" stroke-linejoin="round"
        stroke-miterlimit="10" cx="255.8" cy="30.9" r="35.90229845246923" class="wave treb" style="opacity: 0;"></circle>
      <circle clip-path="url(#clip)" fill="none" stroke="#333333" stroke-width="0.7" stroke-linecap="round" stroke-linejoin="round"
        stroke-miterlimit="10" cx="255.8" cy="30.9" r="34.77505407642333" class="wave bass" style="opacity: 0.0371196;"></circle>
      <circle clip-path="url(#clip)" fill="none" stroke="#333333" stroke-width="1.1" stroke-linecap="round" stroke-linejoin="round"
        stroke-miterlimit="10" cx="255.8" cy="30.9" r="32.426712191876106" class="wave treb" style="opacity: 0;"></circle>
    </g>
    <g id="botRight">
      <circle clip-path="url(#clip)" fill="none" stroke="#333333" stroke-width="0.7" stroke-linecap="round" stroke-linejoin="round"
        stroke-miterlimit="10" cx="255.8" cy="157.1" r="28.94751826048538" class="wave bass" style="opacity: 0.213712;"></circle>
      <circle clip-path="url(#clip)" fill="none" stroke="#333333" stroke-width="1.1" stroke-linecap="round" stroke-linejoin="round"
        stroke-miterlimit="10" cx="255.8" cy="157.1" r="24.47117575388012" class="wave treb" style="opacity: 0;"></circle>
      <circle clip-path="url(#clip)" fill="none" stroke="#333333" stroke-width="0.7" stroke-linecap="round" stroke-linejoin="round"
        stroke-miterlimit="10" cx="255.8" cy="157.1" r="19.16970800422734" class="wave bass" style="opacity: 0.510009;"></circle>
      <circle clip-path="url(#clip)" fill="none" stroke="#333333" stroke-width="1.1" stroke-linecap="round" stroke-linejoin="round"
        stroke-miterlimit="10" cx="255.8" cy="157.1" r="13.24684744404871" class="wave treb" style="opacity: 0;"></circle>
      <circle clip-path="url(#clip)" fill="none" stroke="#333333" stroke-width="0.7" stroke-linecap="round" stroke-linejoin="round"
        stroke-miterlimit="10" cx="255.8" cy="157.1" r="6.9302062830936455" class="wave bass" style="opacity: 0.880903;"></circle>
      <circle clip-path="url(#clip)" fill="none" stroke="#333333" stroke-width="1.1" stroke-linecap="round" stroke-linejoin="round"
        stroke-miterlimit="10" cx="255.8" cy="157.1" r="35.90229845246923" class="wave treb" style="opacity: 0;"></circle>
      <circle clip-path="url(#clip)" fill="none" stroke="#333333" stroke-width="0.7" stroke-linecap="round" stroke-linejoin="round"
        stroke-miterlimit="10" cx="255.8" cy="157.1" r="34.77505407642333" class="wave bass" style="opacity: 0.0371196;"></circle>
      <circle clip-path="url(#clip)" fill="none" stroke="#333333" stroke-width="1.1" stroke-linecap="round" stroke-linejoin="round"
        stroke-miterlimit="10" cx="255.8" cy="157.1" r="32.426712191876106" class="wave treb" style="opacity: 0;"></circle>
    </g>
  </svg>
</div>
</div>

<figcaption>MacBook Pro 与 iPad Pro 的扬声器示意图</figcaption>