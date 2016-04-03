---
title: Maximizing the Spread of Influence through a Social Network 阅读笔记
layout: post
tags:
  - algorithm
  - paper
  - influence_maximization
---
![](/media/files/2015/03/05.jpg)

最近开始疯狂读论文了,基本有闲时间就读,刚开始有点慢,差不多两天一篇.之前读了些 information diffusion 的文章,打算接下来读 influence maximization 方面的并尝试发下论文.

今天读完了 [Maximizing the Spread of Influence through a Social Network](http://www.cs.cornell.edu/home/kleinber/kdd03-inf.pdf),这是篇比较老的研究影响力最大化的论文,比较经典,讲的清楚详尽.篇 paper 主要研究的问题是**在有 n 个节点的社交关系图中,怎样选取 k 个 activity 的节点使一段时间后 activity 的节点最多**.

这篇 paper 主要思路是这样,首先它证明了这个问题是[ NP 难](http://zh.wikipedia.org/wiki/NP_%28%E8%A4%87%E9%9B%9C%E5%BA%A6%29)的,然后作者提出如果 σ(S)(S 为初始 activity 的节点集合,σ(S) 表示最终 activity 的最优节点集合)是[submodular](http://en.wikipedia.org/wiki/Submodular_set_function)的,那么可以用 [hill climbing](http://en.wikipedia.org/wiki/Hill_climbing) 算法求得近似解,并且保证近似解至少能达到最优解的 63%(1 - 1/e).然后作者就这个问题提出了 n 种模型,依次证明每种模型是不是 NP 难的,它的σ(S)函数是不是 submodular 的,证明过程非常详细,大部分是无聊的,少部分如果看懂能给你惊喜.然后最后的结论是 general 的模型是无法用 hill climbing 算法求得足够好的近似解的,但有很多特例是可以的.然后又建了两个扩展的模型,一种是节点能从 activity 变为 inactivity 的,一种是能动态的改变节点被传染的概率的,然后说明了这两种模型也是可以使用 hill climbing 算法找到次优解的.over.

前几天被大量数学公式虐惨了,终于读到篇能大体明白的 paper,还是老人们写的东西好懂.今后争取没读完一篇都在这里写下感受吧.