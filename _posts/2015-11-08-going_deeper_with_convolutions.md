---
title: Going deeper with convolutions 阅读笔记
layout: post
tags:
  - cnn
  - dnn
  - deep_learning
  - face_verification
---

暑假在中科院[实习](http://lufo.me/2015/09/%E5%AE%9E%E4%B9%A0%E7%BB%93%E6%9D%9F/)的时候一直在用GoogleNet做实验,但直到今天才读了提出GoogleNet的这篇paper,真是惭愧.

读完以后还有很多细节不懂,但这篇paper主要思想就是提出了inception层,有两个好处,第一,inception层计算时降低了数据的维度,使得channel可以再有限的计算资源下构建更复杂的网络结构(GoogleNet有22层),第二,inception层将多个scale的feature结合起来,效果更好,原文是这么说的:
> One of the main beneficial aspects of this architecture is that it allows for increasing the number of units at each stage significantly without an uncontrolled blow-up in computational complexity. The ubiquitous use of dimension reduction allows for shielding the large number of input filters of the last stage to the next layer, first reducing their dimension before convolving over them with a large patch size. Another practically useful aspect of this design is that it aligns with the intuition that visual information should be processed at various scales and then aggregated so that the next stage can abstract features from different scales simultaneously.

下面看一下inception层的结构:

![](/media/files/2015/11/03.png)

可以看到它是4个scale的feature的结合.3个黄色的1x1的卷积层都是降维的作用,作者认为降维后仍可以保留大部分原始信息.上一层的输出分别经过1x1,3x3,5x5的conv和pooling层最后结合在一起作为inception层的输出.下面看一下GoogleNet的结构,进一步解释inception层:

![](/media/files/2015/11/04.png)

以inception (3a)为例,它的输入是28x28x192,有192个channel,1x1的conv只输出了64个channel,降低了维数但保留了大部分信息,3x3和5x5的conv也都是在降维后进行,pooling层经过降维也只有32个channel,最终的输出为28x28x256,channel增加的很少.所以说inception层允许CNN更加deeper.

关于GoogleNet最后几层原文中是这样说的:
> The use of average pooling before the classifier is based on [12], although our implementation differs in that we use an extra linear layer. This enables adapting and fine-tuning our networks for other label sets easily, but it is mostly convenience and we do not expect it to have a major effect. It was found that a move from fully connected layers to average pooling improved the top-1 accuracy by about 0.6%, however the use of dropout remained essential even after removing the fully connected layers.

大概就是说用avg pool替代fully connected效果更好,linear层的作用是在使用其他数据集的时候做一个映射,比如我们训练face verification的model的时候有10000多类别,就要在这一层将1024维的输入映射到10000多维.dropout层是有一定几率(这里是40%)将输入变为0输出,有助于避免overfiting,基本现在所有的CNN都有用到.

差不多这篇paper的重点就是这些,这个idea真的很牛逼,在计算复杂度没有明显提供的情况下把CNN做到这么多层,膜拜.