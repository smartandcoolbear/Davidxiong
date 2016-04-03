---
title: python脚本爬下载链接
layout: post
tags:
  - python
  - skill
---

![](/media/files/2014/10/11_3.jpg)

今天发现了一个好玩的小电(huang)影(pian)，《Happy Tree Friends》。然后搜了下有一个网站提供下载。蛋疼的是有几十上百个下载链接，于是写了个小脚本把所有链接爬了下来copy到迅雷里就ok了！

    #-*-coding:utf-8-*-
    import re  #python中使用正则表达式需导入此库
    import urllib2

    url = "http://www.happytreefriendscn.com/download/"

    href = 'href="(.*?)"'  #定义正则表达式的句法规则
    href_re = re.compile(href)  #通过compile函数“编译”正则表达式

    res = urllib2.urlopen(url)
    data = res.read()
    href_info = href_re.findall(data)

    f = open('result.txt', 'w+')

    for item in href_info:
        if '.flv' in item:
            print item
            f.write(item + '\n')

