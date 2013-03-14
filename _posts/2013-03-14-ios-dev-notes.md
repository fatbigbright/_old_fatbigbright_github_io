---
layout: post
title: iOS开发笔记
permalink: ios-dev-notes
description: Some Description
date: 2013-03-14 09:40:02 +08:00
tags: "iOS dev"
---

###iOS开发笔记

####本开发笔记将不定时徐徐更新。

1. UITableView的单元格存在图片的情况下，在划动时会存在卡顿的现象。现有的优化方案有将源图改小保存一个复本，单元格显示这个较小的复本，或者使用异步加载，尚需试验确认。