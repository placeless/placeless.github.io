---
layout: post
title:  "借助 Measurement Protocol 防 GA 拦截"
date:   2017-10-14 23:48 +0800
---

[上次的问题](/blog/track-single-page-application-with-google-tag-manager.html#article)：由于网络不畅，导致 gtm.js 长时间加载，最终影响整个页面的响应。

![gtm.js 长时间加载](/files/2017/10/13/loading.jpg){:height="74px" width="250px"}

今天尝试去解决它，方案的出发点很简单：既然依托于 Google 的 gtm.js 被污染劣化，那索性不加载额外的 js，把自己当个离线设备，自己采集、整理、发送数据总可以了吧。这个想法的依据就是 GA 提供的：[Measurement Protocol](https://developers.google.com/analytics/devguides/collection/protocol/v1/)。

## 什么是 Measurement Protocol

使用 Google Analytics 做 Web 数据采集，通常的做法是在网页上加载 GA 的采集 SDK，通过这一 SDK 完成一系列自动以及自定义的数据采集需求。这一方案兼顾部署效率以及兼容宽广度，是被推荐的通行办法，沿用多年。直到 2012 年，GA 进步到 Universal Analytics 版本时，引入了 Measurement Protocol，用于实现各种在线离线环境的数据采集和整合，延伸 GA 的应用范畴。

![google analytics platform components](/files/2017/10/14/mp.png)

<figcaption> Google analytics platform components，src: https://developers.google.com/analytics/devguides/platform/</figcaption>

什么是 Measurement Protocol？顾名思义，它是一个度量协议，背靠 GA API，约束了如何传递、以及可以传递哪些数据到 GA 服务器。至于怎么采集这些需要传递的数据，则需要你根据自己的情况，实现自己的需求。注意上图，平时 ga.js、analytics.js 帮助我们实现了数据的自动采集，但如果我们无法加载这些 js，也可以自己把数据按规范采集好，通过 Measurement Protocol 发往 GA 进行处理。

好比说，GA 提供了一条 A 到 B 的物流管道，可以帮你快速运载特定物资从 A 到 B，但物资的清理、打包、装车则需要你自己想办法。要想这个办法，也不难，我们只需继续追问自己如下问题👇：

1. GA 平时采集些什么数据
2. 我自己如何采集这些数据
3. 如何通过 Measurement Protocol 上传数据

## Google Analytics 平时采集些什么数据

这个问题，GA 有详细的帮助和开发文档可供参考，[Measurement Protocol Parameter Reference](https://developers.google.com/analytics/devguides/collection/protocol/v1/parameters) 内更有各个参数的门类梳理，在此，仅以此篇文章的 GA 采集请求为例，梳理一下我自己的需求。

![ga params](/files/2017/10/14/ga_params.png){:width="500px"}

上图 21 个参数，乍一看不少，结合 Measurement Protocol Parameter Reference 稍作梳理，即可发现，关键内容只有 dl、dt、cid 等几项。因为这是最普通的内容页面浏览行为，并不涉及其他的事件交互、交易环节等，且我也只想关注用户来源、浏览内容、以及环境信息等 3 个方面的指标，即：

- 用户识别（什么人）
- 环境信息（什么时间、设备）
- 浏览内容（浏览什么文章）

## 如何采集这些数据

假如我们自己想设计一套 Web 数据采集工具，最初的起点，应该也就是上文这些东西：用 cookie 来做用户标注，环境信息通过 user agent 来获得，浏览内容等通过 DOM API 去读取。

### 1. 用户识别

这里仅讨论匿名 cookie，思路简单：如果还不存在 `cid` cookie，则生成一个；如果已经有了 `cid` 则使用现有的，说明是老访客。对于自定义 Client ID，GA 有现成的规范，需要符合 UUID v4 标准。我也不是专业的技术人员，所以以下代码多来自 stackoverflow。

它做 3 件事：生成 UUID v4 规格的 `cid`，提取现有 cookie 中的 `cid`，有则用之，无则赋值。

```javascript
// https://stackoverflow.com/questions/105034/create-guid-uuid-in-javascript
function uuidv4() {
  return 'xxxxxxxx-xxxx-4xxx-yxxx-xxxxxxxxxxxx'.replace(/[xy]/g, function(c) {
    var r = Math.random() * 16 | 0, v = c == 'x' ? r : (r & 0x3 | 0x8)
    return v.toString(16)
  })
}

// https://stackoverflow.com/questions/10730362/get-cookie-by-name
function getcookie(name) {
  var value = '; ' + document.cookie
  var parts = value.split('; ' + name + '=')
  if (parts.length == 2) { return parts.pop().split(';').shift() }
}

var cid = getcookie('cid') || uuidv4()
if (!document.cookie) { document.cookie = 'cid=' + cid }
```

### 2. 环境属性

这里所说的环境属性，我是指用户使用的什么浏览器、从什么来源过来等信息。这些都是常规客户端信息，要获得也很简单，按照浏览器提供的各项 API 来读取即可。API 文档可参考 [MDN](https://developer.mozilla.org)，专业、详实又丰富。

```javascript
'&dr': document.referrer
'&ul': (navigator.language || navigator.userLanguage)
'&dp': (window.location.pathname + window.location.search)
'&sd': screen.colorDepth
'&sr': (screen.width + 'x' + screen.height)
'&vp': (Math.max(document.documentElement.clientWidth, window.innerWidth || 0) + 'x' + Math.max(document.documentElement.clientHeight, window.innerHeight || 0))
```

### 3. 浏览内容

通上边的环境信息一样，调用相应的 DOM API 即可。如果是 Event Tracking、EC Tracking 等，具体的实现方式可能不同，但基本思路一致。

```javascript
'&dl': document.location
'&dt': document.title
```

## 通过 Measurement Protocol 上传数据

这里可以分为三个部分：

- 数据的整合（Payload）
- 传输方式的实现（接口，GET/POST 的选取，以及如何发起）
- ~~基本条件（User Agent String 以及 IP，这里不用考虑）~~

数据的整合比较直观，按照[文档](https://developers.google.com/analytics/devguides/collection/protocol/v1/reference)来即可，所谓 [Payload Data](https://developers.google.com/analytics/devguides/collection/protocol/v1/reference#payload)，它形如平常做 QA 时见到的 URL，只是字符串的连接。

```
?v=1&_v=j64&a=2079646992&t=pageview&_s=1&dl=http%3A%2F%2Flocalhost%2F2017%2F10%2F14%2Facross-the-firewall-with-measurement-protocol.html&ul=en-us&de=UTF-8&dt=%E5%80%9F%E5%8A%A9%20Measurement%20Protocol%20%E9%98%B2%20GA%20%E6%8B%A6%E6%88%AA&sd=24-bit&sr=1680x1050&vp=1035x257&je=0&_u=IEBAAcABI~&jid=1764491897&gjid=787771514&cid=1155599406.1508063074&tid=UA-101491967-2&_gid=1136739196.1508063074&_r=1&gtm=uab&z=1632362829
```

传输方式的实现，其实就是如何在客户端向 GA 发起 HTTP 请求，并带上上面的数据。[文档](https://developers.google.com/analytics/devguides/collection/protocol/v1/reference)提供了接口地址 `https://www.google-analytics.com/collect`，并给出了可接受的请求方式 POST 和 GET。推荐 POST，因为它能够传输更多数据。

发起 POST 还需要另两个个条件：user_agent_string 和 IP 地址。因为我们正好是从浏览器端发起，user agent 和 IP 就不用管了，只需如何发起 POST 请求即可。如何发起，[Ajax (Wikipedia)](https://en.m.wikipedia.org/wiki/Ajax_(programming))。

全部的范例代码如下。`onreadystatechange`、`readyState` 与 `XMLHttpRequest` 的用法，请参考 [MDN](https://developer.mozilla.org/en-US/docs/Web/Events/readystatechange) 文档。实际范例可打开浏览器开发工具，看本页的网络请求。

```javascript
// https://stackoverflow.com/questions/105034/create-guid-uuid-in-javascript
function uuidv4() {
  return 'xxxxxxxx-xxxx-4xxx-yxxx-xxxxxxxxxxxx'.replace(/[xy]/g, function(c) {
    var r = Math.random() * 16 | 0, v = c == 'x' ? r : (r & 0x3 | 0x8)
    return v.toString(16)
  })
}

// https://stackoverflow.com/questions/10730362/get-cookie-by-name
function getcookie(name) {
  var value = '; ' + document.cookie
  var parts = value.split('; ' + name + '=')
  if (parts.length == 2) { return parts.pop().split(';').shift() }
}

function cachebuster() {
  return Math.ceil(Math.random() * 1000000)
}

function setcookie() {
  var d = new Date()
  d.setTime(d.getTime() + 1000*60*60*24*365*2)
  var expires = 'expires=' + d.toGMTString()
  var cid = getcookie('cid') || uuidv4()
  if (!document.cookie) { document.cookie = 'cid=' + cid + '; ' + expires + '; path=/' }
}

document.onreadystatechange = function() {
  if (document.readyState === 'complete') {
    setcookie()
    var data = encodeURI(
      'v=1&tid=UA-xxxxxxxx-y'
      + '&cid=' + getcookie('cid')
      + '&t=pageview'
      + '&dl=' + document.location
      + '&dr=' + document.referrer
      + '&ul=' + (navigator.language || navigator.userLanguage)
      + '&dp=' + (window.location.pathname + window.location.search)
      + '&dt=' + document.title
      + '&sd=' + screen.colorDepth
      + '&sr=' + (screen.width + 'x' + screen.height)
      + '&vp=' + (Math.max(document.documentElement.clientWidth, window.innerWidth || 0) + 'x' + Math.max(document.documentElement.clientHeight, window.innerHeight || 0))
      + '&z=' + cachebuster()
    )
    var xhr = new XMLHttpRequest()
    xhr.open('POST', 'https://www.google-analytics.com/collect')
    xhr.send(data)
  }
}
```

## 总结

以上，只是 Measurement Protocol 的一个最为简单的范例，它的可玩性非常高。几乎可以在任何环境去按照规范构造数据，然后通过 Measurement Protocol 推送到 GA，进行数据整合，抛开对官方 SDK 的下载依赖，完全剥离了 GA/GTM SDK 与用户端页面的加载关系。

而 GA 的接收接口，这几年一直有通过监控宝进行可用性的监测，它被 GFW 拦截的时候并不多，而且有自己再做一层代理的玩法来进一步规避。

对于更复杂的数据采集需求，接下来打算细读一下 Segment 的开发文档，学习他们抽象出来的数据结构，学习他们的连接能力。
