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

