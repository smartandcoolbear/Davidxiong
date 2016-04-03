---
title: Python源码剖析笔记2:Python虚拟机
layout: post
tags:
  - daily
  - python
---

很久之前写了[Python源码剖析](http://book.douban.com/subject/3117898/)的[第一篇笔记](http://lufo.me/2015/09/python_source_code1/),现在才写出来第二篇.除了因为中间旅游了半个月,更重要的是因为发现读到后面越来越难懂.本来这一部分还应该包括第12章Python虚拟机中的类机制.但是发现虽然读的过程中偶尔也能发现Python实现的精妙之处,但大部分时候都是云里雾里,感觉这样读下去作用不大,所以打算先放一放,以后再读.下面是第7-11章的笔记.

- PyCodeObject与pyc文件
	- Python代码会被编译为字节码,即PyCodeObject,对于代码中每个code block都会生成一个PyCodeObject,每个名字空间为一个code block,函数,类,module都对应着独立的名字空间.
	- import机制会触发pyc文件的生成,执行import abc时,如果没有abc.pyc,Python会先创建abc.pyc,再import.pyc文件包含三部分信息:magic number,pyc文件创建时间, PyCodeObject.不同Python版本编译出的pyc文件有不同的magic number,可以防止Python加载其他版本生成的pyc文件.时间信息保证Python文件修改后不会加载修改前生成的pyc文件.
	- 写入pyc文件时,对于不同的对象有不同的写入动作.首先写入对象类型信息,然后通过w_long和w_string将对象内容写入.w_long将数字按字节写入.
	- w_string写入非intern字符串时,直接写入字符串的类型(TYPE_STRING),长度和字符串本身.写入intern字符串时,有一个名为strings的dict保存intern字符串信息,key为字符串,value为这个字符串是第几个进入strings的.写入字符串时,先查找strings中有没有这个字符串,如果没有,将它添加到strings,然后将类型(TYPE_INTERND),字符串长度和字符串本身写入pyc文件.如果strings中有这个字符串,将类型(TYPE_STRINGREF)和这个字符串对应的编号写入pyc文件.读取pyc文件时,strings变为一个list,当读到TYPE_INTERND类型时将它加入strings,当读到TYPE_STRINGREF类型时,读出它的序号i,它对应的字符串就是strings中第i个元素.
 - Python虚拟机的执行环境
 	- PyCodeObject对象包含了字节码指令和所有静态信息,但是不包含程序运行的动态信息:执行环境.执行环境包含名字空间等,Python实际执行的时候面对的是PyFrameObject,PyFrameObject是PyCodeObject加上执行环境信息,Python虚拟机执行PyCodeObject中的字节码,生成包含PyCodeObject信息和动态信息的PyFrameObject,然后调用函数利用PyFrameObject中的信息继续执行.
 	- Python中一个.py文件为一个module,加载module有两种方式:一是通过import动态加载,二是通过python main.py这种方式加载主module.
 	- 作用域是由代码决定的,是静态的,名字空间是与作用域对应的动态的东西,是一个PyDictObject,key为变量名,value为变量对应的对象.
 	- 最内嵌套作用域规则:由一个赋值语句引进的名字在它所在的作用域内可见,而且在它的嵌套作用域也可见,除非被它嵌套作用域中用了同样名字的赋值语句所遮蔽.
 	- 一个module对应一个global作用域,类和函数定义local作用域.Python自身定义了builtin作用域,这里包括builtin函数.Python 2.2之前作用域规则称为LGB规则,沿着local,global,builtin作用域查找名字对应的对象.Python 2.2开始引进了嵌套函数,作用域规则改为LEGB,E为直接外围作用域.嵌套函数中外层函数是内层函数的直接外围作用域,但一个类的作用域与这个类中的函数的作用域不是嵌套的.
 	- 嵌套作用域规则使得决定Python行为的是代码出现的位置而不是代码执行的顺序.
 - Python虚拟机中的一般表达式
   - Python执行字节码时在每个名字空间维护一个常量表consts(保存常量,如1,"Python"),符号表names(保存符号,如i),运行时栈(执行字节码时需要的内存,用于完成操作,如创建dict,赋值等)和local名字空间(dict,保存符号到值的映射),运行时栈和local名字空间中保存的都是对应PyObject的地址.
   - 执行a=1时,先从consts中读入1,放入运行时栈,然后从names中读入a,将a,1存入local名字空间.
   - 执行d={'a':1,'b':2}时,循环读入每个键值对,储存在运行时栈,循环生成新的dict.最后存入local名字空间.创建list时,先讲所有值从consts读入运行时栈,然后依次读出存入list,然后存入local名字空间.
   - 执行a=b时,依次在local,global,buildin中搜索b,搜索到后将b对应的值压入运行时栈,然后将a与b对应的值存入local名字空间.
   - 执行a+b时,如果a,b是PyIntObject(且它们的和不会溢出)或PyStringObject,Python会进行快速运算.否则进行慢速运算,会进行大量判断类型及寻找对应加法函数等操作,速度慢一些.
 - Python虚拟机中的条件控制语句
   - 条件控制语句有if,for,while,switch,Python没有实现switch.
   - 判断时如果两个都是PyIntObject,进行快速判断,否则慢速判断.判断指令同时实现了in,is等功能.
- Python虚拟器中的函数机制
	- 每次函数调用都会生成一个PyFuncObj,它包含PyCodeObj和对应的动态信息(global作用域,局部变量等),由PyFuncObj会生成对应的PyFrameObj.
	- 函数的声明(def func())与调用(func())都在函数外部的PyCodeObj里,函数的实现在函数自己的PyCodeObj里面.
	- 有四种类型参数:位置参数,键参数,扩展位置参数,扩展键参数,函数声明时参数类型必须按照从前到后的顺序排列,如func(a,b=1,*args,**kwargs).参数是位置参数还是键参数是由实参决定的,与声明的时候没有关系.
	- 本来参数保存在.py文件对应的PyFrameObj的内存空间的运行时栈部分,函数调用时,传入函数对应的PyFrameObj的内存空间,保存在extra部分,紧挨着运行时栈.
	- 函数的局部变量也保存在extra部分,因为函数的局部变量使用的内存是不变的,在编译时就能确定,不需要动态查找PyDictObj,可以提高效率.
	- 闭包指使用了外部命名空间的变量的函数.PyCodeObj中有两个属性:co_cellvars保存嵌套的作用域中的变量名,co_freevars保存外层作用域中的变量名.它们也保存在函数对应的内存中的extra部分,所以可以实现闭包.