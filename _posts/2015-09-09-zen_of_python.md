---
title: The Zen of Python 
layout: post
tags:
  - python
---

今天在看[利用Python进行数据分析](https://book.douban.com/subject/25779298/)时发现了Python的一个彩蛋:

{% highlight python %}
import this
{% endhighlight %}

> The Zen of Python, by Tim Peters
> 
> Beautiful is better than ugly.
> 
> Explicit is better than implicit.
> 
> Simple is better than complex.
> 
> Complex is better than complicated.
> 
> Flat is better than nested.
> 
> Sparse is better than dense.
> 
> Readability counts.
> 
> Special cases aren't special enough to break the rules.
> 
> Although practicality beats purity.
> 
> Errors should never pass silently.
> 
> Unless explicitly silenced.
> 
> In the face of ambiguity, refuse the temptation to guess.
> 
> There should be one-- and preferably only one --obvious way to do it.
> 
> Although that way may not be obvious at first unless you're Dutch.
> 
> Now is better than never.
> 
> Although never is often better than *right* now.
> 
> If the implementation is hard to explain, it's a bad idea.
> 
> If the implementation is easy to explain, it may be a good idea.
> 
> Namespaces are one honking great idea -- let's do more of those!

下面是`this`的源代码:

{% highlight python %}
s = """Gur Mra bs Clguba, ol Gvz Crgref

Ornhgvshy vf orggre guna htyl.
Rkcyvpvg vf orggre guna vzcyvpvg.
Fvzcyr vf orggre guna pbzcyrk.
Pbzcyrk vf orggre guna pbzcyvpngrq.
Syng vf orggre guna arfgrq.
Fcnefr vf orggre guna qrafr.
Ernqnovyvgl pbhagf.
Fcrpvny pnfrf nera'g fcrpvny rabhtu gb oernx gur ehyrf.
Nygubhtu cenpgvpnyvgl orngf chevgl.
Reebef fubhyq arire cnff fvyragyl.
Hayrff rkcyvpvgyl fvyraprq.
Va gur snpr bs nzovthvgl, ershfr gur grzcgngvba gb thrff.
Gurer fubhyq or bar-- naq cersrenoyl bayl bar --boivbhf jnl gb qb vg.
Nygubhtu gung jnl znl abg or boivbhf ng svefg hayrff lbh'er Qhgpu.
Abj vf orggre guna arire.
Nygubhtu arire vf bsgra orggre guna *evtug* abj.
Vs gur vzcyrzragngvba vf uneq gb rkcynva, vg'f n onq vqrn.
Vs gur vzcyrzragngvba vf rnfl gb rkcynva, vg znl or n tbbq vqrn.
Anzrfcnprf ner bar ubaxvat terng vqrn -- yrg'f qb zber bs gubfr!"""

d = {}
for c in (65, 97):
    for i in range(26):
        d[chr(i+c)] = chr((i+13) % 26 + c)

print "".join([d.get(c, c) for c in s])
{% endhighlight %}

s是The Zen of Python经过ROT13加密后的字符串,ROT13将每个字符表示为它后移13位后的字符,有趣的是,这个代码完全违反了诗中的规则: )




