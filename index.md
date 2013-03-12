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

## 感谢

感谢所有光临本站的朋友。请关注[我的微博](weibo.com/fatbigbright)。


