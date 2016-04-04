---
title: About
layout: page
comments: yes
---


## 基本信息

- 姓名:熊川樘
- 教育经历: 2013级上海交通大学密西根学院电子科学与技术专业
- 个人博客: [Davidxiong.com](http://Davidxiong.com/)
- Github: [github.com/smartandcoolbear](https://github.com/smartandcoolbear)
- 手机: 15201931536
- 电子邮件: [Davidxiong_work@163.com](mailto:Davidxiong_work@163.com)

## 技能
- 熟悉Python,有使用机器学习相关库(numpy,scipy,sklearn等)经验,了解C,C++,熟悉深度学习框架Caffe
- 熟悉常用数据结构,熟悉常用机器学习算法
- 长期使用Linux/Unix操作系统,熟悉常用shell命令,熟悉docker容器技术


## 实习经历

- 2016.2-现在:于[SYSU-CMU Joint Institute of Engineering](http://jie.sysu.edu.cn/)实习,主要负责表情识别相关工作,工作内容包括:
	- 人脸检测:使用多个face detector(MoT,dpm)从图片中截取人脸,作为训练和测试数据
	- Data augmentation:使用平移,旋转等方法增加数据
	- 构建CNN:自己构造CNN进行训练
- 2015.7-2015.9:于中国科学院重庆研究院[智能多媒体技术研究中心](http://www.cigit.cas.cn/jggk/ggkypt/201403/t20140328_4082809.html)人脸识别组([云从科技](http://www.cloudwalk.cn/)研发组)实习,工作内容包括:
  - 构建CNN:修改CNN结构和参数,提高人脸识别正确率
  - 数据抓取:完成爬虫抓取百度,人人,bing等网站的图片并进行自动筛选包含人脸的图片
- 2014.3-2015.7:中山大学移动信息工程学院人工智能,软件设计的教学助理

## 论文发表

- **Xuebo Liu**, Shuang Ye, Yonghao Luo, Yanghui Rao, [ZhihuRank: A Topic-Sensitive Expert Finding Algorithm in Community Question Answering Websites](http://lufo.me/docs/ICWL2015.pdf), [International Conference on Web-based Learning (ICWL) 2015](http://www.cityu.edu.hk/merc/icwl/icwl2015home.htm)
- Xin Li, Yanghui Rao, Yanjia Chen, **Xuebo Liu**, Huan Huang, [Social Emotion Classification via Reader Perspective Weighted Model](http://lufo.me/docs/AAAI2016.pdf), [AAAI 16 Student Abstract](www.aaai.org/Conferences/AAAI/2016/aaai16studentcall.php)
- Xin Li, Haoran Xie, Yanghui Rao, Yanjia Chen, **Xuebo Liu**, Huan Huang and Fu Lee Wang, [Weighted Multi-label Classification Model for Sentiment Analysis of Online News](http://lufo.me/docs/BigComp2016.pdf), [Big data and smart computing (BigComp) 2016](http://conf2016.bigcomputing.org/main/)


## 项目经历

- **机器学习**
  - ZhihuRank:问答类社交网站中专家推荐创新算法,在社交类问答网站(知乎,Quora)中基于用户与问题间的主题相似度和用户间的赞同关系对每个问题推荐最适合回答这个问题的用户.算法基于LDA和PageRank,从知乎上抓取了超过20万条答案进行实验,效果比同类算法提高2%-10%,以第一作者完成论文并发表在ICWL 2015
  - 人脸识别:完成基于深度学习的人脸识别系统.使用affine transform进行alignment,使用CNN提取图片特征,使用联合贝叶斯完成verification.LFW数据及上测试人脸识别的准确率超过99%
  - [语音识别系统](https://github.com/lufo816/SpeechRecognitionSystem):完成基于GMM和HMM的语音识别系统,可以识别特定的连续语音,如电话号码,单个数字的识别准确率超过90%,电话号码的识别准确率超过80%.使用MFCC作为特征,HMM中的每个state使用GMM表示,以word为单位进行识别
  - 文本情感识别:使用word2vec提取文本特征,使用维基百科进行训练,SVM作为分类器,SVM使用线性核,使用One vs Rest的方法进行多分类,在SemEval-2007英文数据集(短文本)上准确率超过43%,在新浪新闻数据集上准确率超过60%
- **其他**
  - 最流行的[知乎民间 API](https://github.com/egrcc/zhihu-python)(Github上有超过500个stars):可以获取知乎的各种数据,使用requests库发送请求,使用BeautifulSoup对网页源码进行解析.个人负责修改Bug,添加功能,如获取每个答案赞同人列表等


## 演讲

- 2015年11月于广州华南理工大学进行关于发表在[ICWL 2015](http://www.cityu.edu.hk/merc/icwl/icwl2015home.htm)上论文的演讲,幻灯片在[这里](http://lufo.me/docs/pre_icwl_2015.pdf)

-----

如果这个博客对您有所帮助，您可以扫描下面的二维码请我喝杯咖啡:

手机微信扫一扫下方的二维码可以直接付款
![](/media/files/2015/07/09.jpg =256x256) 
