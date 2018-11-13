---
layout: post
title:  "图片压缩：ImageOptim-CLI"
date:   2018-11-13 19:26 +0800
redirect_from:
  - 2018/11/13/imageoptim-cli-a-workflow-for-image-optimization
---

![image tool stack](/files/2018/image-tool-stack.png)

上图是我的图片处理工具栈，矢量原型 Sketch，位图编辑 Pixelmator，搜集管理 Inboard，压缩优化 JPEGmini、ImageAlpha、ImageOptim。前四个是付费 App，后两个自由开源。

今天处理的图片较多，想能不能有个自动工作流来解放双手，一搜，恰巧找到了 [JamieMason/**ImageOptim-CLI**](https://github.com/JamieMason) 这个 macOS 项目。一句话描述就是，它会根据你的指定，选择性调用 JPEGmini、ImageAlpha、ImageOptim 等工具，来处理图片压缩问题。开始敲一行命令，中间过程全自动。

79% TypeScript + 21% AppleScript，分发为可执行的二进制包，所以不依赖 Node.js，并且支持 Homebrew 安装。

使用也非常方便，终端输入 `imageoptim --help` 即可得到说明，只需注意三个事项：第一、终端要取得 Accessibility 权限；第二、调用的 App 要自己另外下载安装；第三、顾名思义，它默认调用 [ImageOptim](https://imageoptim.com)，如果不用，可通过 `--no-imageoptim` 参数 disable 它，如需组合其他工具，添加对应参数，比如添加 `--jpegmini` 参数调用 JPEGmini。

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

最贴心的是，它还给出了 GIF、JPEG、PNG 各个格式各个工具及组合的[压缩效果对比](http://jamiemason.github.io/ImageOptim-CLI/comparison/all/photoshop/desc/)。

![image tool stack](/files/2018/imageoptim-cli-comparison.png)

先看 JPEG 项目，果不其然，JPEGmini & ImageOptim 组合几无敌手，只在蓝天白云和 bril 人像上输给了 Kraken，当然，这个成绩主要是 JPEGmini 的功劳。

再看 PNG 项目，悬念只在 ImageOptim 单干以及与 ImageOptim 搭配两个方案之间。考虑到 24 位色的天际线、雀斑脸、蓝天白云等图片上，ImageOptim 单干弱势明显，还是建议二者无脑搭配。

综合以上两个项目来看，如果能接受较高的图片质量损耗（注意表格中无底色的红字），Kraken 也是非常有竞争力的单一优化工具。