---
layout: post
title:  "Sublime Text: 不同类型文档指定不同语法高亮"
redirect_from:
  - 2014/01/14/sublime-text-specific-syntax
---

Sublime Text 不仅内置了众多漂亮的语法高亮主题（Color Scheme），而且还有不少开发者贡献了更加针对某一类语言的主题，比如 [Railscasts] 之于 Rails。但是并非一套主题放到所有类型的文档上都好看，像 [Espresso Soda]，用于 HTML 和 CSS 编辑时，时髦又易用，但放到 Ruby on Rails 上，那就一身痒了，如下图:

![Monokai Soda on Rails](/files/2014/01/14/msor.jpg)

总不能每次打开不同类型的代码文档，再手动切换顺眼的语法高亮吧，要知道大多数时候，一个 Project 下面都包含了多种语言的代码，比如 Rails 下面就有：.rb，.erb，.html，.css，.js 等好几类的文档，显然不可能切换到 .rb 时，开 Railscasts.tmTheme，切至 .html，又去开 Monokai Soda。

还好 Sublime Text 并非只能一次指定一套 Color Scheme，它很贴心的提供了一个 `Syntax Specific－User` 的功能，让开发者可以针对特定类型的语言，指定特定的语法高亮，让 HTML，JavaScript，Python，Ruby 各领风骚，又不影响兄弟感情。

![Monokai Soda on Rails](/files/2014/01/14/syntax_specific.jpg)

打开想要做特殊配置的文档，依上图菜单打开配置文件，做如下配置即可，比如打开一个 Ruby 文件，依次点开菜单：`Preference » Setting - More Syntax » Specific - User`，会自动开启一个叫做 `Ruby on Rails.sublime-settings` 的配置文本，照以下形式，填入对应内容即可。`Packages/User/Railscasts.tmTheme` 是需要自定义的内容，是指存放语法高亮主题的路径和文件名。

```json
{
  "color_scheme": "Packages/User/Railscasts.tmTheme"
}
```

现在 Ruby 代码就顺眼多了，注释颜色我自己改过了，并非 Railscasts 默认的（不好看），怎么自定义语法高亮主题颜色，下次再做记录。

![Monokai Soda on Rails](/files/2014/01/14/rcor.jpg)

[Railscasts]: https://github.com/tdm00/sublime-theme-railscasts
[Espresso Soda]: https://github.com/buymeasoda/soda-theme/#syntax-highlighting-colour-schemes
