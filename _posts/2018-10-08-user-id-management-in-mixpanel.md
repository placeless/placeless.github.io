---
layout: post
title:  Mixpanel 的用户 ID 管理
date:   2018-08-17 15:38 +0800
---

针对自有账户体系的平台，成熟的数据采集工具一般都提供了登录前后以及跨设备的 ID 关联功能，今天，想具体了解一下 Mixpanel 的方案。

## Distinct ID 及其形式

Distinct ID 是 Mixpanel 用于标记和识别用户的机制。在 Web 端，Mixpanel 的用户 ID 是以 `distinct_id` 字段形式存储在 Cookie 内的。在移动端则分为两个情况，如果是 iOS，且支持 AdSupport.framework 的话，Mixpanel 使用 IDFA 作为 Distinct ID，不支持的话，使用 IDFV；如果是 Android，则采用由其 SDK 统一生成 UUID。

Distinct ID 是一个几十位的字符串，有长有短，但无论是哪个端的哪一种情况，其大致的样子，如下：

```
1664f48772c4b2-05d9706e860b83-346c780e-1aeaa0-1664f48772dae3
```

上面所说的，是全由 Mixpanel 托管的情况，但对于自有账户体系的平台来说，用户 ID 可能是一些别的状态，比如 Email 或是 QQ 等数字代号等，当然也是可以直接用来作为 Mixpanel 的 Distinct ID 的。

## Distinct ID 的生命周期

如果是 Web 端 Cookie 形式，则兼具 Cookie 的特征和限制，无须多言；若是 iOS 端，IDFA 是 OS 级别的 ID，除非用户主动重置或是重装系统，否则不会发生变化（如果用户打开了 Limit Ad Tracking，IDFA 会是一串 0）；若是 Android 端，UUID 跟 iOS 的 IDFV 类似，会随 App 的卸载重装而发成变化。

## 用户标识的管理

Mixpanel 的用户标识管理，注意 2 个方法就行了，一是 `mixpanel.alias()`，通常用在注册时，一个用户一生只调用一次；一是 `mixpanel.identify()`，通常用在用户的每次登录时。前者相当于 `distinct_id` 的别名，即 A 又名 B，A 与 B 为一对一的映射关系，但 Distinct ID 还是 A；后者则相当于重命名，即 A 被重新命名为 B，A 是一个旧的死去的 `distinct_id`，B 是新的与 A 无关的 `distinct_id` 。

这么设计的理由是：Alias 用来保障注册等关键 Funnel 的监测，Identify 用来照顾既有用户跨设备的情况。因为，用户注册/登录之前，其行为数据都是关联在一个以随机的匿名 ID 上的，只有当他/她注册登录时，才会留下具体的 Email/QQ 等更有实际意义的个人账号。若要把登录之前无意义的随机匿名 ID 和之后的 Email 关联到一起，并且不丢失匿名状态时的行为数据，不让 Funnel 断裂，就需要一个中间表，去记录这一映射关系，比如：

| alias                 | distinct_id                |
| --------------------- | -------------------------- |
| hello.world@gmail.com | 1664f48772c4b2-05d9706e... |

这张映射表是存储在 Mixpanel 的数据中心的，添加记录的方法，就是在调用 `mixpanel.track('Signup')` 时，同时也触发一个 `mixpanel.alias('hello.world@gmail.com')` 事件。这样我们就知道 1664f4… 转化成了注册用户 hello.world。

可见 Alias 是针对新用户设计的，如果是既有用户呢？就要请出 Identify 了。每当既有用户登录时，比如 hello.world@gmail.com，我们直接调用 `mixpanel.identify('hello.world@gmail.com')`，把它写到当前的 Distinct ID 里即可，这样，接下来的浏览行为，就不再归属于某个随机的临时用户，而是 hello.world。

## 范例解析

![Mixpanel Live View](/files/2018/10/08/mixpanel_live_view.png)

上图是一段 Mixpanel 的报告截图，展示了同一个用户，从匿名游客到注册转化，再换设备登录的一段经历。

从最后一行往上看，尾号为 dae3 的用户，在 10 分钟前，使用 Chrome 到访网站，并且很快留资注册（Alias）为 hello.world@gmail.com，Distinct ID 依然保持为 dae3。

第二行，9 分钟前，他/她更换设备，使用 Safari 继续浏览，注意，刚切换到 Safari 时，由于新设备没有旧 Cookie，用户也尚未登录，所以 Mixpanel Web SDK 生成了一个尾号为 99a2d 的随机 ID，短暂认作一个新游客。

第一行，8 分钟前，用户使用 hello.world 账号登录之后，采集的数据发送至 Mixpanel，系统查询映射表，得知 hello.world 即为之前的 dae3，因此系统将数据记录到 dae3 头上，不再更新 99a2d 的记录。

## Mixpanel 没有解决的问题

至此，用户行为被跨设备关联了起来，第 4 行至第 3 行，匿名到留资注册的关键转化过程，也没有丢失。唯一不够完美的地方，是用户在新设备上初次登录之前的一小段数据，被记录在了一个临时匿名 ID 上。好在这一点东西，看起来并不那么关键。

Google Analytics 由于是有 Session 实体的，可以通过 [Session Unification](https://support.google.com/analytics/answer/4574780?hl=en) 功能来实现同一 Session 内，登录前后、有无 User ID 两段数据的关联。甚至，如果激进一点罔顾 GA Policy 的话，还可以在用户注册/登录时，把 User ID 写到 Cookie/LocalStorage 里面，便于你在用户的非登录状态去管理 Client ID 于 User ID 的关联。