---
title: Everyone’s an influencer quantifying influence on twitter 阅读笔记
layout: post
tags:
  - algorithm
  - paper
  - influence_maximization
---
![](/media/files/2015/04/01.jpg)

这篇 paper 主要工作是根据 Twitter 用户之前发过的 Tweets 的传播度预测未来的传播度.度量传播度使用的是用户发过的 Tweets 平均被转发或 RT@原作者的次数.作者爬下大量数据后提取了最近发过的 Tweets 中包含短链接的用户研究所有包含短链接的 Tweet 的传播度(因为包含短链接的 Tweet 易被分类,这一特点之后回用到).然后进行预测.

预测作者选用的是回归树,使用的特征为:

- followers
- friends(关注的人)
- tweets
- date of joining

需要预测的:

- average, minimum, and maximum total influence

结果预测的结果与真实结果的的均值(由于回归树只有有限个结果,这里指某个结果所有样本的真实值的均值)拟合度很高,回归系数(R2)为0.98,但不取均值直接比较 R2=0.34,不是很理想,于是作者开始作死了.

然后作者对 Tweets 进行分类,在(Amazon’s Mechanical Turk)[https://www.mturk.com/mturk/welcome]上花钱找人做问卷调查,选取了1000条 Tweets 对每条的 URL 提取特征,最终提取了以下几个:

- Rated interestingness(被调查者的感兴趣度)
- Perceived interestingness to an average person(被调查者认为别人的感兴趣度)
- Rated positive feeling
- Willingness to share via Email, IM, Twitter, Facebook or Digg.
- Indicator variables for type of URL(SNS,blog,News...)
- Indicator variable for category of content(Technology,gaming,sports...)

然后加入这些特征在做回归预测,高潮来了,R2从0.34降到0.31.

不得不佩服作者把失败的实验拿出来分享的勇气,作者分析了下原因:一是由于人工成本太高,只对1000个 Tweets 进行了分类,不 general,二是只对 post 的 Tweets 进行了分类,没对接受者的口味进行分类(比如某体育明星发了条科技新闻,调查结果认为科技新闻比体育新闻更受欢迎,但他的粉丝可能并不感冒).

最后作者扯了下做营销的人应该怎么选择种子用户,比较扯,就不讲了.

Over.