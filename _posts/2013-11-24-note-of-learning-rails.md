---
layout: post
title: "Rails学习笔记"
description: ""
category: 
tags: []
---
{% include JB/setup %}

1. git add时会发现有许多.keep文件，都是些打开无内容的文件，其实它们是用来保证一些应当加入源码管理的空目录不会被忽略。
2. 在git commit之前取消上一次git add的命令为：git rm -r --cached .  ，要注意代表当前目录的最后的点（.）。
3. public目录下如果有index.html，则在routes.rb中定义的`root to:=> "Action"`不会起作用。系统会强制优先跳转到静态页面index.html。至少在ruby1.9.3-p448 + rails 4.0.1下是这样的行为。
4. 使用`rvm gemset create [gemset_name]`来建立gemset，可以实现rails多版本共存及切换。具体可参考[这篇文章](http://blog.eddie.com.tw/2011/04/08/rvm-and-gemsets/)。其中重要的命令有此例：`rvm gemset 1.9.3@r3212 --default`，这个命令中，r3212是自定义的gemset的名字，在这个gemset下，我安装了rails3.2.12，而这个命令则设定了系统默认使用的gemset，即ruby1.9.3和rails3.2.12。
