---
layout: post
title:  "shadowsocks-libev & acl on macOS"
date:   2018-11-30 15:21 +0800
---

![Why So Serious](/files/2016/10/25/joker.png)

## 为什么选择 shadosocks-libev

Shadowsocks 有很多变体，但因为买的 VPS 配备了懒人方案，即 CentOS 6 + Shadowsocks 一键安装，所以服务器端一直用它提供的 Python 版，本地的话，移动端用土豆丝，桌面端用小飞机（背后跑的是 libev 版）。

除了一些玄学层面的问题，对于我来说，这个方案还有这么几个缺点：1. Server 端 Python 版版本落后，停更在 2.8.5，很多新特性跟不上；2. 升级不便，Server 端是服务器环境所限，Client 端是小飞机的更新频次以及手动下载的问题；3. 两端变体不一，一个 Python 版，一个 libev，忍不住要统一（闲得）。


## shadosocks-libev 的优点

有 3 个点我最想要/体验：1. 新的加密方案，比如 chacha20-ietf-poly1305；2. simple-obfs 混淆；3. ACL 分流模式。


## 注意部署细节

服务器系统换成了 Ubuntu 18.04，shadowsocks-libev 的安装和更新，直接 apt install，apt update && apt upgrade，现在和将来都非常方便。此外，注意两件事就好了：一是修改 /etc/sysctl.conf 打开 BBR；二是服务器端的 shadowsocks-libev/config.json 以及启动参数。

客户端不用变化，但我想在 macOS 上做一点改变，一是借助 ACL 模式实现 Mail.app 的零配置收发 Gmail；二是通过 homebrew 管理和升级 shadowsocks-libev。所以 macOS 上关闭了小飞机，转而通过 Homebrew 安装、配置、和部署，安装不提，配置和部署可能有两点需要注意：

1. ACL 参考这个项目 [ACL4SSR][ACL4SSR]，注意 libev 目前只能用 [fullgfwlist.acl][fullgfwlist.acl]，不支持广告屏蔽等功能
2. Brew service，我们肯定是要把 libev 作为服务静默启动的，但这个服务得按需修改

以下是在默认的配置上的修改，两点注意，一是 config 文件的位置 /usr/local/etc/shadowsocks-libev.json；而是默认没有附加 `--acl` 参数，没有开启 ACL 模式。

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
  <dict>
    <key>Label</key>
    <string>homebrew.mxcl.shadowsocks-libev</string>
    <key>ProgramArguments</key>
    <array>
      <string>/usr/local/opt/shadowsocks-libev/bin/ss-local</string>
      <string>-c</string>
      <string>/usr/local/etc/shadowsocks-libev.json</string>
      <!— 我的新增👇 —>
      <string>--acl</string>
      <string>/usr/local/etc/shadowsocks-libev.acl</string>
      <!— 🔚 —>
      <string>-u</string>
    </array>
    <key>RunAtLoad</key>
    <true/>
    <key>KeepAlive</key>
    <true/>
  </dict>
</plist>
```

都是分流工具，ACL 相对于 PAC 模式的好处是，不受浏览器限制，不只 HTTP，所以 ACL 模式下 Mail.app 等翻墙收发 Gmail 🤤。这些更改，在正式部署前，可以通过手工指定路径和参数的方式，去做测试和观察：

```
ss-local -c /usr/local/etc/shadowsocks-libev.json --acl /usr/local/etc/shadowsocks-libev.acl -u -v
```

哪些域名，哪些 IP 直连了，哪些代理了，终端会打印出来，一目了然。之后要新增代理域名或 IP，自己修改 `.acl` 文件即可。再一次，目前 libev 好像只支持 `[bypass_all]` + `[proxy_list]` 以及 `[proxy_all]` + `[bypass_list]` 这两种黑/白名单模式，前者是默认全部直连，你自己指定需代理的域名/IP（支持正则表达式🆒），后者刚好相反。看 issue 回覆，作者暂无扩充计划。


## 遗留问题

有两个问题，一是都是手动修改，还不知道 brew update && brew upgrade 之后是否次次都要动手改；二是终端翻墙的问题，还需依赖别的工具，比如小飞机使用的 privoxy，这个再说。


## Reference

- [shadowsocks-libev](https://github.com/shadowsocks/shadowsocks-libev "shadowsocks-libev 项目 GitHub 仓库地址")
- [simple-obfs](https://github.com/shadowsocks/simple-obfs "simple-bfs 项目 GitHub 仓库地址")
- [ACL4SSR/ACL4SSR][ACL4SSR]
- [用 ACL 代替 Pac 进行更底层的智能分流](https://darknode.in/network/acl-geoip-proxy/ "DarkNode 博客的实践经验")

[ACL4SSR]: https://github.com/ACL4SSR/ACL4SSR "ACL 规则 GitHub 仓库地址"
[fullgfwlist.acl]: https://raw.githubusercontent.com/ACL4SSR/ACL4SSR/master/fullgfwlist.acl "原版 SS 能且仅能使用此规则"