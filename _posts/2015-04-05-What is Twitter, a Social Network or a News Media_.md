---
title: What is Twitter, a Social Network or a News Media? 阅读笔记
layout: post
tags:
  - algorithm
  - paper
  - influence_maximization
---
![](/media/files/2015/04/02.jpg)

这篇 paper 全面的概述了对 Twitter 的研究中的一些问题.首先分析了下 Twitter 的基本数据:整个 Twitter 所有用户 follower 的数量和 following 的数量在小于10^5时符合 power-law(y=1/x^a),大于10^5后分布不符合 power-law.然后分析了 Twitter 中的互粉情况,数据比较surprised:把 Twitter 看做图,只有22.1%的边是双向边(互粉),67.6%的用户关注的所有人没有一个人关注他,这些数据表明 Twitter 更像媒体而不是 SNS.然后分析了用户间的距离(两个用户间间隔几个互粉关系),平均距离是4.12,70.5%的用户对距离是4或更少,97.6%的用户对距离6或更少.然后比较用户和粉丝间的同质性,从地理位置和受欢迎度两方面比较,作者在判断地理位置时用了一个很聪明的办法:因为用户的地理位置信息往往是乱填的,所以作者采用用户所在的时区代表位置,最后的结果是粉丝比较少的用户和他的粉丝之间有较高相似度.

之后作者对三种度量最具影响力的人的方法进行了比较:follower 数量,PageRank,retweet 数量,结果显示前两个度量结果基本一样,最后一个和前两个差别较大.因为某些创造有价值内容的帐号(比如新闻帐号)往往比粉丝更多的明星用户获得更多的 retweet,关于这一点之后还有说明.

作者接着分析了 Twitter 的热词.与 Google 的搜索热词比较相似度很低,只有3.6%.95%的 Google 每日热词是新的而 Twitter 只有72%,可能因为 Twitter 的 retweet 机制让热词的生命周期更长.作者还发现突发性新闻往往在 CNN 进行报道之前已经在 Twitter 上火起来了,表明 Twitter 是对突发事件很敏感的媒体.最后作者分析了与热词有关的 tweets 的来源,发现绝大部分是用户自己发表的,其次是 replies 和 retweet,最后是 mention(@).接着作者研究了热词持续的时间:如果热词连续一天没有相关 tweet 发布则认为这个词结束了一个生命周期,73%的热词只有一个生命周期,15%有两个,5%有三个.31%的热词只能持续1天,只有7%能超过10天.最后作者用已有的算法把热词分为四类,每类的爆发和持续时间都很不同.

然后作者对 retweet 行为进行了分析,发现几乎所有用户无论粉丝数量多少,一旦被 retweet,那么因为 retweet 而看到这条 tweet 的用户数几乎是一样的,这也解释了为什么根据 retweet 数量判断影响力会和其他方法不同.接着作者发现 tweet 数量和被 retweet 的深度(树深)与 tweet 数量和被 retweet 的人数都符合 power-law.然后分析了 retweet 发生的时间:50%发生在一小时内,75%发生在一天内,但有10%发生在一个月后,并且被二层 retweet(retweet 别人对他的 retweet)或多层 retweet 的平均时间比直接 retweet 更短.最后(这回真的是最后了),作者发现一个人往往喜欢对他关注的一小撮人 retweet 而不是对关注的所有人均匀的 retweet,而一个人也往往被一小撮粉丝 retweet 而不是被所有粉丝均匀地 retweet.

总之这篇 paper 大而全的分析了人们在 Twitter 的活动,把应该用图表说明的东西变成文字也是累死我了,Over.

