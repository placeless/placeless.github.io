---
layout: post
title: "Running Page"
date: 2024-02-04 16:00 +0800
---

心仪 [@yihong618](https://twitter.com/yihong0618) 的 [Running Page](https://github.com/yihong0618/running_page) 久已，最近终于用上了❤️。优哉游哉跑步好几年，但数据维护基本没做，NRC 退出中国之后，更没导出保存，找出 Endomondo，记录不多，且它的 GPX 奇葩不带时间戳没法用。盘点下来，也就剩 Apple Watch 健康数据还能导出用一用。

![running page](/files/2024/running_page.png)

## 我的使用方案

Running Page 项目支持很多设备和数据平台。我是 Apple Watch 用户，结合我的实际情况，我的选择是 GPX + Strava 方案。理由简单，简单试用对比之后，这个方案能比较顺畅地把历史存量和增量更新糊到一处，供 Running Page 调用。

## Apple Watch 数据

下载开通 Strava 账号之后，即可在设置界面连接手表设备和手机的健康数据。开启自动上传功能，还能获得自动同步能力，无需特意开启 Strava App。设置完成之后，即可勾选导入最近 30 天的数据，也非常方便。

对于手表内的历史记录，则需要导出、处理、再上传。进入 iPhone 健康 App，点击右上角头像，下拉到底即可点击 Export All Health Data。这里面可能包含多种数据，我们只取其中的 workout-routes。

但是光通过这些轨迹路线还分不清是散步、跑步、骑车，需要另行过滤。比如可以通过 [gpx.py](https://github.com/tkrajina/gpxpy) 先对距离过短、距离过长、速度过快进行快速的路线分类，然后借助 [Avenue GPX Viewer](https://github.com/vincentneo/Avenue-GPX-Viewer) 等工具补充目测。清理前👇

![moving page](/files/2024/moving_page.png)

分类完成之后，把结果扔到 Running Page 的 GPX_OUT 目录，然后跟着 [Strava 章节](https://github.com/yihong0618/running_page?tab=readme-ov-file#strava)拿到几个鉴权信息，再然后跟着 [GPX_to_Strava 章节](https://github.com/yihong0618/running_page?tab=readme-ov-file#gpx_to_strava)批量上传即可。
