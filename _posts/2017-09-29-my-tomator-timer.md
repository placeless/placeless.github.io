---
layout: post
title:  "我的番茄时钟"
date:   2017-09-29 09:06 +0800
---

之前，在 macOS 上使用的番茄时钟，来自网络下载的一个 Alfred Workflow，但是不巧在某次重装系统时丢失了；现在，出于简单即可的心态，所有的 Workflow 都自己写，仅仅是满足自己的需求，轻量又透明。

鉴于我需求简单：一个番茄钟在 30 分钟以内，完成后全屏显示一个提示。于是推翻了之前使用系统时间和计数器两个想法，就搞了一个 sleep，它醒了就叫我一下：

```
import time

T = abs(int({query}))

if T <= 30:
  time.sleep(T * 60)
  exit
else:
  exit
```

Workflow 后面的 bash 是一行简单的统计脚本，通过 API 往 Google Analytics 发送我的打卡记录，用于将来的自我审视。

![tomator timer](/files/2017/09/29/tomator_timer.png)