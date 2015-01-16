title: 模板的声明和定义能否分离
date: 2014-12-20 10:24:05
categories:
tags: C++
---

这个问题是我在做《C++ Primer, 5th》上的一个习题时遇到的。我当时把template
的声明放在了`.h`文件中，而把成员函数的定义放在`.cc`文件中，结果在编译链接时
一直报错：

```bash
ld: symbol(s) not found for architecture x86_64
clang: error: linker command failed with exit code 1 (use -v to see invocation)
```

我原本以为是`g++`的链接出了问题，后来google发现原来是template的定义和声明
必须都放在`.h`头文件中。接下来会详细说一下这个问题。

参考文献：

* [C++ FAQ](http://www.parashift.com/c++-faq/templates-defn-vs-decl.html)
* [知乎](http://www.zhihu.com/question/20630104)

<!--more-->
## 为什么模板的声明和实现（成员函数的定义）要放在一个头文件中？

首先，我们要知道以下事实：

* 模板(template)不是一种类或者函数。模板是一种“蓝图”或“公式”，可以用来创建类似的
函数或类。
* 为了能让编译器产生代码，它需要看到模板的定义（而不仅仅是声明）以及用来实例化
模板的具体类型。例如，如果你想使用`Foo <int>`，那需要让编译器同时看到`Foo`模板
和你调用的具体的`Foo <int>`。
* 编译器当编译一个`.cc`文件时，就大多数情况下可能不会记得另一个`.cc`文件的细节。
也就是说，同一时刻编译器只能专注于一个`.cc`文件。这被称作“分离编译模型”
(separate compilation model)。

基于以上事实，我们来看下面这个例子，假设我们需要定义`Foo`这个模板

```C++
template<typename T> 
class Foo {
    public: Foo(); 
    void someMethod(T x); 
private: 
    T x; 
};
```
而它的成员函数的定义

```C++
template<typename T>
Foo<T>::Foo()
{
  ...
}

template<typename T>
void Foo<T>::someMethod(T x)
{
  ...
}
```
假设有程序`bar.cc`使用`Foo <int>`

```C++
// bar.cc 
void blah_blah_blah() {
    ... 
    Foo<int> f; 
    f.someMethod(5); 
    ... 
}
```

如果你把模板的声明放到`Foo.h`，而把它的成员函数的定义放到`Foo.cc`中，
当编译器编译`Foo.cc`文件时，它能看到完整的模板代码(`Foo.h`+`Foo.cc`)；当编译器
编译`bar.cc`文件时，它能看到实例化`Foo <int>`和模板的声明(`Foo.h`)。但编译器
永远不可能同时看到实例化和完整的模板代码。这就和上面的事实第二条冲突了，也就不会
产生`Foo <int> ::someMethod`。

[余天升](http://www.zhihu.com/people/flily)的解释：

> C++中每一个对象所占用的空间大小，是在编译的时候就确定的，在模板类没有真正的被使用之前，
> 编译器是无法知道，模板类中使用模板类型的对象的所占用的空间的大小的。
> 只有模板被真正使用的时候，编译器才知道，模板套用的是什么类型，应该分配多少空间。
> 这也就是模板类为什么只是称之为模板，而不是泛型的缘故。
> 
> 既然是在编译的时候，根据套用的不同类型进行编译，那么，套用不同类型的模板类实际上
> 就是两个不同的类型，也就是说，`stack<int>`和`stack<char>`是两个不同的数据类型，
> 他们共同的成员函数也不是同一个函数，只不过具有相似的功能罢了。
> ![](/img/121915_template.jpeg)
> 
> 所以模板类的实现，脱离具体的使用，是无法单独的编译的；把声明和实现分开的做法也是
> 不可取的，必须把实现全部写在头文件里面。为了清晰，实现可以不写在class后面的花括
> 号里面，可以写在class的外面。

[徐辰](http://www.zhihu.com/people/xu-chen-5)的解释：

> C++中的template可以看作一种类型安全的宏，从这个角度来说其实template的实现
> 必须要放在使用它的源代码里一起编译，通常我们就用头文件来解决这个问题。
> 每个使用template的源代码在编译的时候都需要把template的源代码整个重新编译一遍，
> 生成的目标代码中其实已经完全没有template的任何信息了（除了mangle过的符号名字
> 还能看到一些线索）。
> 
> 这样做的后果也很明显，那就是编译时间明显变长，为了解决这个问题，
> C++标准委员会在没有经过深思熟虑的情况下在C++03中加入了一个`export`关键字，
> 试图为各厂家解决这个问题的方式提供一个标准的框架，但是export的语义从来没有被明确过，
> 标准文本中只是很不负责任的说了一句用`export`声明的template可以分离编译，
> 没有提及任何具体实现方案。
> 问题在于`export`仅仅作为一个编译性能补丁被引入的，而这个性能问题很多编译器厂商
> 已经用预编译头文件的方式基本解决了，包括Microsoft Visual C++和GCC
> （我会告诉你GCC用mmap实现的预编译头文件补丁就是渣么:-），唯一符合标准实现只有
> 的EDG的前端，使用这个前端的编译器只有Comeau和Intel。
> 在接下来的7年里，C++的标准委员会对于`export`的语义进行了多次讨论，最后达成共识，
> 认定export对于编译器没有实质性的帮助，最终在C++11中去除了。
> 
> 目前你能做的就是，在头文件里定义整个template，但是可以把显式特化放在源代码里。

## 如果非要把模板的成员函数的定义单独放在`.cc`文件中，有什么办法？ 

必须强调的是：

* *目前，最好的方式还是把整个模板的定义和实现都放在一个头文件中。*
* *以下方式基本上只会让你的程序变得混乱，除非万不得已，还是不用为好。*

可以将模板的成员函数的定义放在`.cc`文件中，然后添加一行实例化声明。以上面为例，
将成员函数放到`Foo.cc`文件中，在最后添加一行：

```C++
template class Foo<int>;
```
或者只是将成员函数放到`Foo.cc`文件中，然后新建一个文件，例如`Foo-impl.cc`
```C++
// Foo-impl.cc
#include "Foo.cc"
template class Foo<int>;
```







