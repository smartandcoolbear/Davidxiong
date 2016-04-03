---
title: CNN + Joint Bayesian 实现 Face Verification
layout: post
tags:
  - daily
  - cnn
  - deep_learning  
  - face_verification 
---

最近一周一直在帮老师做Face Verification的原型,服务器caffe都没装好,先耽误了两三天装caffe,坑真是太多,终于明白为什么之前[实习](http://lufo.me/2015/09/%E5%AE%9E%E4%B9%A0%E7%BB%93%E6%9D%9F/)的时候有个实习生装了几个星期都没装好.总之一定要注意版本.

然后是代码,[这里](https://github.com/cyh24/Joint-Bayesian)有一份Joint Bayesian的代码,按照论文[Bayesian Face Revisited: A Joint Formulation](http://research.microsoft.com/en-us/um/people/jiansun/papers/ECCV12_BayesianFace.pdf)中的方法实现了一遍,用LBP特征在LFW数据集上accuracy能达到90%,于是就用这个代码训练Joint Bayesian,然后用pycaffe从之前训练好的模型提取图片特征,进行verification.

之前的模型是用google net训练的,数据是CASIA Webface,将每个人当做一个类进行训练,当时用plda做出来LFW的accuracy是92.73%,比较低是因为数据,记得在另一个扩展了的CASIA的数据集上超过了97%.但是用这个模型加Joint Bayesian最后accuracy才80%,我试了下直接算欧氏距离都有75%.之前实习时老师就说过这个论文中用的EM算法效果一般,前几次模型效果还行,往后收敛后反而效果更差,再加上论文中使用的一个更大的数据集训练的,而我直接是用的LFW进行训练,所以效果会比较差.但是时间紧急,暂时只能先做到这样.以后可以看一下之前的plda是怎么做的,再用Python实现以下,或者听说如果数据足够好,直接用欧氏距离accuracy都能超过99%.接下来两个月准备北上去实习,下学期就专心做毕设了.代码我放到了[Github](https://github.com/lufo816/face_verification_demo).