---
title: 解决Python中读写数据库中文乱码问题
layout: post
tags:
  - mysql
  - python
---

![](/media/files/2014/12/01.jpg)

之前写了个用户输入菜名查询菜谱的[微信公共号](https://github.com/lufo816/WeiXinCookbook/)，是把数据爬下来保存在文件里，下次查询时直接从文件读。今天作死想把数据存在数据库里，结果搞了一晚上。

其实问题很简单就能解决，不过之前用MySQL都是英文，没遇到过中文编码问题，Python中最坑的就是编码问题了Orz，开始向数据库写中文字符报错，解决方法是在创建表后执行这条MySQl语句：

	ALTER TABLE table_name CONVERT TO CHARACTER SET utf8 COLLATE utf8_general_ci;

这样表示表中所有字段都用utf-8编码，再次插入utf-8编码的字符串就ok了。

还有就是从MySQL读数据后报错，解决方案是在Python中打开数据库加上charset参数：

	db = MySQLdb.connect(host='localhost',user='root',passwd='passwd',charset='utf8')

简单两条语句坑了一晚上T_T
