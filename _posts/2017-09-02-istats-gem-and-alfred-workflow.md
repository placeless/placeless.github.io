---
layout: post
title:  "无需 App，即时了解 Mac 温度"
date:   2017-09-02 06:35 +0800
---

MacBook Pro 的散热，见仁见智。但面板烫手时，还是想看看 CPU、电池的具体温度的，一是好奇，二是持续高温有损电池寿命。

说无需 App，其实还是用到一个 Ruby gem 的，叫做 [iStats](http://chris911.github.io/iStats/)。注意，跟收费 App -- iStat Menus 是不一样的。

安装简单（系统自带 Ruby 可能需要 sudo）

```shell
gem install iStats
```

 使用简单，终端直接运行 `istats` 即可。

![iStats in iTerm2](/files/2017/09/02/istats.png)

终端不常驻的话，还可以写一个简单的 Alfred Workflow，通过 Alfred 即时查看。

![iStats in Alfred](/files/2017/09/02/istats_in_alfred.png)

新建一个无需参数的 Workflow，语言为 Bash，脚本如下。这里只是 grep 出了 CPU 和电池两项，拆分输出成两行（如上图）。

```shell
temp=`/usr/local/bin/istats | grep -E '(CPU|Battery)\ temp'`
cpu=$(echo $temp | cut -f1 -d\°)"°C"
bat="B"$(echo $temp | cut -f2 -d\B)

cat << EOB
{"items": [
    {"title": "$cpu"},
	{"title": "$bat"}
]}
EOB
```
