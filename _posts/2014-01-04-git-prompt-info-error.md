---
layout: post
title:  "zsh: git_prompt_info 错误"
tags:
  - zsh
  - 笔记
---

最近使用 oh-my-zsh 终端 cd 到 git 本地仓库时, 总出现两行错误提示, 碍眼的很.

```bash
git_prompt_info:2: command not found: current_branch
git_prompt_info:2: command not found: current_branch
```

原因是 oh-my-zsh 升级之后, git_prompt_info 函数更改了, 而我自定主题文件则还是用的老代码, 因此只要找到新的 git_prompt_info 函数的位置:

```bash
~/.oh-my-zsh/lib/git.zsh
```

将其复制替换主题文件内的 git_prompt_info 即可解决问题, 重新实现 git 分支的识别.
![zsh-theme](/files/2014/01/04/zsh-theme.png)
