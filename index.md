---
layout: page
title: 胖大海的杂烩站
tagline: 
---
{% include JB/setup %}

## 本站简介

本站是使用jekyll博客系统搭建的个人主页，用于分享一下我自己的编程心得、兴趣爱好、以及开发的程序的介绍等。
    
## 文章列表

以下是文章列表

<ul class="posts">
  {% for post in site.posts %}
    <li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>

## 备注
* 2013-03-20 夜

发现之前_config.yml中的引擎配置语句的位置有误，导致配置未起作用而无序列表项若以中文开头，则列表不能正确生成。将这句“markdown: rdiscount”移至语句“pygments:true”这个语句之上，才能使引擎配置生效。
## 感谢

感谢所有光临本站的朋友。请关注[我的微博](http://www.weibo.com/fatbigbright)。


