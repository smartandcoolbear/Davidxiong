---
title: 使用Python批量替换指定目录所有文件中的指定文本
layout: post
tags:
  - python
  - skill
---

![](/media/files/2014/10/07.jpg) 
好久没动博客，国庆七天宅在宿舍闲的蛋疼，最后一天终于想整下博客了>_<。

之前迁移过来是用的把xml转为markdown的工具[exitwp](https://github.com/thomasf/exitwp)把原来博客转为.md格式后，用"categories"表示文章的标签，而新的网站要使用"tags"表示。所以以往的标签都丢了。身为序猿这都不是事儿，写了个python脚本实现了把所有文章里"categories"替换为"tags"。然后对每篇文章的tags进行了逐一修改，博客tags一栏终于能看了！

    #-*-coding:utf-8-*-
    import os
    
    myRoot = '/home/lufo/lufo816.github.com/'
    from_ = 'categories'
    to_ = 'tags'
    
    def change(rootDir):
        list_dirs = os.walk(rootDir)
        for root, dirs, files in list_dirs:
            for f in files:
                myFile = os.path.join(root, f)
                f = open(myFile, 'r')
                data = f.read()
                data = data.replace(from_, to_)
                f.close()
                f = open(myFile, 'w')
                f.write(data)
                f.close()
    
    change(myRoot)