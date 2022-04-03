---
layout: post
title: 'MacBook Pro 2021 使用体验'
date: 2022-03-30 17:30 +0800
---

今年，库克的刀法更加随心所欲了，新款 MacBook Pro，硬是按照尺寸、类型、CPU、GPU、SSD 给你切出一个家族来。于是，14 还是 16；Pro 还是 Max；32 还是 64，512 还是 1T，个个都是选择题。

为了省事，先是点了一个 16 寸高配，加到 64G 内存，但由于64G 内存款等待时间实在太长，足有两期花呗，加上又看了[专业人士的配置](https://imtx.me/blog/some-thoughts-after-ordering-the-m1-pro-macbook-pro/)，感觉 32G 应该够我用了，于是退单，最终选择 M1 Max 24 核 GPU + 32G 内存 + 1T SSD 款，收货预期压缩到两周。

截至目前，使用了 4 个月，聊聊感受。

一句话来说，对于一个从 2017 款 15 寸 MBP 过来的用户，以上配置完全够用，体验提升很大。

## 刘海

刘海所处的一整条屏幕区域，都是 Safe Area，其他窗口进不去，只有从外接显示器往内建屏幕拖入窗口时，能暂时驻留这片区域，不被自动弹开。其实没啥用。跟我使用习惯类似的用户，完全享受不到苹果所说的「额外赠送显示面积」，因为我们永远隐藏菜单栏，隐藏 Dock，这一片只能放菜单栏的区域，约等于无。反而还要想办法找黑色壁纸，把刘海藏起来。

## 优点

* 屏幕
    * HDR 影片的效果是前（MBP）所未有的
    * 用了就回不去的功能 😱
* 2x 整数缩放
    * 清晰度提升，略有感知
* 120Hz 高刷
    * 略有感知
    * 用一阵之后，跟老款对比，感知超级明显 😱
* 剪刀键盘
    * 手感有提升
    * 用一阵之后：我原来用的是什么烂东西 😱
* 散热和温度
    * 直接：我原来究竟用的是什么烂东西 😱
* 外接显示器时的散热和噪音：
    * 我TM原来究竟用的是什么烂东西 😱
* 外放喇叭
    * 大幅提升，明显感知
* Fn 功能键
    * 拜拜 Touch Bar
* Playgrounds + SwiftUI 终流畅

##  缺点

* 打字时，薛定谔的选字框，不知道它会出现在哪里
    * 系统问题，重启/更新解决
* Control Center 内存泄漏
    * 停止 Sharing 面板下的 AirPlay Receiver 服务
* 软件兼容，没有安装 Rosetta，完全 Arm64 环境
    * RStudio（在 Daily 渠道找 Electron Desktop 解决）
    * Charles Proxy ❎
* 刘海刺眼
    * 选一张合适的壁纸，实现隐藏
* 键盘背光
    * 边缘透光，没有蝶式盖的住
* Xcode
    * 安装、升级很慢，下载10分钟，安装2小时
* 快速 Notes 偶尔崩溃
* 开磁盘加密，开机慢一点，登入有进度条
* 右上角图标区域载入慢，有时只有一个输入法图标
    * 更新系统之后，没有了
* 沉，不好拿起；沉，很沉，单手伤
* Wi-Fi 速度只有 866Mbps，降级了 🚨

## 配置经验

通过以下方式，使用苹果的 Accelerate Framework，榨取新硬件带来的性能提升。R 自带了编译好的 vecLib 链接，自行手动指向一下即可。

```bash
cd /Library/Frameworks/R.framework/Resources/lib
ln -sf libRblas.vecLib.dylib libRblas.dylib
```

而对于 Python numpy 则有：

方案一：pip 源码安装，指定编译参数，详情可参考这个 [gist](https://gist.github.com/MarkDana/a9481b8134cf38a556cf23e1e815dafb)。

```bash
conda install cython pybind11
pip install --no-binary :all: --no-use-pep517 numpy
```
然后记得编辑 ~/.condarc，开启

```
 pip_interop_enabled: true
 ```

最后，Homebrew 安装的 mambaforge 每次更新自身，会删除所有安装的包，我目前暂时的做法是，在 `~/.condarc` 内再指定一下用户目录，如下 `pkgs_dirs`

```
channels: [conda-forge]
pip_interop_enabled: true
repodata_threads: 4
pkgs_dirs:
  - ~/.local/python
```

方案二（个人推荐）：指定 conda 安装参数 `libblas=*=*accelerate`，比如我现在用 [micromamba](https://mamba.readthedocs.io/en/latest/installation.html)：

```
micromamba install numpy "libblas=*=*accelerate"
```
