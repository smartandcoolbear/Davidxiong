---
title: PythonCookbook笔记1:数据结构
layout: post
tags:
  - daily
  - python
---

最近开始读[Python Cookbook](http://chimera.labs.oreilly.com/books/1230000000393/),刚刚读完第一章,这一章介绍了Python中自带的各种数据结构,学习了很多之前不知道的.感觉这书组织不是特别有条理(非贬义),从哪里都可以开始读,主要介绍了Python的各种奇技淫巧,非常有用,有趣.下面是笔记:

1. 任何可迭代对象都可以用这种方法解压:`x,y=[4,5]`,如果想解压出n个元素,可以这样:`name, *phone_numbers=('Dave','773-555-1212', '847-555-1212')`,这样`phone_numbers`就是`['773-555-1212', '847-555-1212']`了.
2. `collections.deque`是**d**ouble-**e**nd **q**ueue,`deque(maxlen=N)`可以规定它的大小,用来保存N个历史记录.deque两段插入或删除的时间复杂度都为O(1).
3. 使用`heapq`模块可以进行一些堆相关的工作,创建堆时间复杂度为O(n),插入和删除的时间复杂度为O(logN),`nlargest(n,list)`和`nsmallest(n,list)`返回堆中最大或最小的n个元素,具体做法为从list中取出n个元素建成堆,然后对list中的每个元素与堆中最大的元素比较,如果比它大则pop出来此时堆中最小的元素然后将较大的元素push进去,最后堆中剩余n个最大的元素,最后对这n个元素进行一次排序,复杂度小于O(n+mlog(n)+nlog(n)),m为list中元素总数,所以当n<<m时,复杂度小于排序的复杂度(O(mlogm)).当n=1时,最好用`min()`,`max()`,当n接近n+m时,最好先排序再取n个元素.
4. 可以用堆实现优先级队列,pop和push的复杂度都为O(logn).
5. 使用defaultdict可以在遇到不存在的key时会直接创建这个元素而不是报错,这样能使代码更简洁,避免判断key是否存在:

		from collections import defaultdict
	
		d = defaultdict(list)
		d['a'].append(1)
		d['a'].append(2)
6. 可以使用`from collections import OrderedDict`维护dict的顺序,它使用一个双向链表保持顺序,内存开销大概是普通dict的两倍.
7. 对dict进行排序可以使用zip,注意zip返回的可迭代对象只能遍历一次,遍历完之后为空.
8. `dict.keys()`和`dict.items()`都可以进行set的操作,`dict.values()`不可以.因为dict和set都是用哈希表实现的,key和set的元素都要求是hashable的(也正因此不允许有重复元素,它们hash值一样查找时会发生错误),value不要求hashable.Python源代码中的注释:D.items()/keys() -> a set-like object providing a view on D's items.
9. 序列去重:

		def dedupe(items, key=None):
			seen = set()
			for item in items:
				val = item if key is None else key(item)
				if val not in seen:
					yield item
					seen.add(val)
key的作用是使unhashable的对象hashable,如将dict转为tuple,或者根据一些特定的部分去重.这个方法可以保持原始序列的顺序.
10. 对于比较dirty的数据常常需要截取一部分,如:

		record = '....................100          .......513.25     ..........'
		cost = int(record[20:32]) * float(record[40:48])
这样写unreadable,最好改成:

		SHARES = slice(20,32)
		PRICE  = slice(40,48)
		
		cost = int(record[SHARES]) * float(record[PRICE])
		
11. `collections.Counter`专门用来计数,它接受一个可迭代对象,然后产生一个dict,key是可迭代对象中的每个元素,value为它出现的次数,支持很多操作,包括counter间的加减.
12. 对一个dict组成的list安装dict中某个value进行排序,以往我通常使用`sorted(dict_list, key=lambda x:x[key])`,可以使用`sorted(dict_list, key=itemgetter[key])`,效果一样,itemgetter也可以作用在其他有`__getitem__`属性的对象上,它返回一个函数(类似上面的lambda表达式).itemgetter也可以接受多个参数,返回一个tuple. attrgetter与itemgetter类似,可以用它得到一个对象的attr.
13. `from itertools import groupby`接受两个参数,第一个是iterator,第二个是key,返回一个iterator,遍历iterator有两个元素,第一个是key,第二个是key对应的对象.注意使用groupby时对象要根据key提前排好序,它只查找连续的相同的值合并.
14. 对列表进行过滤可以直接使用这种方法:`[i for i in list if i>0]`,这回返回一个list,如果返回的list很大,可以使用`(i for i in list if i>0)`,这回返回一个generator,对与dict,可以使用`{key: value for key, value in dict.items() if value > 0}`.也可以使用filter来做,filter与前一种方法速度相似,适合过滤规则较复杂的情况,filter有两个参数,第一个是函数func,第二个是列表list(或者其他可迭代对象),返回一个generator,遍历它可以得到list中所有func(i)返回true的元素i.也可以使用`from itertools import compress`,compress接受两个参数,第一个为一个列表list1(或者其他可迭代对象),第二个为一个保存bool值的列表list2(或者其他可迭代对象),返回一个generator,遍历可以得到所有list2[i]为true的list1[i].
15. `from collections import namedtuple`可以允许你使用名字而不是下标访问tuple,让程序更加readable:

		Subscriber = namedtuple('Subscriber', ['addr', 'joined'])
		sub = Subscriber('jonesy@example.com', '2012-10-19')
这样就能通过`sub.addr`访问元素.实现方法类似生成一个类,类中的变量名是addr, joined,值是tuple对应的值.当dict很大时可以用`sub._replace(**dict)`(sub是上面代码中的对象,**的作用是让dict解包,如{1:'a'}解包为1='a')让dict变为namedtuple节省内存,但namedtuple是不可变的,可以用`sub._replace(attr=new_value)`改变但本质上是创建了一个新的namedtuple,开销很大.
16. You need to execute a reduction function (e.g., sum(), min(), max()), but first need to transform or filter the data.可以这样:`s = sum(x * x for x in nums)`,等同于`s = sum((x * x for x in nums))`,先生成一个generator然后求和,比`s = sum([x * x for x in nums])`更加优雅简洁.
17. `from collections import ChainMap`可以将多个dict连接起来,ChainMap本质上维护一个list,保存多个dict,按照一定的规则访问这些dict,例如:

		a = {'x': 1, 'z': 3 }
		b = {'y': 2, 'z': 4 }
		c = ChainMap(a,b)
		print(c['x'])      # Outputs 1  (from a)
		print(c['y'])      # Outputs 2  (from b)
		print(c['z'])      # Outputs 3  (from a)
当多个dict有相同元素是它会优先输出前面dict的,利用这一特性可以实现对local,global,builtin变量的访问,ChainMap只是一个保存dict的list,改变它的值会影响原本dict的值.也可以使用`dict1.update(dict2)`来连接两个dict,但这样会创建一个全新的dict,它将dict1与dict2结合然后赋值给dict1.