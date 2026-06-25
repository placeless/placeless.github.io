---
layout: post
title: "解决小米自动喂食器的残废的手动控制问题"
date: 2026-05-02 02:11 +0200
---

自动计划喂食没什么问题，临时加餐很烦。为了绕过米家 App 层层叠叠又不太可靠的手动控制，我做了 [mihome-tools](https://github.com/placeless/mihome-tools)：一组可以从 macOS、Linux 或 iPhone 直接控制喂食器、查询喂食记录的工具。

> 2026-06-25 更新：项目现在包含可安装的 macOS/Linux 命令行工具、登录会话刷新，以及可以直接在 iPhone 上运行的 Scriptable 客户端。

更麻烦的是，米家 App 的手动控制曾经有个 bug：进入手动喂食后，页面列出了 1 份、2 份、3 份等份量选项，点下去却跳回自动计划页面。入口看似存在，实际不能用。

于是我绕过 App，直接调用 Mi Home 云端 API。现在它有两种用法：

- 在 macOS 或 Linux 上安装 `mihome-feed`、`mihome-feed-stats` 和 `mihome-login` 三个命令；
- 在 iPhone 上通过 Scriptable 直接喂食、查看统计、接入快捷指令和桌面 widget。

两种方式都是设备直接请求 Mi Home 云端，不需要常驻服务器。

## 它是怎么工作的

米家 iOS App 和云端服务器之间的通信可以通过 MITM 代理抓包，比如 [mitmproxy](https://www.mitmproxy.org/)。抓一次自己账号的完整喂食请求，可以确认：

- 喂食动作和历史记录使用的请求地址；
- 设备的 `did`、`accessKey`、`SIID` 和 `AIID`；
- Cookie 中的会话信息，如 `ssecurity`、`serviceToken` 和 `userId`；
- 请求体、签名和加密方式。

Mi Home API 使用基于 RC4 和 SHA-256 的签名加密。简化来看，请求过程是：

1. 生成包含随机数和时间戳的 `nonce`。
2. 通过 `ssecurity` 和 `nonce` 派生 `signed_nonce`。
3. 使用 `signed_nonce` 对业务参数签名并进行 RC4 加密。
4. 将 `_nonce`、密文和最终签名一起发送给 Mi Home。
5. 使用同一个 `signed_nonce` 解密响应；部分响应还需要 GZIP 解压。

这些协议细节已经封装在项目里，正常使用不需要自己实现。抓包的主要目的，是取得当前喂食器对应的设备参数。不要公开完整请求、Cookie 或原始响应，它们可能包含可以直接操作设备的凭证。

## 在 macOS 或 Linux 上安装

命令行版本需要 Python 3.10 或更高版本，以及 [uv](https://docs.astral.sh/uv/)。克隆仓库后运行：

```bash
git clone https://github.com/placeless/mihome-tools.git
cd mihome-tools
make install
```

`make install` 会通过 `uv tool` 安装锁定依赖的独立命令：

```text
mihome-feed
mihome-feed-stats
mihome-login
```

如果 shell 找不到命令，先用下面的命令确认 `uv tool` 的可执行文件目录已经加入 `PATH`：

```bash
uv tool dir --bin
```

以后更新只需要：

```bash
git pull --ff-only
make install
```

## 配置设备和账号

默认配置文件是：

```text
~/.config/mihome/feeder.env
```

配置分为两类：

- 设备参数：`did`、`accessKey`、喂食动作的 `SIID` 和 `AIID`，以及所在区域的 API 地址；
- 账号会话：`ssecurity`、`serviceToken`、`userId` 等。

完整模板和字段说明以项目的 [README](https://github.com/placeless/mihome-tools#configuration) 为准。配置文件只接受简单的 `KEY='value'` 或 `export KEY='value'`，会被当作数据解析，而不是作为 shell 脚本执行。

写好设备参数后，限制文件权限：

```bash
chmod 600 ~/.config/mihome/feeder.env
```

第一次使用或会话过期时运行：

```bash
mihome-login
```

它会打开小米账号登录，检查账号是否匹配，通过统计接口验证新会话，然后原子更新配置文件中的会话字段，并为旧文件留下带时间戳的备份。设备专属的 `did`、`accessKey`、`SIID` 和 `AIID` 不会由登录过程自动发现，仍需从自己的设备配置或抓包请求中取得。

## 喂食和查看统计

喂 1 份：

```bash
mihome-feed 1
```

喂 2 份：

```bash
mihome-feed 2
```

项目会在本地检查份量，超过 `MIHOME_FEED_MAX_PORTIONS` 的请求不会发送到云端。这个限制不只是防止手滑，也让 Shortcuts、Alfred 等自动化入口更安全一些。

查看最近七个自然日的统计：

```bash
mihome-feed-stats
```

查询更长时间，并列出每日数据：

```bash
mihome-feed-stats 30 2000 --full
```

输出类似：

```text
today: 3, 15g
yesterday: 5, 25g
feed_week:  31
feed_month: 82

daily:
  2026-06-23: 5
  2026-06-24: 3
```

排查问题时可以加上 `--debug`：

```bash
mihome-feed-stats 30 1000 --debug
```

调试输出会遮盖账号 ID、设备 ID 和 `accessKey`。`--json` 则会输出 Mi Home 的原始数据，其中仍可能包含隐私信息，分享之前需要自己检查。

三个命令也使用稳定的退出状态：成功为 `0`，Mi Home 返回业务失败为 `1`，配置、认证、网络或协议错误为 `2`。因此它们可以直接接入 shell、Alfred 或其他自动化工具。

## 我在 macOS 上的调用方式

我平时不会打开终端手工运行命令，而是用一个很小的 Alfred Workflow 调用 macOS 快捷指令，再由快捷指令执行 mihome-tools：

```text
Alfred Workflow → macOS Shortcuts → mihome-tools
```

这个 Workflow 有两个入口：

- 在 Alfred 输入 `feed <份数>`，例如 `feed 2`，会把份数传给名为 `Feed` 的快捷指令，再执行 `mihome-feed`；
- 输入 `stat`，会运行 `Feed Stats` 快捷指令，调用 `mihome-feed-stats` 并返回今、昨两日的统计。

这样做多绕了一层，却把界面和执行逻辑分开了：Alfred 负责快速输入，Shortcuts 负责组织动作，mihome-tools 只负责访问 Mi Home。以后想再加快捷键、通知或其他自动化步骤，也不需要改 Python 代码。

![Alfred 输入 feed 2，准备放粮 2 份](/files/2026/mihome-tools-alfred.webp)

## 在 iPhone 上直接使用

iPhone 端使用 [Scriptable](https://scriptable.app/)，请求路径很简单：

```text
Scriptable on iPhone → Mi Home cloud API
```

它不需要 Mac、树莓派、Web 服务或家庭网络入站端口。安装步骤是：

1. 从 App Store 安装 Scriptable。
2. 把 [`MiHomeInstaller.js`](https://github.com/placeless/mihome-tools/blob/main/scriptable/MiHomeInstaller.js) 的内容复制到一个同名的 Scriptable 脚本中。
3. 运行 `MiHomeInstaller`，下载其余脚本。
4. 运行 `MiHomeSetup` 完成配置。

<img src="/files/2026/mihome-tools-scriptable.webp" alt="Scriptable 中安装完成的 MiHome 脚本" width="420">

如果已经配置好命令行版本，最省事的迁移方法是复制 `~/.config/mihome/feeder.env`，通过通用剪贴板传到 iPhone，然后在 `MiHomeSetup` 中选择 **Import JSON or feeder.env from Clipboard**。

导入开始后，安装脚本会立即清空剪贴板；连接测试通过后，凭证保存在 Scriptable Keychain 中，而不是写进 JavaScript 文件。

### 喂食和快捷指令

运行 `MiHomeFeed`，选择份数并确认后才会发送请求。iPhone 端同样执行最大份量限制。

<img src="/files/2026/mihome-tools-feed-confirmation.webp" alt="Scriptable 中选择喂食份数" width="420">

从 Apple Shortcuts 或 Siri 调用时，需要传入包含份数和显式确认的字典：

```json
{ "portions": 1, "confirmed": true }
```

这里的 `confirmed: true` 用来防止无人值守或误触发的快捷指令直接喂食。Shortcuts 场景不会再显示 Scriptable 确认弹窗；脚本会返回包含 `ok`、`portions` 和 Mi Home 响应的字典，方便快捷指令继续判断或显示结果。

### 统计和 widget

`MiHomeStats` 显示今天、昨天、最近七个自然日、本月和每日喂食数据。快捷指令也可以传入查询范围：

```json
{ "days": 30, "limit": 1000 }
```

添加 widget 时，iOS 的列表里显示的是 **Scriptable**，而不是单独的脚本名称：

1. 长按主屏幕，点 **+**，添加小号或中号 Scriptable widget。
2. 长按新 widget，选择 **编辑 Widget**。
3. 在 **Script** 中选择 `MiHomeWidget`。

小号 widget 显示今天的份数；中号 widget 还会显示昨天、最近七日和本月的总数。点击 widget 会打开 `MiHomeStats`。在 Apple Shortcuts 中查询统计应运行 `MiHomeStats`，不要运行 `MiHomeWidget`。

<img src="/files/2026/mihome-tools-widget.webp" alt="主屏幕上的 MiHomeWidget 喂食统计" width="420">

iOS 决定 widget 的实际刷新时机，因此它不一定严格按照请求的时间更新。

## 会话过期怎么办

Scriptable 能保存和使用凭证，但不能执行小米账号登录。如果遇到 HTTP 401，或者返回 `code: 3, message: auth error`，需要在电脑上运行：

```bash
mihome-login
```

刷新后，再把新的 `feeder.env` 导入 `MiHomeSetup`。如果没有使用命令行版本，也可以用其他可信的小米登录工具取得新会话，但不要把账号密码或会话交给来历不明的网站和脚本。

## 限制和安全

这是一个非官方社区项目，与小米没有关系。它使用的是可能随时变化的 Mi Home 私有 API，不保证长期稳定。

当前的历史记录解析针对我使用的喂食器：份量位于事件键 `4.2` 的 MIoT 属性 `piid: 4`。其他型号可能使用不同的 action、事件键或输入结构，需要调整配置甚至代码。

最后，能够读取这些凭证的人，通常也能操作对应设备：

- 不要提交或公开 `feeder.env`、抓包文件和 Cookie；
- 不要分享未经检查的 `--json` 输出；
- 不要安装不可信的 Scriptable 脚本；
- iPhone 截图中也要避开账号、设备 ID、API 地址和凭证。

项目的安装模板、故障排查和最新兼容情况都在 [placeless/mihome-tools](https://github.com/placeless/mihome-tools)。
