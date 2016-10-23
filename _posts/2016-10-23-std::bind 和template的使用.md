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
