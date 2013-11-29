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
5. appfog中创建并上传特定版本的RoR应用，例`af push htmlexercises --runtime ruby193`
6. 使用命令`rails g model event name:string description:text`来创建一个模型类，类名为event，有string类型字段name，和text类型字段description。   
    结合命令`bundle exec rake db:migrate`将上述新建的模型类同步更新到数据库结构定义。相应的内容migrate内容会保存到db/migrate目录下的rb文件中。   
    使用命令`tail -f log/development.log`可以查看db:migrate的过程。   
    至此，用`rails console`或`rails c`来打开交互控制台，就可以用各种对象命令来操纵刚才创建的数据库表。   
    使用命令`rails g migration add_status_to_events`增加一个db/migrate下的文件，可以在文件里为已有的表增减修改表结构。
