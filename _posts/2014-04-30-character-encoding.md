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

##导读

我们在进行软件开发的过程中都遇到过字符乱码问题。引起乱码的原因不外乎是编码所用的字符集与解码所用的字符集不一致，或者解码时错将原本的前一字符的低位字节与后一字符的高位字节当作一个字符进行解码。然而问题的出现，往往还伴随着具体的操作系统中众多易令人混淆的概念的身影，甚至是不同的软件，都可能对同一概念有不同的所指，于是我们总是在这些绕也绕不开的坑中艰难跋涉。

为此，作者通过对资料的查询，试图对诸多的概念做一下记录和梳理，始有此文。文中内容的参考资料均来自于网络资料及我对资料的理解，资料原址我会在最后列出，如果叙述有错误，还望各位读者多拍砖赐教——当然，如果有读者的话（其实现阶段本博客的访问量几乎为零 :( ）。

随着作者对资料的查询和整理理解的过程，本文几易撰写思路。下面的第一部分也从字符编码的历史修改成了字符集与相应标准的关系。在理解字符集之前先来梳理一下这些信息，个人认为是完全有必要的。

我们在平常工作中，在字符编码方面经常会接触到一些名字很类似的词，比如ASCII与ANSI，GB2312和GBK一类。本文在回顾字符编码及相应标准之后将一对一对地介绍一下它们各自有什么关系，又有什么不同。

##字符编码及相应标准

为免本部分的内容过于庞杂，这里先列出要点的脱水版，以方便理解为主：

* ASCII是7位编码集。可表示128个字符。
* 使用西文的国家，以ASCII为基础，推出过一系列的互不兼容的扩展了ASCII编码范围的8位字符编码集及标准。
* GB2312是解决了汉字使用的双字节编码标准。同时也兼容了ASCII。
* Unicode的制定，旨在一统天下所有的字符。
* Unicode编码系统包括编码方式和实现方式，编码方式规定了一个字符在Unicode当中的唯一编号，而实现方式则是指这个字符到底在计算机中以什么方式进行存储。UTF，即Unicode转换格式，是Unicode的实现方式。
* UTF-8是为互联网使用最为广泛的Unicode实现，在它的实现中，字符是变长的。 

下面来介绍一下出镜率很高的字符集与相应的标准：

1. ASCII字符集与ISO/IEC 646标准
   * [ASCII](http://zh.wikipedia.org/wiki/ASCII)：美国信息交换标准代码（**A**merican **S**tandard **C**ode for **I**nformation **I**nterchange）。它是一种7bit位编码方式，7个bit位最多可以表示128个字符（2的7次方），它包含了各种非打印控制符、符号、数字及英文大小写字母。也就是说ASCII是128个字符集合的字符编码集。
   * [ISO/IEC 646](http://zh.wikipedia.org/wiki/ISO_646)，ISO/IEC小组在ASCII的基础上将其标准化，就是ISO/IEC 646标准。   
2. EASCII字符集与ISO/IEC 8859标准
   * [EASCII](http://zh.wikipedia.org/wiki/EASCII)：扩展美国信息交换标准代码（**E**xtended **ASCII**）,它将ASCII的b7这个奇偶校验位包括进来，用以扩展编码集的可表示范围，从而字符容量增加到了256个。其中编号0-127的字符与ASCII相同，128-255字符作为扩充的部分，表示一些表格符号、计算符号、希腊符号及特殊的拉丁符号，用以适应欧洲非英语国家的字符使用需要。也就是说，它是完全兼容ASCII编码的。ISO/IEC小组制定的[ISO/IEC 8859](http://zh.wikipedia.org/zh-cn/ISO/IEC_8859)标准之一[ISO/IEC 8859-1](http://zh.wikipedia.org/wiki/ISO/IEC_8859-1)，从维基百科上来看，这一标准所定义的字符集是与EASCII一致的。ISO/IEC 8859是一系列8位西文字符编码标准，它们基于ASCII对不同的西文字符进行了不同的扩展。
   * [ISO/IEC 8859-1](http://zh.wikipedia.org/wiki/ISO/IEC_8859-1)：国际标准化组织制订的8位字符集。它以ASCII为基础，扩展了集合范围，供附加符号的拉丁语言的使用。又称Latin-1或“西欧语言”。
4. GB2312字符集与ISO/IEC 2202标准
   * [GB2312](http://zh.wikipedia.org/wiki/GB_2312)：由中国国家标准总局制订的适应中国大陆地区简体字使用的字符编码。收录了6763个汉字及682个字符（包括拉丁字母、希腊字母、日文平假名及片假名字母、俄语西里尔字母）。GB2312是基于[EUC存储方式](http://zh.wikipedia.org/wiki/EUC)的双字节编码方式，第一个字节为高位字节，第二字节为低位字节。
   * EUC的基础也是出自ISO/IEC，是被称为[ISO/IEC 2202](http://zh.wikipedia.org/wiki/ISO/IEC_2022)的7位编码标准。GB2312的制订正遵循了这一标准。 
5. BIG5字符集与[CNS11643](http://zh.wikipedia.org/wiki/CNS11643)
   * [BIG5](http://zh.wikipedia.org/zh-cn/%E5%A4%A7%E4%BA%94%E7%A2%BC)：港澳台同胞使用的繁体汉字字符编码。也称大五码。Windows的繁体中文版是基于这种编码进行开发的。它包括了13,060个汉字及441个字符。它也是一种双字节编码方式，每个字符的首个字节为高位字节，第二个字节为低位字节。
   * [CNS11643](http://zh.wikipedia.org/wiki/CNS11643):中文标准交换码，2003年，BIG5字符集被追加到了CNS11643的附录中，拥有了较为正式的地位。CNS11543是中华民国/中国台湾地区的国家标准。向国际标准组织提交了申请，但未查到是否申请通过，且国际标准的编码是什么。
6. Unicode与ISO IEC 10646，还有UCS
   * 前面提到的字符集都是以ASCII为基础进行制定，适用于某一特定语言的使用需要，但不适用于多语言环境，Unicode的制定则是旨在推出一种适用于所有语言的替代性方案。
   * Unicode是原本由两个组织各自独自制定，后来双方将各自的工作成果合并起来共同推出的标准（一是ISO/IEC小组制定的ISO/IEC 10646标准，一个是Xerox及苹果等软件商组成的统一码联盟制定的Unicode标准）。Unicode经历了多个版本的演变（1991年到2012年，从1.0一直到6.2版，好像现在仍在修订当中），现在可以说实现了最初的设计目标。
   * ISO/IEC 10646其编号代表是ISO/IEC 646标准（ASCII）的扩展。两个组织意识到各自为政，推出两套不同的标准没有必要，所以将它们合并起来。就是现在的Unicode。
   * [UCS](http://zh.wikipedia.org/wiki/%E9%80%9A%E7%94%A8%E5%AD%97%E7%AC%A6%E9%9B%86)，全称是Universal Character Set。它是ISO/IEC 10646标准所定义的标准字符集。

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

##乱码的种类

##结语
我在资料查询过程中，强烈地发现维基百科英文版的资料详细度与准确度大大高于中文版。看来还是前者的维护人群更大，毕竟计算机科学发源于西方，并且西方人士的参与度更高。

参考资料：

* [ASCII字符编码](http://en.wikipedia.org/wiki/Ascii)
* [EASCII字符编码](http://zh.wikipedia.org/wiki/EASCII)
* [GB2312字符编码](http://zh.wikipedia.org/wiki/GB_2312)
* [GBK]()
* [GB18030字符编码](http://zh.wikipedia.org/wiki/GB_18030)
* [代码页](http://zh.wikipedia.org/wiki/%E4%BB%A3%E7%A0%81%E9%A1%B5)
* [字符编码的前世今生——腾讯大讲堂](http://djt.qq.com/article/view/658?ADTAG=email.InnerAD.weekly.20130902&bsh_bid=281085951)
* [字符编码笔记：ASCII，Unicode和UTF-8——阮一峰的网络日志](http://www.ruanyifeng.com/blog/2007/10/ascii_unicode_and_utf-8.html)