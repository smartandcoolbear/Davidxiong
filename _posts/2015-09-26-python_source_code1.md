---
title: Python源码剖析笔记1:PyObject 
layout: post
tags:
  - daily
  - python
---

很久之前就想读[Python源码剖析](http://book.douban.com/subject/3117898/),但是很久之前这书就没卖的了,只能在淘宝买了个盗版,结果质量惨不忍睹,问了下别人据说新版发行日期还未定,发现[豆瓣阅读](http://read.douban.com/ebook/1499455/?dcs=subject-einfo&dcm=douban&dct=3117898)上有电子版,效果还可以,就花40大洋买了(刚上去一看降价了20块Orz),这几天把前几章介绍PyObject的部分读完了,下面是笔记.

- PyIntObject
  - 定长不可变对象
  - 小整数对象:[-5,257),为了防止使用小整数时频繁申请释放内存Python初始化时申请小数组对象的内存并且不释放.
  - 其他整数:对其他整数,Python运行环境将提供一块内存空间,这些内存空间由这些大整数轮流使用,也就是说,谁需要的时候谁就使用.这样免去了不断地malloc之苦,又在一定程度上考虑了效率问题.free_list是一个管理空闲int内存的链表,每当没有空闲内存时,申请一个PyIntBlock,PyIntBlock默认保存一个可以保存82个int的数组和指向下一个PyIntBlock的指针.
- PyStringObject
  - 不可变对象,带来的方便:可作为dict的键值,实现intern机制,不便:字符串修改需要重新申请内存.
  - intern机制:建立dict:interned,key为已经被intern机制处理过的字符串对象,对一个字符串对象(比如'abc')进行intern机制时先看interned中有没有这个对象,如果有则将指针指向’abc’,之前创建的临时对象’abc’计数减为0,销毁.如果没有'abc’将它记录到interned中.注意只包含字符,数字和下划线的字符串才会被intern,Python源码中解释是这样:This is generally restricted tostrings that **"looklike" Python identifiers**, although the intern() builtincan be used to force interning of any string.
  - 字符缓存:对一些字符(windows为0-255)有缓存机制,这些字符对象第一次创建时字符缓存池中的指针指向这个对象,导致它的计数永远大于0,不会消失,下次再使用时不需要重新创建对象.
  - 字符串连接效率低,使用+连接n个字符串需要申请n-1次内存,使用join()需要申请1次内存.
- PyListObject
  - 维护一个数组,数组元素为指向PyObject的指针,指针指向的内容可变.
  - 类似STL中的vector,有两个元素ob_size和allocated,ob_size为已使用的内存,allocated为分配给这个对象的内存(类似vector,为了避免频繁申请内存,分配的内存大于申请的内存).在中间插入元素要将后面的元素后移,删除元素要前移,效率低下.
  - 对象缓冲池:一个PyListObject不再使用时不立即释放内存,先查看free_list缓存的PyListObject是否已经满了,如果没有,将PyListObject加入free_list中,下次使用PyListObject时不需要重新申请内存,重新初始化下free_list中缓存的PyListObject就可以.需要注意PyListObject中PyObject*列表被释放了,没有被缓存.
- PyDictObject
  - 类似C++的map,map实现方式为红黑树,搜索复杂度为O(logN),dict实现为hash表,搜索复杂度为O(1).因为Python实现中大量用到dict,比如通过变量名获取变量值,所以对效率要求更高.
  - dict中一个key-value对为一个entry,保存指向key的指针,指向value的指针和hash值.
  - 在确定元素位置时,先对key计算hash,如果这个位置不可用则使用二次探测函数计算下一个位置.
  - entry有三个状态,开始创建时为unused状态,key,value都指向null,被使用时为active,key,value都指向PyObject,删除时为dummy状态,key指向dummy,value指向null,dummy本质为一个PyStringObject,表示这个元素被删除,但是这个查找时被删除元素后面可能有正确的key,所以遇到被删除的元素要继续查找下去,所以设为dummy状态和unused区分.
  - 查找:先计算hash值,然后与dict元素数量相与,将hash值变为0到size-1,如果对应位置为unused,返回一个entry,如果对应位置的key与要查找的key相同(先判断地址,再判断值),则找到了要找的key,否则查找下一个元素.对应位置为unused时返回的entry有两种:如果查找的路径下遇到了dummy,则记录下dummy的地址,最后返回这个dummy的entry,如果没有则返回unused的entry,因为dummy没有被删除,在插入时需要先搜索,如果在dummy上插入则直接修改dummy即可,不用浪费一个新的entry.
  - 插入:先查找,如果有key就修改value,没有就在查找返回的entry上加入新的key,value.
  - 删除:先查找,然后删除key,value指向的内容,把entry从active变为dummy.
  - 当插入后active+dummy的个数占总entry数2/3时需要增加entry,因为这时碰撞率已经很高了,增加后的entry为8的指数且大于active的entry的4倍.
  - dict有类似list的缓冲池,与list缓冲池机制一样.