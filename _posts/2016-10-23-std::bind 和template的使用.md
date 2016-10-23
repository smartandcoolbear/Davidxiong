---
title: std::bind 和template的使用
layout: post
tags:
  - C++
---



接上次的笔记，继续整理std::bind,std::function等函数，以及template的一些使用说明。


相关链接：



1.[模板(Templates)](http://www.prglab.com/cms/pages/c-tutorial/advanced-concepts/templates.php)


2.[C++中的类模板详细讲述](http://www.cnblogs.com/assemble8086/archive/2011/10/02/2198308.html)


3.[C++11中的std::bind](http://www.jellythink.com/archives/773)

4.[什么是函数式编程思维？](https://www.zhihu.com/question/28292740)


# Template的理解与使用


使用模板技术其实原因很简单，就是为了泛型编程。主要可以分为模板函数和模板类两个方面。具体来说，模板(Templates)使得我们可以生成通用的函数或类，这些函数或类能够接受任意数据类型的参数，可返回任意类型的值，而不需要对所有可能的数据类型进行函数重载。这在一定程度上实现了宏（macro）的作用。它们的原型定义可以是下面两种中的任何一个：

    template <class identifier> function_declaration;
    template <typename identifier> function_declaration;

上面两种原型定义的不同之处在关键字class 或 typename的使用。它们实际是完全等价的，因为两种表达的意思和执行都一模一样。

举例来说：

    template<class 模板参数表>
    class 类名｛
    // 类定义．．．．．．
    };


template 是声明类模板的关键字，表示声明一个模板，模板参数可以是一个，也可以是多个，可以是类型参数 ，也可以是非类型参数。类型参数由关键字class或typename及其后面的标识符构成。非类型参数由一个普通参数构成，代表模板定义中的一个常量。


模板函数：

    #include <iostream.h>
    template <class T> T GetMax (T a, T b) {
    T result;
    result = (a>b)? a : b;
    return (result);
    }

    int main () {
      int i=5, j=6, k;
      long l=10, m=5, n;
      k=GetMax(i,j);
      n=GetMax(l,m);
      cout << k << endl;
      cout << n << endl;
      return 0;
    }

类模板：

    #include <iostream.h>
    template <class T> class pair {
         T value1, value2;
    public:
        pair (T first, T second) {
            value1=first;
            value2=second;
        }
        T getmax ();
    };
    template <class T>
    T pair::getmax (){
        T retval;
        retval = value1>value2? value1 : value2;
        return retval;
    }
    int main () {
        pair myobject (100, 75);
        cout << myobject.getmax();
        return 0;
    }
另外，像上文所说，模板中可以传入非类型参数，也就是说有些常量，函数等也可直接传入。具体的传入形式包括：

    template <class T> // 最常用的：一个class 参数。
    template <class T, class U> // 两个class 参数。
    template <class T, int N> // 一个class 和一个整数。
    template <class T = char> // 有一个默认值。
    template <int Tfunc (int)> // 参数为一个函数。
    
    
# std::bind的理解与使用


其实std::bind， std::functionu应该和上篇文章的lambda表达式一起讲，因为这几个最重要的用途都是一致的：用以处理回调函数。


要讲这个之前，先要说明一个概念：闭包（closure）。这个概念在js,python中都很常见了，但是在c++中提出来的时间不长。引用维基百科的说法：

    A closure (also lexical closure, function closure or function value) is a function together with  
    a referencing environment for the non-local variables of that function.  
    
也就就说：闭包 = 一个函数 + 它所依赖的环境（不需是函数内部的变量）。这就说是，这个函数不仅可以访问本地变量，还可以访问闭包内的外部变量。哟，厉害了我的哥。换句话说，只要我们通过某些方法构建了一个闭包（利用用lambda表达式捕获了外部变量），闭包内的这个函数就拥有了访问reference environment的权利，可以用来实现相应的功能。有的小伙伴就不同意了：“这个函数凭什么可以访问外部变量？它的生命周期是什么样？”，其实对于这个问题，最重要的就是理解js里面很重要的一个概念：“作用域”。 我再把js里面闭包的概念说一遍：“闭包是一种特殊的对象。它由两部分构成：函数，以及创建该函数的环境。环境由闭包创建时在作用域中的任何局部变量组成。”简而言之，这些函数可以“记忆”它被创建时候的环境。我猜想，针对在闭包中的函数，闭包其实会创建一个类，把整个闭包（整个作用域）里的变量都存放起来，作为这个函数可以访问的对象。

理解了闭包之后，我们再来看看std::bind这个函数的作用。


bind是这样一种机制，它可以预先把指定可调用实体的某些参数绑定到已有的变量，产生一个新的可调用实体。这句话前半句没什么意思，重要的就是后半句。大声告诉我，可以产生一个什么玩意儿？！可调用实体！这就摆明了跟你说，这个是拿来做回调用的嘛。而且这个可调用实体是什么类型，就是一个std::function对象！


又有些朋友要问了，这个我用函数指针也能办到啊，那我就再解释清楚一点：function模板类和bind模板函数，使用它们可以实现类似函数指针的功能，但却比函数指针更加灵活，特别是函数指向类的非静态成员函数时。std::function可以绑定到全局函数/类静态成员函数(类静态成员函数与全局函数没有区别),如果要绑定到类的非静态成员函数，则需要使用std::bind。


再给一个例子：

    // in foo.h
    #pragma once
    #include <iostream>
    class Foo
    {
    public:
    void f1()
    {
    std::cout << "Foo::f1()" << std::endl;
    }

    static void f2()
    {
    std::cout << "static Foo::f2()" << std::endl;
    }
    };

    // in main.cpp
    int main()
    {
    Foo foo;
    std::function<void(void)> fs = &Foo::f2; // 这可以赋值，编译通过。
    fs();
    std::function<void(Foo*)> ff = &Foo::f1; // 这里就不行了，因为std::function绑定了一个非静态变量。
    ff(&foo);
    return 0;
    }
    
对于这个不行的应该怎么办呢？我们可以这样：

    std::function<void(Foo*)> ff = std::bind(&Foo::f1);  //接收一个对象
    ff(&foo);  //传入一个实例的引用

    
也可以这样：

    std::function<void(void)> ff = std::bind(&Foo::f1,&foo);  //直接绑定一个实例对象的引用
    ff();
    
还可以这样：

    std::function<void(Foo*)> ff = [](Foo* foo){ foo->f1() };  //利用Lambda表达式

这说明了什么？说明std::bind， std::function, lambda表达式都是生成一个可调用的对象，都是构造一个闭包，都是用于函数回调。


通过上面的介绍，我们基本了解了function, bind和lambda的用法，把三者结合起来，C++将会变得非常强大，有点函数式编程的味道了。最后，这里再补充一点，对于用bind来生成function和用lambda表达式来生成function, 通常情况下两种都是ok的，但是在参数多的时候,bind要传入很多的std::placeholders，而且看着没有lambda表达式直观，所以通常建议优先考虑使用lambda表达式。


另外，有些同学在这里就懵逼了，什么叫函数式编程？


引用知乎里面轮子哥的话：“函数式编程在使用的时候的特点就是，你已经再也不知道数据是从哪里来了，每一个函数都是为了用小函数组织成更大的函数，函数的参数也是函数，函数返回的也是函数，最后得到一个超级牛逼的函数，就等着别人用他来写一个main函数把数据灌进去了。”


详细一点的解释就是：函数式编程是一种编程范式，我们常见的编程范式有命令式编程（Imperative programming），函数式编程，逻辑式编程，常见的面向对象编程是也是一种命令式编程。


命令式编程是面向计算机硬件的抽象，有变量（对应着存储单元），赋值语句（获取，存储指令），表达式（内存引用和算术运算）和控制语句（跳转指令），一句话，命令式程序就是一个冯诺依曼机的指令序列。


而函数式编程是面向数学的抽象，将计算描述为一种表达式求值，一句话，函数式程序就是一个表达式。


更为具体的解释，可以看知乎的第二个高票回答（用心阁的回答）。
