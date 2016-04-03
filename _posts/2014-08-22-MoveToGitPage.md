---
title: 不折腾会死，Blog迁到Github了！
layout: post
tags:
  - web
  - skill
---

![](/media/files/2014/08/22.jpg) 
几个月前把Blog[从wordpress迁到lofter](http://lufo816.github.io/2014/06/08/ByeByeBeautiful.html)，果然还是不能用这种图省事的方法，最近又遇到了新问题。lofter上各种推荐烦死人，并且文章分享到朋友圈排版丑的要死。于是乎折腾了两三天迁到github。特别感谢[waynezhang](https://github.com/waynezhang/blog)提供的模板，风格极简，字体排版美如画，不能更赞。

其实这本是挺简单的事，无奈生性愚笨，折腾好久才搞定，下面就说下我遇到的那些坑。

我的系统是ubuntu。首先要了解下git和github，安装git（这些比较简单，自行google），然后新建一个repository，**第一个坑**，这个repository的命名一定要是username.github.com，这样你的page的域名才是username.github.com，否则就是github.com/lufo/repositoryname我试了好久才知道要这样才行ＴＴ。

现在**第二个坑**来了。这样做的话整个网站的代码基本都要自己写，需要你有极强的前端知识。这里有一个更方便的做法是不用自己新建repository，直接找一个你喜欢的Blog然后在github上fork它的代码，这样就clone了一个Ｂlog，然后就改一些内容让这个Blog变成你自己的就可以了！当然要按作者要求申明版权信息。

首先用clone命令把代码down到本地：（这里用的是SSH连接，要用SSH连接请参照[这里](http://beiyuu.com/github-pages/)配置）
>git clone git@github.com:username/useename.github.com.git

然后进行修改，_post里放的是文章，_image里放的是图片，把原来的删除换成你的就可以了。然后改下其他文件把网站上原作者的标记（名字，各种链接等等）换成你的就可以了。

**第三个坑**来了，我想把lofter上文章导入，找了很久发现一个把xml转为markdown的工具[exitwp](https://github.com/thomasf/exitwp)，但试了很久都不行，尼玛原来不支持lofter导出的xml文件，必须要是wordpress导出的！于是就把之前在wp上写的文章转好，手动编辑了几篇在lofter写的文章才算了事。

这时差不多大功告成了，用一下几个命令更新github的仓库：（要在clone到本地的目录下执行）
>git add -A

>git commit -m "first commit"

>git remote add origin git@github.com:username/useename.github.com.git

>git push -u origin master

等几分钟在访问你的主页就更新成功了，之后写文章时只需要写好放到_post里然后执行下面指令提交就可以了！
>git add -A

>git commit -m "first commit"

>git push -u origin master

以上！