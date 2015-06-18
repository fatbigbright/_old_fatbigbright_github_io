---
layout: post
title: "一步步实现俄罗斯方块之一"
description: ""
category: 
tags: []
---
{% include JB/setup %}

* 在窗体中放置一个画布（Canvas）
* 为画布增加边框，是要在XAML文件的Canvas节点的上级增加Border节点。
* 对于增加边框要注意以下几点：
   1. Canvas作为Border的下一级节点，Margin值应都为0。
   2. Border要设置HorizonAlignment和VerticalAlignment，本程序中分别设置为了left和top。否则会发现实际运行效果时，明明Border的长宽已和Canvas相适应，但右边与下边仍被Canvas所覆盖。
   3. Border的长宽是包含了其BorderThickness值的，也就是说，对于200×440的内层Canvas，它的长宽应定为204×444。

程序部分反而没什么可说的了。以下是今天的完成效果。
![tetris20150618]({{ BASE_PATH }}/assets/images/tetris20150618.jpg)