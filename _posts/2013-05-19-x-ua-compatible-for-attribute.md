---
layout: post
title:  "X-UA-Compatible for Attribute"
---

这网站一搭好之后，就去做了[W3C HTML5验证]，提示了一个之前完全没有意识到的错误：

![X-UA-Compatible-for-attribute](/files/2013/05/19/x-ua-compatible-error.png)

WTF? 原来，随处可见的[X-UA-Compatible]，并非W3C的开放标准。它是微软放出IE8时，丢出来的文档兼容模式（document compatibility），目的是帮助网站更好的兼容各种奇怪的IE56789。究其缘由，还是各IE版本间差异太大，许多IE6友好的网站，如今到了IE8不太能玩，那可咋整？赶紧在HTTP Header里面，告诉新IE：照顾一下老人家！模拟老模式来渲染。
{% highlight html %}
<!-- 模拟IE7 -->
<meta http-equiv="X-UA-Compatible" content="IE=EmulateIE7" />
{% endhighlight %}

由于IE实在太烂了，Google实在看不下去，为了让IE用户能上个舒服网，在09年9月帮忙推出了[Google Chrome Frame]，因此有了如下的`<meta>`标记：
{% highlight html %}
<meta http-equiv="X-UA-Compatible" content="IE=edge,chrome=1">
{% endhighlight %}
Edge模式告诉IE以可用的最高级别模式显示内容，比如IE9不用考虑向后兼容古老的IE6。后面的`Chrome=1`则是激活Google Chrome Frame，科学上网。

最后，这个错误并没有不良影响，Open Standard支持者可以直接移除它，或者，可以使用IE的保留招数[Conditional comment]来曲线救国。
{% highlight html %}
<!--[if ie]>
<meta http-equiv="X-UA-Compatible" content="IE=edge,chrome=1">
<![endif]-->
{% endhighlight %}

<br />

P.S.

> 我这网站用了[Awesome Font Icons]，就是右上角那一排。许多用户可能不显示，或者是一排口口口口。如果是Chrome或者Firefox用户，请检查一下，是否自定了浏览器字体，并且强制使用，不允许网站使用自定字体，导致了FontAwesome显示不正常。

[W3C HTML5验证]: http://validator.w3.org/check?uri=http%3A%2F%2Fplaceless.github.io%2F2013%2F05%2F16%2Fwelcome-to-jekyll.html&charset=%28detect+automatically%29&doctype=Inline&group=0
[X-UA-Compatible]: http://msdn.microsoft.com/en-us/library/cc288325(v=vs.85).aspx
[Google Chrome Frame]: http://www.google.com/chromeframe?hl=zh-CN
[Conditional comment]: http://en.wikipedia.org/wiki/Conditional_comments
[Awesome Font Icons]: http://fortawesome.github.io/Font-Awesome/
