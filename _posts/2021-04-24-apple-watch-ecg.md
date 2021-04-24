---
layout: post
title:  "国行 Apple Watch ECG"
date:   2021-04-24 21:00 +0800
---

不考虑 iCloud 污染和凿壁借光，国行 Apple Watch 开启 ECG，我主要参考了三个说法：[1](https://gist.github.com/x43x61x69/0a9dd6e134c5c4a7ce39c9aab5639727)、[2](https://hiraku.tw/2019/10/4911/)、[3](https://www.bilibili.com/read/cv6486549/)。同属一个派别，即备份修改还原法。在当前的官方新版系统 iOS 14.4 和 watchOS 7.3.3 正式版的背景之下，均以失败告终。

此时做了以下动作：

1. 取消了手机手表的配对
2. 通过 iMazing 备份了手机
3. 导入了 V2 的 `...heart-rhythm.plist` 文件
4. 通过 Finder 恢复了修改过的备份
5. 重新配对了手表

而且，这个过程反复尝试过了好几遍，ECG都没有正常工作，在手表上按 ECG App 提示请先在手机 Health App 上做配置，而一旦进入 Health App 配置又会遭遇「不支持你所在的地区」。

阅读了 [1] 的后续评论之后，尝试了以下动作：

1. 长按，删除了手表上的 ECG App
2. 安装描述文件，加入了苹果 Public Beta 计划
3. 更新了 iOS 到 14.6 PB，watchOS 到 7.5 PB
4. 禁用了位置服务和 Find My 功能
5. 取消手机手表配对
6. 重新配对为新手表
7. 配置过程这的位置等选项，都选择稍后配置，没有立即开启

引入的变量较多，无法确定其中的步骤 1、4 和 6 是不是必要的，总之，Public Beta 很关键。等待配置加载完成，按下手表上的 ECG App，不再有配置和地区提示，直接进入使用说明，跳过之后，进入红心飘荡，功能正常，Yeah！。
