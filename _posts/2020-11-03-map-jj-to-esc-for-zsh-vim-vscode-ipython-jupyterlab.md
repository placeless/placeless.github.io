---
layout: post
title:  "Map jj to Esc"
date:   2020-11-03 00:53 +0800
---

带 Touch Bar 的老款 MacBook Pro，它的 <kbd>Esc</kbd> 是不可能习惯的了了，对于喜欢 vi-mode 的用户来说，解决这个问题，有两个办法：一是使用 <kbd>⌃</kbd> + <kbd>[</kbd>；二将其他按键映射成为 <kbd>Esc</kdb>。这里从个人习惯出发，整理一下第二种方式，覆盖了这些 vi-mode：ZSH、VIM、VS Code、iPython、以及 Jupyterlab。

## ZSH

开启和设定 vi-mode 比较简单，在 .zshrc 当中增加如下内容即可。注意后面的 timeout 不要设置为0。

```
bindkey -v
bindkey -M viins jj vi-cmd-mode
KEYTIMEOUT=25
```

麻烦的是配置响应 vim 模式的光标样式。我的日常情况是这样：Alacritty 终端默认启动一个 Tmux，运行 Zsh。一直以来，Esc 和光标，总有小问题，比如 Esc 突然响应不及时、卡死，光标突然不跟随模式而改变形状了。一番折腾下来，目前最推荐[这个写法](http://micahelliott.com/posts/2015-07-20-vim-zsh-tmux-cursor.html)，使用时间不长，但暂时还没发现什么问题。

## VIM

我用的 Neovim，配置文件名跟 VIM 不一样，但写法是一样的。第二行的目的，是在两个 <kbd>j</kbd> 之间设定一个较短的等待时间，尽量缓解 jj 是正常输入而不是模式切换时的停顿感。

```
inoremap jj <Esc>
:autocmd InsertEnter * set timeoutlen=200
:autocmd InsertLeave * set timeoutlen=1000
```

## VS Code

偏好设置当中搜索或直接在 settings.json 中加入以下内容即可。

```
"vim.insertModeKeyBindings": [
     {
         "before": ["j", "j"],
         "after": ["<esc>"]
     }
]
```

这里还有一点问题，中文输入模式下按下这两个键，会有混乱情况出现。比如，可以拿小鹤双拼输入 jpjt（解决）试试看。

## iPython

创建或修改 ~/.ipython/profile_default/startup/keybindings.py

```
from IPython import get_ipython
from prompt_toolkit.enums import DEFAULT_BUFFER
from prompt_toolkit.filters import HasFocus, ViInsertMode
from prompt_toolkit.key_binding.vi_state import InputMode

ip = get_ipython()

def switch_to_navigation_mode(event):
   vi_state = event.cli.vi_state
   vi_state.input_mode = InputMode.NAVIGATION

if getattr(ip, 'pt_app', None):
   registry = ip.pt_app.key_bindings
   registry.add_binding(u'j',u'j',
                        filter=(HasFocus(DEFAULT_BUFFER)
                                 & ViInsertMode()))(switch_to_navigation_mode)
```

## Jupyterlab

可以使用这个插件 [jupyterlab-vimrc](https://github.com/ianhi/jupyterlab-vimrc) 来自定义需要的键位。比如，在偏好设置的 vimrc 内添加以下内容即可模拟 jj 到 Esc。

```
"imap": 
  [
      ["jj", "<Esc>"],
  ]
```
