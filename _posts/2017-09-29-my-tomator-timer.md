---
layout: post
title:  "我的番茄时钟"
date:   2017-09-29 09:06 +0800
---

之前，在 macOS 上使用的番茄时钟，来自网络下载的一个 Alfred Workflow，但是不巧在某次重装系统时丢失了；现在，出于简单即可的心态，所有的 Workflow 都自己写，仅仅是满足自己的需求，轻量又透明。

鉴于我需求简单：一个番茄钟一般在 30 分钟以内，完成后全屏显示一个提示。于是推翻了之前使用系统时间和计数器两个想法，就搞了一个 sleep，它醒了就叫我一下：

```
import time
import sys

T = abs(int(sys.argv[1].split()[0]))

if T <= 60:
  time.sleep(T * 60)
  exit
else:
  exit
```

Workflow 图形如下，后面的 bash 是一行简单的统计脚本，通过 API 往 Google Analytics 发送我的打卡记录，用于将来的自我审视。

![tomator timer](/files/2017/09/29/tomator_timer.png)

举个例子：⌘ + ␣ 快捷键调出 Alfred，输入 `tm 30 read perspective made easy`，上面的 Python 脚本设置一个 30 秒 * 60 的计时器，下方的 Bash 则分解字符串为 `action = ‘read’`，`label = ‘perspective made easy’`，`value = 30`，并将数据推到 Google Analytics 的事件统计报表里，当计时结束，会有一个 `take a break` 的全屏通知和一个小 Banner 形式的 GA response 结果。

```bash
val=$(echo $time | cut -f1 -d' ')
act=$(echo $time | cut -f2 -d' ')
lb=$(echo $time | cut -f3-20 -d' ')
ga=`curl -s -o /dev/null -w "%{http_code}" -X POST -d "v=1&tid=UA-XXXXXXXX-Y&cid=AAA&uid=BBB&t=event&ec=timer&ea=$act&el=$lb&ev=$val" https://www.google-analytics.com/collect`
echo -n $ga
```

根据我的输入，GA 会产出如下的汇总报表给我：

| Event Category | Event Action | Event Lable               | Total Events | Unique Events | Event Value |
| :------------- | :----------- | :------------------------ | :----------: | :-----------: | :---------: |
| timer          | play         | dota                      |      2       |       1       |     46      |
| timer          | read         | the_beginning_of_infinity |      3       |       1       |     138     |
| timer          | read         | perspective made easy     |      1       |       1       |     30      |

