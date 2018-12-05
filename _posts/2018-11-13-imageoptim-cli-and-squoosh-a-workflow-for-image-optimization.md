---
layout: post
title:  "图片压缩：ImageOptim-CLI 与 Squoosh"
date:   2018-11-13 19:26 +0800
redirect_from:
  - 2018/11/13/imageoptim-cli-a-workflow-for-image-optimization
---

![image tool stack](/files/2018/image-tool-stack.png)

上图是我的图片处理工具栈，矢量原型 Sketch，位图编辑 Pixelmator，搜集管理 Inboard，压缩优化 JPEGmini、ImageAlpha、ImageOptim。前四个是付费 App，后两个自由开源。今天处理的图片较多，想能不能有个自动工作流来解放双手。

## ImageOptim-CLI

一搜，恰巧找到了 [JamieMason/**ImageOptim-CLI**](https://github.com/JamieMason) 这个 macOS 项目。一句话描述就是，它会根据你的指定，选择性调用 JPEGmini、ImageAlpha、ImageOptim 等工具，来处理图片压缩问题。开始敲一行命令，中间过程全自动。

79% TypeScript + 21% AppleScript，分发为可执行的二进制包，所以不依赖 Node.js，并且支持 Homebrew 安装。

使用也非常方便，终端输入 `imageoptim --help` 即可得到说明，只需注意三个事项：第一、终端要取得 Accessibility 权限；第二、调用的 App 要自己另外下载安装；第三、顾名思义，它默认调用 [ImageOptim](https://imageoptim.com)，如果不用，可通过 `--no-imageoptim` 参数 disable 它。

支持的调用的 3 个 App 如下，JPEGmini Lite 免费，标准和 Pro 均需付费：

```
  Supported Apps:

    ImageAlpha: https://pngmini.com
    ImageOptim: https://imageoptim.com
    JPEGmini Lite: https://itunes.apple.com/us/app/jpegmini-lite/id525742250
    JPEGmini Pro: https://itunes.apple.com/us/app/jpegmini-pro/id887163276
    JPEGmini: https://itunes.apple.com/us/app/jpegmini/id498944723
```

如需组合其他工具，添加对应参数即可，比如添加 `--jpegmini` 参数调用 JPEGmini：

```
  Examples:

    # Run ImageOptim.app over every image in current directory
    imageoptim

    # Run ImageAlpha.app and ImageOptim.app over every PNG in current directory
    imageoptim --imagealpha '**/*.png'

    # Run JPEGmini.app and ImageOptim.app over every JPG in current directory
    imageoptim --jpegmini '**/*.jpg' '**/*.jpeg'

    # Run JPEGmini.app over every JPG in current directory
    imageoptim --jpegmini --no-imageoptim '**/*.jpg' '**/*.jpeg'

    # Run ImageOptim.app over every image in a specific directory
    imageoptim '~/Desktop'
```

可调节的参数不多，除了外部工具调用和一些无关图片优化的命令之外，仅有一个全局的 `--no-color`，和 ImageAlpha 配备的 `--number-of-colors`，`--quality`，`--speed` 3 个参数。也就是说，如果希望在这里指定诸如转换图片格式、缩放尺寸、指定具体的质量数值、Progressive Rendering 等参数来实现高阶图片优化，是行不通的。它解决的是以同样的标准自动化批量处理图片的问题。

```
  Options:

    -V, --version           output the version number
    -a, --imagealpha        enable ImageAlpha
    -j, --jpegmini          enable JPEGmini
    -C, --no-color          output to the terminal without colors
    -I, --no-imageoptim     disable ImageOptim
    -Q, --no-quit           do not quit apps once finished
    -S, --no-stats          do not display file size savings and quality loss information
    --number-of-colors <n>  ImageAlpha palette size, defaults to 256
    --quality <min>-<max>   ImageAlpha quality range from 0-100, defaults to 65-80
    --speed <n>             ImageAlpha speed from 1 (brute-force) to 10 (fastest), defaults to 1
    -h, --help              output usage information
```

那么，这几个工具是单干还是组合好呢，哪个搭配压缩和质量最合理呢？针对这个问题，ImageOptim-CLI 贴心地给出了 GIF、JPEG、PNG 各个格式各个工具及组合的[压缩效果对比](http://jamiemason.github.io/ImageOptim-CLI/comparison/all/photoshop/desc/)。

![image tool stack](/files/2018/imageoptim-cli-comparison.png)

先看 JPEG 项目，果不其然，JPEGmini & ImageOptim 组合几无敌手，只在蓝天白云和 bril 人像上输给了 Kraken，当然，这个成绩主要是 JPEGmini 的功劳。

再看 PNG 项目，悬念只在 ImageOptim 单干以及与 ImageOptim 搭配两个方案之间。考虑到 24 位色的天际线、雀斑脸、蓝天白云等图片上，ImageOptim 单干弱势明显，还是建议二者无脑搭配。

综合以上两个项目来看，如果能接受较高的图片质量损耗（注意表格中无底色的红字），Kraken 也是非常有竞争力的单一优化工具。

## Squoosh

单张细校，因为 GPEGmini 没得选项可以调，唯有单开 ImageOptim 或是 PNGAlpha 做细节配置（其实也没太多可调的）。这里还有一个选项：Google 刚刚出炉的 [Squoosh](https://squoosh.app/)。

![squoosh screenshot](/files/2018/squoosh.png)

[GoogleChromeLabs/**squoosh**](https://github.com/GoogleChromeLabs) 是一个 Web App，跨 OS，可以本地部署，支持拖拉拽，简单易用。

它有两个基本模块，一是编辑；二是压缩。编辑就是缩放尺寸和减少颜色两项，而压缩就比较复杂了，根据输出格式不同，调节参数不同。大概也能分为两类：一类是 Squoosh 提供的编解码器（Codecs）比如下图中的 OptiPNG、MozJPEG、WebP；一类是你在使用的浏览器附带的，可能每个都不同，比如下图我的 Safari 带了 7 个 Codecs，而如果用 Chrome 打开，就只有 3 个，Firefox 也只有 3 个，但它不支持 WebP，所以 WebP 的位置是 BMP。

![squoosh codecs](/files/2018/squoosh_codecs.png){:height="50%" width="50%"}

话说，由于没有帮助文档，为了搞清 Squoosh 的这个 Browser-XX 具体是啥意思，是什么新潮编解码器，摸了半天。最后在源码里面顺藤摸瓜，跟着 `canvasEncode` 函数往下，才看到这么一句注释：敢情是浏览器自带的意思。

```
/**
 * Encode some image data in a given format using the browser's encoders
 *
 * @param {ImageData} data
 * @param {string} type A mime type, eg image/jpeg.
 * @param {number} [quality] Between 0-1.
 */

export async function canvasEncode(data: ImageData, type: string, quality?: number): Promise<Blob> {...
```

## 动手玩

举俩例子，一是批量处理下载回来的 Free Solo 提供的高清大图；一是优化一个广告背景图。

先是高清照片，这个没什么好说，照片细节丰富，一般默认输出为 JPEG，所以首选 JPEGmini，另加入一组 ImageOptim 结果作为对照。

| 照片             | 原大小  | JPEGmini | ImageOptim |
| :-------------- | -----: | -----: | ----: |
| Free Solo 1.jpg | 4.43MB | 67.07% | 5.42% |
| Free Solo 2.jpg | 5.94MB | 40.98% | 5.05% |
| Free Solo 3.jpg | 5.4MB  | 67.11% | 5.37% |
| Free Solo 4.jpg | 1.45MB | 52.14% | 5.25% |
| Free Solo 5.jpg | 3.33MB | 68.10% | 5.63% |
| Free Solo 6.jpg | 2.42MB | 73.54% | 2.78% |
| Free Solo 7.jpg | 4.07MB | 62.94% | 5.92% |
| Free Solo 8.jpg | 5.86MB | 78.96% | 5.80% |
| TOTAL           | 32.9MB | 63.89% | 5.30% |

如上可见，JPEGmini 非常出众，App 设计的优雅易用，处理 JPEG 花点钱在这个 App 上非常值。其实我还测试了 ImageOptim + JPEGmini 的方案，结果是，多一些处理等待时间，效果会再提升一丢丢。

再是广告图。它其实是一个在线广告的一部分，是一个动态创意的背景底图，其上会搭配不同的文案等元素，原格式为 PNG，原图 901kB，文件信息：PNG image data, 1600 x 515, 8-bit colormap, non-interlaced，现在来做一个压缩对比。

![squoosh ad](/files/2018/squoosh_ad.png)

先看 ImageOptim-CLI 的相关结果。如果要保有 PNG 格式，ImageAlpha 是个不错的工具，其默认编解码器是 pngquant，可选 pngnq。**需要注意的是，前面这俩，这都是有损压缩**，ImageOptim 默认采用 OptiPNG 等无损压缩工具，所以**只是看起来压缩效率低，开启有损模式，差别没那么大的**。

| 方案                    | 压缩结果 | 节省   |
| :---------------------- | -------: | -----: |
| ImageOptim              | 851kB    | 5.55%  |
| ImageAlpha              | 245kB    | 72.81% |
| ImageOptim + ImageAlpha | 222kB    | 75.38% |

再看 Squoosh 的跨浏览器方案。OptiPNG 无损，吃点亏；WebP 是厉害，但目前仅有谷歌自己家的产品支持，这个方案不能用；如果不保有原格式（反正它只是背景图嘛，不需要透明），完全可以转为 JPEG，使用 MozJPEG 75% 的质量，可以压缩掉 94% 的体积，仅剩 52kB，这对伺服效率的提升可不小，原来绝大部分的时间就是都消耗在了 Receiving ad for slot 上了。


| 方案                       | 压缩结果 | 节省 |
| :------------------------- | ------: | ---: |
| OptiPNG，effort=2          | 872kB    | 3%   |
| MozJPEG，quality=75        | 52.3kB   | 94%  |
| WebP，effort=4，quality=75 | 39.6kB   | 96%  |
