---
title: Microblog in China identify influential users and automatically classify posts on Sina microblog 阅读笔记
layout: post
tags:
  - algorithm
  - paper
  - influence_maximization
---
![](/media/files/2015/04/03.jpg)

这篇对新浪微博进行了分析,还是中大人写的.

它前半部分和我读的[上一篇 paper ](http://lufo.me/2015/04/05/What%20is%20Twitter,%20a%20Social%20Network%20or%20a%20News%20Media%3F.html)差不多,对新浪微博整体的数据做了下分析,结果和上篇差不多不再细说.

接着它提出了一个计算影响力最大的人的算法,**通过某人的微博能让多少人看到来进行评价**,这个想法是很好的,首先它说如果一个人 timeline 上有 N 条微博,那他会阅读其中的 ln(N+e) 条.这点其实是很重要的,但作者没有表明来源,不知道是不是真的这样.奇怪的是作者的公式中有一部分使用了这个公式,有一部分是假设了j阅读 i 发过的微博的概率是 i 发过的微博数除以 j 关注的所有人发过的总数.总之公式不是很完善.

然后作者用朴素贝叶斯对文本进行分类,用 NB 应该是偷懒了.最后作者把以上两者结合得到了计算各个主题下影响力最大的人的方法,就是根据某人过去发的微博所属的主题做极大似然估计得出这个用户的兴趣矩阵,然后如果某篇微博属于主题 i,就把兴趣矩阵的第 i 个元素当做这个用户会转发这个微博的概率(之前是常数).我觉得这个公式还是比较水,作者也没有展示结果.

Over.

