---
layout: post
title: "关于字符编码"
description: ""
category: 
published: false
tags: [博客阅读笔记, 微软, dev]
---
{% include JB/setup %}

关键词：ANSI,ASCII,Unicode,UTF-8,CodePage,C#,Reporting Service,Collision

##前言

这是一篇和字符编码相关性很高却不只限于字符编码这一个狭獈话题的文章。文中内容的参考资料均来自于网络资料及我对资料的理解，资料原址我会在最后列出，如果叙述有错误，还望各位看官多拍砖赐教（其实现阶段本博客的访问量几乎为零 :( ）。

问题的起因源自我在公司修改的一个产品bug，因为一些保密规定，我只能把问题先抽象一下（我发现想把这个问题描述清楚，竟然都是很困难的！）：

我们在平常工作中，在字符编码方面经常会接触到一些名字很类似的词，比如ASCII与ANSI，GB2312和GBK一类。本文将一对一对地介绍一下它们各自有什么关系，又有什么不同。

随着作者对资料的查询和整理理解的过程，本文几易撰写思路。下面的第一部分也从字符编码的历史修改成了字符集与相应标准的关系。在理解字符集之前先来梳理一下这些信息，个人认为是完全有必要的。

##字符编码及相应标准

字符编码及标准的详细历史可以参见由腾讯的程序员撰写的[这篇文章](http://djt.qq.com/article/view/658?ADTAG=email.InnerAD.weekly.20130902&bsh_bid=281085951)。

1. ASCII字符集与ISO/IEC 646标准
   * [ASCII](http://zh.wikipedia.org/wiki/ASCII)：美国信息交换标准代码（**A**merican **S**tandard **C**ode for **I**nformation **I**nterchange）。它是一种7bit位编码方式——实际当中，是用8位即一个字节来存储，最高位b7用作[奇偶校验](http://zh.wikipedia.org/zh-cn/%E5%A5%87%E5%81%B6%E6%A0%A1%E9%AA%8C%E4%BD%8D)——，7个bit位最多可以表示128个字符（2的7次方），它包含了各种非打印控制符、符号、数字及英文大小写字母。也就是说ASCII是128个字符集合的字符编码集。
   * [ISO/IEC 646](http://zh.wikipedia.org/wiki/ISO_646)，简言之，ISO/IEC小组在ASCII的基础上将其标准化，就是ISO/IEC 646标准。   
2. EASCII字符集与ISO/IEC 8859标准
   * [EASCII](http://zh.wikipedia.org/wiki/EASCII)：扩展美国信息交换标准代码（**E**xtended **ASCII**）,它将ASCII的b7这个奇偶校验位包括进来，用以扩展编码集的可表示范围，从而字符容量增加到了256个。其中编号0-127的字符与ASCII相同，128-255字符作为扩充的部分，表示一些表格符号、计算符号、希腊符号及特殊的拉丁符号，用以适应欧洲非英语国家的字符使用需要。也就是说，它是完全兼容ASCII编码的。ISO/IEC小组制定的[ISO/IEC 8859](http://zh.wikipedia.org/zh-cn/ISO/IEC_8859)标准之一[ISO/IEC 8859-1](http://zh.wikipedia.org/wiki/ISO/IEC_8859-1)，从维基百科上来看，是基于EASCII进行标准化的。ISO/IEC 8859是一系列8位西文字符编码标准，它们基于ASCII对不同的西文字符进行了不同的扩展。
   * [ISO/IEC 8859-1](http://zh.wikipedia.org/wiki/ISO/IEC_8859-1)：国际标准化组织制订的8位字符集。它以ASCII为基础，扩展了集合范围，供附加符号的拉丁语言的使用。又称Latin-1或“西欧语言”。
4. GB2312字符集与ISO/IEC 2202标准
   * [GB2312](http://zh.wikipedia.org/wiki/GB_2312)：由中国国家标准总局制订的适应中国大陆地区简体字使用的字符编码。收录了6763个汉字及682个字符（包括拉丁字母、希腊字母、日文平假名及片假名字母、俄语西里尔字母）。GB2312是基于[EUC存储方式](http://zh.wikipedia.org/wiki/EUC)的双字节编码方式，第一个字节为高位字节，第二字节为低位字节。
   * EUC的基础也是出自ISO/IEC，是被称为[ISO/IEC 2202](http://zh.wikipedia.org/wiki/ISO/IEC_2022)的7位编码标准。GB2312的制订正遵循了这一标准。 
5. BIG5字符集与[CNS11643](http://zh.wikipedia.org/wiki/CNS11643)
   * [BIG5](http://zh.wikipedia.org/zh-cn/%E5%A4%A7%E4%BA%94%E7%A2%BC)：港澳台同胞使用的繁体汉字字符编码。也称大五码。Windows的繁体中文版是基于这种编码进行开发的。它包括了13,060个汉字及441个字符。它也是一种双字节编码方式，每个字符的首个字节为高位字节，第二个字节为低位字节。
   * [CNS11643](http://zh.wikipedia.org/wiki/CNS11643):中文标准交换码，2003年，BIG5字符集被追加到了CNS11643的附录中，拥有了较为正式的地位。CNS11543是中华民国/中国台湾地区的国家标准。向国际标准组织提交了申请，但未查到是否申请通过，且国际标准的编码是什么。
6. Unicode与ISO IEC 10646，还有UCS
   * 前面提到的字符集都是适用于某一特定语言的使用需要，但不适用于多语言环境，Unicode的制定则是旨在推出一种适用于所有语言的替代性方案。
   * Unicode是原本由两个组织各自独自制定，后来双方将各自的工作成果合并起来共同推出的标准（一是ISO/IEC小组制定的ISO/IEC 10646标准，一个是Xerox及苹果等软件商组成的统一码联盟制定的Unicode标准）。Unicode经历了多个版本的演变（1991年到2012年，从1.0一直到6.2版），现在可以说实现了最初的设计目标。
   * ISO/IEC 10646其编号代表是ISO/IEC 646标准（ASCII）的扩展。两个组织意识到各自为政，推出两套不同的标准没有必要，所以将它们合并起来。就是现在的Unicode，也就是

##概念的PK

####[ASCII](http://zh.wikipedia.org/wiki/ASCII "ASCII") vs ANSI
首先，它们俩不是同一种东西，前者不是后者的简写。

ASCII是128个字符组成的集合。

而我们经常所说的ANSI字符集，其实严格来讲它并非是字符集的名字。ANSI是[美国国家标准学会](http://zh.wikipedia.org/wiki/%E7%BE%8E%E5%9B%BD%E5%9B%BD%E5%AE%B6%E6%A0%87%E5%87%86%E5%8D%8F%E4%BC%9A)（**A**merican **N**ational **S**tandards **I**nstitute）的简称，这个标准学会还制定了著名的ANSI C标准。好吧，扯远了，翻回头来说ANSI字符集到底是个什么东西。

要说清所谓的ANSI字符集，先要说一说代码页（Code Page）的概念。

[代码页（Code Page）](http://zh.wikipedia.org/wiki/%E4%BB%A3%E7%A0%81%E9%A1%B5)者，其实就是字符编码的别名，也称“内码表”（抄自维基百科）。

####GB2312 vs GBK
以搜狗五笔输入法为例

####Unicode vs UTF-8

##实例
1. GB2312虽然是针对简体汉字的编码需求而制订的，但它包括了682个非汉字字符，比如日文假名。

参考资料：

* [ASCII字符编码](http://zh.wikipedia.org/wiki/ASCII)
* [EASCII字符编码](http://zh.wikipedia.org/wiki/EASCII)
* [GB2312字符编码](http://zh.wikipedia.org/wiki/GB_2312)
* [GBK]()
* [GB18030字符编码](http://zh.wikipedia.org/wiki/GB_18030)
* [代码页](http://zh.wikipedia.org/wiki/%E4%BB%A3%E7%A0%81%E9%A1%B5)
* [字符编码的前世今生——腾讯大讲堂](http://djt.qq.com/article/view/658?ADTAG=email.InnerAD.weekly.20130902&bsh_bid=281085951)