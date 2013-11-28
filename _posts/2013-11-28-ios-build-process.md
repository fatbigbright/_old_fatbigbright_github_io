---
layout: post
title: "XCode构建过程"
description: ""
category: 
tags: "博客阅读笔记 iOS"
---
{% include JB/setup %}

首先是[原文链接](http://www.objc.io/issue-6/build-process.html)。本文以一个实际项目为例，介绍了XCode5下Build过程中一些值得讨论的细节。
备忘：   

1. 查看Build Log，View->Navigator->Show Log Navigator，参考[官方说明](https://developer.apple.com/library/mac/recipes/xcode_help-log_navigator/articles/log_navigator-about.html)。
2. 如何自定义Build过程？在如下图所示的位置中查看Build Phase，这里列出了所有的构建步骤。
![Build Phase](http://www.objc.io/images/issue-6/build-phases.png)
3. 项目文件的信息。
   * rootObject的值是项目对象，形如   
      `rootObject = 1793817C17A9421F0078255E /* Project object */;`，   
      查找该对象值 `1793817C17A9421F0078255E = { isa = PBXProject; ...`   
      这就表示主项目的定义。