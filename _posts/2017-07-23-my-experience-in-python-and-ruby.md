---
layout: post
title:  "Python vs Ruby"
date:   2017-07-23 23:27 +0800
---

我使用这两门编程语言的水平有限，只是拿来做一些简单的网络请求、文件处理等，比如信息爬取、数据分析、以及写一写 Alfred Workflow 之类的。

硬说个人偏好，更偏向 Ruby 一些，所根据的，并不是语言特性的设计优劣，这些我不懂，无非是一些个人日常经验而已，主要有两处：

- Python 的分裂
- Ruby API 的优雅

具体的例子，比如写一段调用百度翻译 API 的 Alfred Workflow 脚本，用 Python 首先碰到 urlib 和 requests 的选择。requests 设计的更易用一些，但需另外安装，而 Alfred 只支持 macOS 内置的 Python 2，基于它安装其他的函数库，要稍微麻烦一些，还有权限等语言外的考量。而 Ruby net/http 呢，一是没有像 Python2/3、urllib/2/3 等版本区别， 二是 API 文档更易读。总感觉，Ruby API 设计得更好理解一些，整个作业过程更为流畅，不容易遇到字符集等不太想考虑的问题。在 Ruby 上做点事情，总感觉更容易一些，像 Ruby on Rails 对 Web App 开发所做的那样。

Python 我也很喜欢，Numpy、Pandas、Jupyter 等加持，数据处理分析方面，Ruby 完全比不了，与 R 相比，感觉也更加灵活更加好理解，在这些任务上，使用 Python 也是一种享受。