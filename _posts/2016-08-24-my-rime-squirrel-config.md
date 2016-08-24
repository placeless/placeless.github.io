---
layout: post
title:  "我的鼠须管配置"
date:   2016-08-24 18:34 +0800
---

鼠须管是我的常用输入法，时不时的，都要翻到配置文件夹，做一番折腾，过程消耗时间，但有很多乐趣。

## 1. 版本

Rime 鼠须管已经很久没有更新版本了，当前的最新版停留在 2014 年 12 月 23 日的 0.9.26.2，实际在我的 macOS El Capitan 使用当中，并没有遇到什么可见 bug，真是一个久经考验的输入法。

但从 [Github Commits](https://github.com/rime/squirrel/commits/develop) 上看，其实 lotem 一直有在对鼠须管以及其核心组件 brise 和 librime 做持续的改进和调整。举一个看得见的小例子：想要配置鼠须管的外观参数，实现 rime.im 网站下图的效果，老版本就做不到，第一候选的高亮颜色不能像下图一样，覆盖一整块，而只能盖到候选字，却无法和背景以及边框融合到一起。

<img src="http://rime.im/images/home-feature-4.svg" width="250">

<figcaption>新的外观改进</figcaption>

<img src="http://ifanr-cdn.b0.upaiyun.com/wp-content/uploads/2012/09/jianfan.png" width="300">

<figcaption>老版外观</figcaption>

所以，为了用上新的特性，我使用的版本，是根据[编译说明](https://github.com/rime/squirrel/blob/develop/INSTALL.md)自己编译的，过程很简单：

```reStructuredText
1. 安装并启动 XCode，确认一下条款 (光有 Command Line Tool 是不行的)
2. brew install cmake git boost
3. git clone --recursive https://github.com/rime/squirrel.git
4. make deps
5. make
6. sudo make install
```

如果想要把编译好的 APP 留到下一次重装系统之后，全新安装使用，也不难，只需两步，根据 Makefile 和 postflight 文件可以推导出来。

```reStructuredText
# 1. 把编译好的 Squirrel.app 复制到 /Library/Input Methods目录下：
cp -R YOUR_PATH/Squirrel.app "/Library/Input Methods"

# 2. 然后跑一下 postflight 脚本即可：
sudo /Library/Input\ Methods/Squirrel.app/Contents/Resources/postflight
```



## 2. 配置

只自定义了 5 个文件，没有涉及外部词库、emoji 等内容。

```reStructuredText
- installation.yaml  # 同步和备份
- default.custom.yaml  # 候选词数、快捷键、及输入方案
- double_pinyin_flypy.custom.yaml  # 小鹤双拼
- luna_pinyin_simp.custom.yaml  # 朙月拼音，全拼
- squirrel.custom.yaml  # 鼠须管外观、ascii 模式
```

因为，我只想让它更符合自己的偏好。词库可以自己经年累月积累，那些海量的诗词、电影、音乐、计算机等等外部扩展词库，我其实用不到里面的几十个词，如无必要，勿增实体。

### 2.1 installation.yaml

只增加了两行：自定义一个备份 ID，并且设 iCloud Driver 下的`/rime`目录为备份目的地。

```yaml
installation_id: "jed"  # 同步和备份 ID
sync_dir: "/Users/Jed/Library/Mobile Documents/com~apple~CloudDocs/Sync/rime"  # 同步和备份目录，我直接放到 iCloud Driver
```

### 2.2 default.custom.yaml

做了一些极简的个人配置，切勿照搬。

```yaml
patch:
  menu/page_size: 3 # 候选字只显示 3 个

  switcher/hotkeys:
    - "Control+Shift+grave" # 输入法功能键只保留一个

  schema_list:
    # - schema: luna_pinyin_simp # 朙月拼音
    - schema: double_pinyin_flypy # 只保留小鹤双拼
  
  # 改 caps lock 大小写锁定键为 中/英 切换键
  ascii_composer/good_old_caps_lock: false
  ascii_composer/switch_key:
    Caps_Lock: commit_code
    Shift_L: noop
    Shift_R: noop
    Control_L: noop
    Control_R: noop

  # 只保留 tab 和 加减号，作为翻页键
  key_binder/bindings:
    - {accept: ISO_Left_Tab, send: Page_Up, when: composing}
    - {accept: "Shift+Tab", send: Page_Up, when: composing}
    - {accept: Tab, send: Page_Down, when: composing}
    - {accept: minus, send: Page_Up, when: has_menu}
    - {accept: equal, send: Page_Down, when: has_menu}
```

### 2.3 double_pinyin_flypy.custom.yaml

```yaml
# double_pinyin_flypy.custom.yaml, 只对小鹤双拼生效
patch:
  translator/preedit_format: {}     # 输入双拼码的时候不转化为全拼码
  recognizer/patterns/reverse_lookup: # 关闭 ` 键反查功能

  # 启用罕见字過濾，避免字库不全时，候选字出现「口/？」字符号
  engine/filters:
    - simplifier
    - uniquifier
    - cjk_minifier
  engine/translator/enable_charset_filter: true

  switches:
    - name: ascii_mode
      reset: 0
      states: ["中文", "西文"]
    - name: full_shape
      states: ["半角", "全角"]
    - name: simplification
      reset: 1  # 增加這一行：默認啓用「繁→簡」轉換。
      states: ["漢字", "汉字"]
    - name: ascii_punct
      states: ["。，", "．，"]
```

### 2.4 luna_pinyin_simp.custom.yaml 

默认西文输出，并过滤罕见字。

```yaml
patch:
  switches/@0/reset: 0

  # 启用罕见字過濾
  engine/filters:
    - simplifier
    - uniquifier
    - cjk_minifier
  engine/translator/enable_charset_filter: true
```

### 2.5 squirrel.custom.yaml 

自己做了横排和竖排两个定制外观，示例如下图，左边是方案一，右边为方案二，范例文字是猫输入的。调整`style/color_scheme: placeless`，切换外观。

<img src="/files/2016/08/24/squirrel-appearance.png" width="500">

yaml 文件如下，自定义配色时注意：順序是**藍綠紅0xBBGGRR**。

```yaml
patch:
  # 通知栏显示方式以及 ascii_mode 应用，与外观无关
  show_notifications_via_notification_center: true
  app_options/com.apple.dt.Xcode:
    ascii_mode: true
  app_options/com.runningwithcrayons.Alfred-3:
    ascii_mode: true
  app_options/com.apple.Terminal:
    ascii_mode: true
  app_options/com.googlecode.iterm2:
    ascii_mode: true
  app_options/com.apple.finder:
    ascii_mode: true
  app_options/com.sublimetext.3:
    ascii_mode: true
  app_options/com.github.atom:
    ascii_mode: true
  app_options/com.apple.appstore:
    ascii_mode: true
  app_options/com.apple.calculator:
    ascii_mode: true
  app_options/com.apple.iBooksX:
    ascii_mode: true
  app_options/com.apple.iTunes:
    ascii_mode: true
  app_options/com.apple.launchpad.launcher:
    ascii_mode: true
  app_options/com.apple.systempreferences:
    ascii_mode: true
  app_options/com.apple.keychainaccess:
    ascii_mode: true
  app_options/com.apple.Safari:
    ascii_mode: true

  us_keyboard_layout: true

  # 外观配置
  style/color_scheme: placeless # 选择配色方案

  # 方案一
  "preset_color_schemes/placeless":
    color_scheme: placeless
    name: "秋田／Placeless"
    author: "jed <placeless@outlook.com>"
    horizontal: false # 竖排
    corner_radius: 6 # 窗口圆角半径
    border_height: 0 # 窗口边界高度，大于圆角半径才有效果
    border_width: 12 # 窗口边界宽度，大于圆角半径才有效果
    font_face: "Lantinghei SC"
    font_point: 15 # 字号
    label_font_point: 12
    back_color: 0xFFFFFF #背景
    text_color: 0xFFFFFF # 編碼行文字顏色，24位色值，用十六進制書寫方便些，順序是藍綠紅0xBBGGRR
    candidate_text_color: 0x000000 #非第一候选项
    hilited_candidate_back_color: 0xf57c75 #第一候选项背景
    hilited_candidate_text_color: 0xFFFFFF #第一候选项
    line_spacing: 5

  # 方案二
  "preset_color_schemes/placeless2":
    color_scheme: placeless2
    name: "荷田／Placeless"
    author: "jed <placeless@outlook.com>"
    horizontal: true # 横排
    corner_radius: 6 # 窗口圆角半径
    border_height: 0 # 窗口边界高度，大于圆角半径才有效果
    border_width: 10 # 窗口边界宽度，大于圆角半径才有效果
    font_face: "Lantinghei SC"
    font_point: 15 # 字号
    label_font_point: 12
    back_color: 0xFFFFFF #背景
    text_color: 0x000000 # 編碼行文字顏色，24位色值，用十六進制書寫方便些，順序是藍綠紅0xBBGGRR
    candidate_text_color: 0x666666 #非第一候选项
    hilited_candidate_back_color: 0xFFFFFF #第一候选项背景
    hilited_candidate_text_color: 0xf57c75 #第一候选项
```

## 3. 黑魔法

在 macOS 10.9 以前，是可以去掉系统默认输入法，只保留一个常用的，比如「鼠须管」，中英文一个键切换。方法就是额外勾选一个比如「越南输入法」，这时候，美式英语的可删除状态就不再是不可点击的灰色，而是可以直接删除的黑色。

但是这个方法在之后的 macOS 不再可用了，后来在一个远古国外 BBS 上发现一个古老的方法，一直适用到现在，就是稍微有一点麻烦，需要需改系统文件，弄得不好，可能会有些小 bug。

一共四个步骤：

1. 备份 `~/Library/Preferences/com.apple.HIToolbox.plist`
2. 终端运行`plutil -convert xml1 ~/Library/Preferences/com.apple.HIToolbox.plist`
3. 用 vim、sublime text 或者其他编辑器，打开`com.apple.HIToolbox.plist`，删除掉`AppleEnabledInputSources`键下不需要的输入法`dict`
4. 重启


注意：在`squirrel.custom.yaml`内搭配好需要开启`ascii_mode`的应用程序，避免在密码输入框等场景下，中文状态不好上屏，西文状态又是大写的尴尬，尤其是对于跟我一样喜欢改 Shift 而用 CapsLock 键切状态的用户，可参考上文 2.2 和 2.5 章节的内容。