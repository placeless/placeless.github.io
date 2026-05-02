---
layout: post
title: "解决小米自动喂食器的残废的手动控制问题"
date: 2026-05-02 02:11 +0100
---

自动计划喂食没什么问题，临时加餐很烦。通过米家 App 操作，功能、页面层层叠叠，每个环节响应速度还一般，用几次就烦了，非长久之计。很自然想到用 iOS 快捷指令（Shortcuts）或 widget 控制，结果发现米家 App 还有个 bug：进入手动喂食后，出现份量选择界面——1 份、2 份、3 份……以及开/关等选项，但那些选项根本点不了，UI 渲染出来了，点下去却直接跳到自动计划页面。只有「开」和「关」能点。很久没见修复，想必 KPI 覆盖不到，没人在意。

既然官方 App 入口坏了，绕过去就好——直接调 Mi Home 云端 API。米家 iOS App 和云端服务器的通信可以抓包（MITM 代理，比如 mitmproxy）。抓一次完整的喂食请求，就能看到：

- 请求地址
- 请求体的结构和字段
- Cookie 里携带的认证信息（`ssecurity`、`serviceToken`、`userId` 等）
- 加密方式

Mi Home API 使用基于 RC4 + SHA-256 的签名加密，大致流程：

1. 生成 `nonce`：8 字节随机数 + 当前分钟时间戳（4 字节），Base64 编码。
2. 派生 `signed_nonce`：`SHA-256(ssecurity + nonce)`，Base64 编码。
3. 构造明文参数：业务 payload 序列化为 JSON 作为 `data`，用 `signed_nonce` 签名得到 `rc4_hash__`。
4. RC4 加密：以 `signed_nonce` 为密钥，对 `data` 和 `rc4_hash__` 分别加密（跳过前 1024 字节密钥流）。
5. 最终签名：对加密后的参数再签一次，得到 `signature`。
6. 组装 POST body：`_nonce`、`data`（密文）、`rc4_hash__`（密文）、`signature`。

响应同样是 RC4 加密，用同一个 `signed_nonce` 解密，部分响应还会 GZIP 压缩。

macOS 可用的脚本仓库：[placeless / mihome-tools](https://github.com/placeless/mihome-tools)，包含放饭动作和放饭统计，可以用 Shortcuts、Alfred 或任意工具调用。

至于 iOS，[Scriptable](https://scriptable.app/) 是最方便的出口——支持在 widget 和快捷指令里运行 JavaScript，可以直接发 HTTP 请求。把上面的加密逻辑用 JavaScript 重写一遍即可，或者直接丢给 AI 仿写。关键在于拿到正确的几个字段：`ssecurity`、`serviceToken`、`userId`、`did`、`accessKey`。有了这些，加密和请求的逻辑用什么语言都能跑。
