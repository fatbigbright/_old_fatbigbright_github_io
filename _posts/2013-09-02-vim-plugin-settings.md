---
layout: post
title: "vim插件及设置"
description: ""
category: 
tags: []
---
{% include JB/setup %}

开始折腾vim，下面把折腾过程中种种算不上心得的点滴记录如下，没什么顺序，纯粹备忘：

1. 安装NERDTree当作文件资源管理器。打开tree的命令为`:NERDTree`，但写得太长，需要精简。在`_vim_sample.vim`中的将这个命令精简映射成`nd`的相应命令为：`nmap nd :NERDTree<cr>`。nmap命令即是用于进行命令映射的，另外，好像只适于用普通模式下的命令映身，imap命令好像是用于插入模式下的快捷键映射。待查。

2. 前端编辑插件emmet-vim的前身就是大名鼎鼎的Zen-Coding.vim，是Zen-Coding的vim插件实现，作者叫Yasuhiro Matsumoto，松本康弘么？跟大神Matz松本行弘有毛线关系么？   
   Windows下无法使用官方提供的`<c-y>,`快键键，手工映射的命令只能做到在普通模式下用`em`来替代`:call emmet#expandAbbr(0,"")<cr>`命令。在Mac OSX下测试，发现`<c-y>,`命令在苹果系统下是正常使用的，Windows果然还是后娘养的？还是说我公司Windows的环境有问题，快捷键跟别的软件发生冲突了？晚上再试一下我的大机。
   
3. 代码缩进。设置`set ts=4`表示每次tab是四个空格宽。`set tw=4`表示ShiftWidth为4，个人理解是指按o或O产生新行时默认进行的缩进。`set expandtab`则是用空格代替tab。
