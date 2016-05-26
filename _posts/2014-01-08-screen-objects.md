---
layout: post
title:  "几个有意思的屏幕尺寸对象属性"
tags:
  - JavaScirpt
---

下面例举的这些对象及属性，目前并没有一个公开的标准，但是大家基本上都支持，称呼上，各家可能稍有不同。

### 屏幕对象属性

Property | Description
:------- | :----------
availHeight | 返回屏幕高度（Windows 任务栏，Mac & Linux 菜单栏以及 Dock 除外）
availWidth  | 返回屏幕宽度（Windows 任务栏，Mac & Linux 菜单栏以及 Dock 除外）
height  | 返回屏幕实际高度
width | 返回屏幕实际宽度

### 窗口对象属性

Property | Description
:------- | :----------
innerHeight | 设置或者返回浏览器窗口内容显示区域高度
innerWidth  | 设置或者返回浏览器窗口内容显示区域高度
outerHeight  | 设置或者返回浏览器窗口高度，包括工具栏，地址栏等
outerWidth | 设置或者返回浏览器窗口宽度，包括工具栏，地址栏等

更多好玩的属性，可以看这里：[屏幕对象]，[窗口对象]

### 使用方法
很简单，在各浏览器的调试终端（Firefox 快捷键 ⇧ + ⌘ + J）内输入`screen.height`等代码，即可返回结果，Firefox 甚至还有贴心的补全提示。

[屏幕对象]: http://www.w3schools.com/jsref/obj_screen.asp
[窗口对象]: http://www.w3schools.com/jsref/obj_window.asp
