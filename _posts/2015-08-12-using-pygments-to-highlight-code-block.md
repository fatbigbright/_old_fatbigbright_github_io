---
layout: post
title: "使用pygments进行博文中的代码块着色"
description: ""
category: 
tags: [jekyll, blog]
---
{% include JB/setup %}

##前言

如[前一篇博文]({% post_url 2015-07-27-whatisnewincsharp60 %})所述，jekyll默认的代码块是没有着色的，所以我们看到的只有单色。其辨识度十分低，我也因此萌生了为博文的代码块加入着色的想法。

##工具——pygments

pygments是基于python的代码着色工具，读者可以访问其[项目主页](http://pygments.org/)及[代码库](https://bitbucket.org/birkenfeld/pygments-main)了解更多信息。这里需要说的是，jekyll拥有对pygments生成的着色css样式表的原生支持，在使用pygments生成相应的样式表后，只需要在jekyll的布局文件中引用这个样式表，并在post中使用`<% highlight 语言名 %>`与`<% endhighlight %>`包含需要进行着色的代码块即可。另外，被pygments这一对关键字包含的代码块不再需要每行行首加四个空格，用以代表代码块了。

##操作实录

1. 进入博客项目目录下，首先使用`virtualenv pyenv`命令创建一个与全局相隔离的python包依赖环境，环境名为pyenv。
2. 修改.gitignore文件，使git命令忽略pyenv下的所有目录、文件（包括子目录及文件）。
3. 使用`source pyenv/bin/activate`激活新创建的依赖环境pyenv。这样一来，所有使用pip安装的python模块将只会在pyenv这个环境下可用。
4. 进入当前所使用的jekyll的主题的样式目录，我`~/git/fatbigbright.github.com/assets/themes/<当前主题>/css`。
5. 使用`pygmentize -S <着色样式> -f html > pygments.css`命令，生成语法着色的样式表。我选择的“<着色样式>”是vim。
6. 修改`_includes/themes/<当前主题>/default.html`，将生成的样式表路径引入到这个文件中。`<link rel="stylesheet" href="{{ ASSET_PATH }}/css/pygments.css">`。

## Tips

启动python交互式命令行，使用如下命令来查看pygments能支持的代码着色样式风格。

{% highlight python %}

>>> from pygments.styles import STYLE_MAP
>>> STYLE_MAP.keys()

{% endhighlight %}
