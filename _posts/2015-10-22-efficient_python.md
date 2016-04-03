---
title: Python优化技巧
layout: post
tags:
  - python
---

1. **避免愚蠢的代码**
   
   - 对代码性能影响最大的是算法本身的复杂度,将复杂度降低一个级别往往能带来成千上万倍的效率提升,下面举例说明.
     
   - **dict与list**.Python中dict用hash表实现,查找时间复杂度为O(1),list查找时间复杂度为O(n),在需要对数据成员进行频繁的查找或者访问的时候,使用dict而不是list是一个较好的选择:
     
     ``` python
      from time import time 
      t = time() 
      list = ['a','b','is','python','jason','hello','hill','with','phone','test', 
     'dfdf','apple','pddf','ind','basic','none','baecr','var','bana','dd','wrd'] 
      #list = dict.fromkeys(list,True) 
      print list 
      filter = [] 
      for i in range (1000000): 
     	 for find in ['is','hat','new','list','old','.']: 
     		 if find not in list: 
     			 filter.append(find) 
      print "total run time:"
      print time()-t
     ```
     
     上述代码运行大概需要16.09 seconds.如果去掉行#list = dict.fromkeys(list,True)的注释,将list转换为字典之后再运行,时间大约为8.375 seconds,效率大概提高了一半.[这个关于list去重的讨论](http://www.peterbe.com/plog/uniqifiers-benchmark)更好的说明了dict查找的高效.
     
   - **尽量不要重新造轮子**,Python的一些科学计算库如numpy,scipy,pandas,sklearn等提供了大量的关于数据挖掘,机器学习的工具,并且实现往往比较高效,建议了解这些库的常用方法,除非有更高效的实现,否则不要重新造轮子.比如提取文本特征可用如下几行代码搞定:
     
     ``` python
     from sklearn.feature_extraction.text import CountVectorizer
     
     # 四个文本的内容
     corpus = [
          'This is the first document.',
          'This is the second second document.',
          'And the third one.',
          'Is this the first document?',
      ]
     vectorizer = CountVectorizer(min_df=1)
     X = vectorizer.fit_transform(corpus)
     X.toarray()
     
     # X的内容,文本的特征向量
     array([[0, 1, 1, 1, 0, 0, 1, 0, 1],
            [0, 1, 0, 1, 0, 2, 1, 0, 1],
            [1, 0, 0, 0, 1, 0, 1, 1, 0],
            [0, 1, 1, 1, 0, 0, 1, 0, 1]]...)
     ```
   
2. **利用Python的惰性求值**
   
   - 惰性指只有在需要用到时才求值,利用这个特点可以大大提高程序效率.下面举几个例子.
    
   - 条件表达式 if x and y,在 x 为 false 的情况下 y 表达式的值将不再计算.因此可以利用该特性在一定程度上提高程序效率:
     
     ``` python
      from time import time 
      t = time() 
      abbreviations = ['cf.', 'e.g.', 'ex.', 'etc.', 'fig.', 'i.e.', 'Mr.', 'vs.'] 
      for i in range (1000000): 
     	 for w in ('Mr.', 'Hat', 'is', 'chasing', 'the', 'black', 'cat', '.'): 
     		 if w in abbreviations: 
     		 #if w[-1] == '.' and w in abbreviations: 
     			 pass 
      print "total run time:"
      print time()-t
     ```
     
   - **学会使用generator**.简单来说就是一个惰性的可迭代对象,具体内容较多,可自行google.
   
3. 其他技巧
   
   - 字符串连接使用+连接n个字符串需要申请n-1次内存,使用join()需要申请1次内存.
   - 使用列表解析,列表解析要比在循环中重新构建一个新的list更为高效.
   
4. **使用pypy**
   
   - pypy是一个Python解释器,对于部分代码可将效率提高数倍.关于更多使用细节之后会补充.
   
5. **使用Cython**
   
   - 无论如何同样优秀的Python程序和C程序相比Python肯定要慢于C,对于对效率要求比较高的程序可以使用Python写程序的主体,C写关键的算法部分.这是[Cython官网](http://docs.cython.org/src/quickstart/overview.html)对于它的解释:
     
     > Cython is a programming language that makes writing C extensions for the Python language as easy as Python itself. It aims to become a superset of the Python language which gives it high-level, object-oriented, functional, and dynamic programming. Its main feature on top of these is support for optional static type declarations as part of the language. The source code gets translated into optimized C/C++ code and compiled as Python extension modules. This allows for both very fast program execution and tight integration with external C libraries, while keeping up the high programmer productivity for which the Python language is well known.
     
     关于Cython的更多内容之后会补充.



参考:

1. [Python 代码性能优化技巧](https://www.ibm.com/developerworks/cn/linux/l-cn-python-optim/)
2. [sklearn文本特征提取](http://cloga.info/2014/01/19/sklearn_text_feature_extraction/)
3. [Python源码剖析笔记1:PyObject](http://lufo.me/2015/09/python_source_code1/)
4. [Cython官网](http://docs.cython.org/src/quickstart/overview.html)

