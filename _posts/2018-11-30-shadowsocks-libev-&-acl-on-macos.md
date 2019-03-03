---
layout: post
title:  "shadowsocks-libev & acl on macOS"
date:   2018-11-30 15:21 +0800
---

![Why So Serious](/files/2016/10/25/joker.png)

## 为什么选择 shadosocks-libev

Shadowsocks 有很多变体，但因为买的 VPS 配备了懒人方案，即 CentOS 6 + Shadowsocks 一键安装，所以服务器端一直用它提供的 Python 版，本地的话，移动端用土豆丝，桌面端用 [ShadowsocksX-NG](https://github.com/shadowsocks/ShadowsocksX-NG)（背后跑的是 libev 版）。

除了一些玄学层面的问题，对于我来说，这个方案还有这么几个缺点：

1. Server 端版本落后，停更在 2.8.5，新特性缺失
2. Client 端 App 更新慢，需手动下载
3. 两端各异，忍不住要统一（闲得）。


## shadosocks-libev 的优点

有 3 个点我最想要/体验：1. 新的加密方案，比如 chacha20-ietf-poly1305；2. simple-obfs 混淆；3. ACL 分流模式。


## 注意部署细节

服务器：系统换成了 Ubuntu，shadowsocks-libev 的安装和更新，直接 apt install，apt update && apt upgrade，现在和将来都非常方便，但 18.04 的缺点是官方源内置的 shadowsocks-libev 还在 3.1.3，略老。Ubuntu 16.04 没有这个问题，但是系统配置工作，比如文件 lock 问题，开 BBR 要换内核等等，又比 18.04 辛苦一些。为了刚发布的 3.2.4，还是选择了 16.04。

此外，注意两件事：一是修改 /etc/sysctl.conf 打开 BBR；二是服务器端的 shadowsocks-libev/config.json 以及相关启动参数。

客户端：想做一点改变，一是借助 ACL 模式实现 Mail.app 的零配置收发 Gmail；二是通过 homebrew 管理和升级 shadowsocks-libev。所以 macOS 上放弃表现不错的 ShadowsocksX-NG，转而通过 Homebrew 去安装、配置、和部署 SS。安装不提，配置和部署可能有两点需要注意：

1. ACL：规则参考这个项目 [ACL4SSR](https://github.com/ACL4SSR/ACL4SSR) 的 [fullgfwlist.acl](https://raw.githubusercontent.com/ACL4SSR/ACL4SSR/master/fullgfwlist.acl)，或自配
2. 插件：[v2ray-plugin](https://github.com/shadowsocks/v2ray-plugin)，替代 [simple-obfs](https://github.com/shadowsocks/simple-obfs)
3. 参数：终端 `--plugin-opts`；Config.json 是下划线 `plugin_opts`

先看配置，个人范例如下：

```
{
  "server_port":xxx,
  "local_port":xxx,
  "password":"xxx",
  "server":"x.x.x.x",
  "fast_open":true,
  "reuse_port":true,
  "no_delay":true,
  "mode":"tcp_only",
  "timeout":300,
  "method":"xx-xx-xxx",
  "acl":"/x/x/etc/shadowsocks-libev.acl",
  "plugin":"/x/xx/v2ray-plugin",
  "plugin_opts":"tls;host=xxx.xx"
}
```

ACL 详情，可阅读这篇文章：[用ACL代替Pac进行更底层的智能分流](https://darknode.in/network/acl-geoip-proxy/)，作者 [DarkNode](https://darknode.in/)

同为分流，ACL 相对于 PAC 的好处是，不受浏览器限制，不只 HTTP，所以 ACL 模式下 Mail.app 等翻墙收发 Gmail 🤤。这些更改，在正式部署前，可以通过手工指定路径和参数的方式，去做测试和观察：

```
ss-local -c /usr/local/etc/shadowsocks-libev.json --acl /usr/local/etc/shadowsocks-libev.acl -v
```

哪些域名，哪些 IP 直连了，哪些代理了，终端会打印出来，一目了然。之后要新增代理域名或 IP，自己修改 `.acl` 文件即可。再一次，目前 libev 好像只支持 `[bypass_all]` + `[proxy_list]` 以及 `[proxy_all]` + `[bypass_list]` 这两种黑/白名单模式，前者是默认全部直连，你自己指定需代理的域名/IP（支持正则表达式🆒），后者刚好相反。看 issue 回覆，作者暂无扩充计划。

采用 fullgfwlist.acl 省心，但它是一张大表，有域名（网址）有 IP，目前不知道域名和 IP 哪一种查询效率更高，满屏的通用规则之中，大多数的网站是我从不访问的，纠结之后，借鉴了 DarkNode 的思路：少数域名 + 海外 IP 的组合。海外 IP 来自 [x1angli/regional-ip-addresses](https://github.com/x1angli/regional-ip-addresses)，当前版本不再直接产出海外 IP 数据集，可以自己 import netaddr 包，做 IP 集合运算，以 IPv4 为例，`IPSet(['0.0.0.0']) ^ ( CNIPv4 | Reserved)`，也可以通过 git checkout 到过去的 `c116403` 来产出 outwall.txt，直接用于 [bypass_list]。

流量的混淆与伪装，此前主要靠 [simple-obfs](https://github.com/shadowsocks/simple-obfs)，几年付出之后，作者似乎放弃了维护，转战 [v2ray-plugin](https://github.com/shadowsocks/v2ray-plugin)，作为前作的替代品，被赋予厚望。这可是真 SSL，并非伪装，所以必需域名和 SSL 证书。域名自己注册，免费付费皆可，证书可以通过 [acme.sh](https://github.com/Neilpang/acme.sh) 工具，从 Let's Encrypt 或者 Cloudflare 等地免费获取。

我的 DNS 解析，原本放在国内的服务商 DNSPOD 处，这回由于获取 SSL 证书需要微调 DNS 记录，才发现 DNSPOD 的登录工具 D 令牌年久失修，我陷入了死循环：登录才能解绑 D 令牌，没有 D 令牌又登录不了，附赠的其他选项也尝试了，都是 placeholder，都是看菜，不解决实际问题。

遇事不决问谷歌，得知还有 CloudFlare 这一选项，迁移过程前后所费不到 10 分钟。修改两个地方，无论是域名注册商 Godaddy 还是 DNS 解析的 CloudFlare，都是简单明了，直击要害，没有废话和误导，完成主要任务之外，更有诸多功能可以开眼，且大部分还能免费体验，要说，还是西方的月亮圆。

DNS 放到 CloudFlare 解析之后，SS server 填你的域名，可以使用它的 CDN 加速，这一点见仁见智，CN2 或者 GIA 用户，我看就没必要了，免费 CDN 高峰期 Qos 有限保障付费用户，而且在我处电信走的 163 出口，全程 202 开头的 IP 段，速度打折。


## 遗留问题

终端翻墙的问题，不少终端工具依赖 HTTP 代理，不支持 Socks5，需要 privoxy 等解决，个人目前不太需要。

手机端不支持 v2ray-plugin，我的办法是在服务器端启动两个 ss-server 进程，端口和口令各异，一个附带 v2ray-plugin，另一个不带，专门给不支持的设备连接。但是很快发现很多不明 IP 在嗅探，参考这个 issue，在服务器端做了一个 ACL 规则 [reject_all]，然后列举了自己常在的 IP 段，以 CIDR 的形式放在 [white_list] 里，勉勉强强先对付着。

v2ray-plugin 配置错误感觉基本都是权限问题，注意优先尝试这个命令：

```
setcap cap_net_bind_service=+ep /PATH/TO/v2ray-plugin
```

折腾过程很耗时间，有因为别人没写清楚，有因为缺少知识，然而，一切的问题，终归还是因为缺少知识。


## Reference

- [用 ACL 代替 Pac 进行更底层的智能分流](https://darknode.in/network/acl-geoip-proxy/ "DarkNode 博客的实践经验")
