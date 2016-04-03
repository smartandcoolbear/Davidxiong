---
title: 发现 Face Verification demo 效果差的原因
layout: post
tags:
  - daily
  - cnn
  - deep_learning  
  - face_verification 
---

之前的[一篇博客](http://lufo.me/2015/11/face_verification_demo/)记录了写Face Verification的demo的过程,里面说到LFW上accuracy只有80%,还猜测了各种原因.但是昨天才发现了真正的原因,知道真相的我眼泪掉下来.

为了让accuracy提高,最近我自己又开始训新的模型,还找了中科院的程博要了他们新训的模型(在此感谢程博),这个模型只用LFW训练plda都能达到98.6%的accuracy,用更多数据训plda可以达到99.5%.然而我用这个模型做测试还是80%,我猜测可能是plda参数的问题,于是直接拿matlab代码训练出的参数计算score,然而效果还是没有变化.我又比对了matlab代码提取出的特征和Python代码提取出的特征,有微小差异(猜测可能因为matlab和Python转灰度图和resize的实现略有差异),为了验证是不是因为这个导致效果差,我又直接用matlab提取出的特征测试了一遍,结果还是80%.现在特征也一样,plda参数也一样,但结果差别这么大,只能怀疑是Python代码的测试部分有问题.于是我把score明显不对的图片对print出来,发现明明应该是同一人的图片有的竟然不是同一人!LFW测试的图片对是从网上下载的,我以为图片的id就是按名称升序排列的LFW图片列表中的位置,其实有些是不对的.于是今天按照官网给出的[图片对](http://vis-www.cs.umass.edu/lfw/pairs.txt)修改了图片的id,终于accuracy提高到了99%.

因为代码是在别人的基础上改的,测试的代码完全是别人写的,所以才会因为这个小错误浪费这么多时间.其实图片对的id所代表的意义只是我想当然这么认为的,之后定要注意对代码每一部分代表的意义弄明白,尤其是这种关键部分.