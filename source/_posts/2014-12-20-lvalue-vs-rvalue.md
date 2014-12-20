title: 左值 vs 右值
date: 2014-12-20 08:32:34
categories:
tags: C++
---
## 定义

C++表达式要么是右值(rvalue)，要么是左值(lvalue)。这两个名词是从C语言中继承过来的，
原本是为了帮助记忆，左值可以放在赋值语句的左边，而右值不可以。

当一个对象被用作右值的时候，用的是对象的值（内容）；当对象被用作左值的时候，用的是
对象的身份（在内存中的位置）。
<!--more-->

来自[n3055](http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2010/n3055.pdf)的解释

> * An **lvalue** (so-called, historically, because lvalues could appear on the left-hand
> side of an assignment expression) designates a function or an object. Example:
> If E is an expression of pointer type, then *E is an lvalue expression referring
> to the object or function to which E points. As another example, the result of
> calling a function whose return type is an lvalue reference is an lvalue.
> 
> * An **rvalue** (so-called, historically, because rvalues could appear on the right-hand
> side of an assignment expression) is an xvalue, a temporary object or subobject
> thereof, or a value that is not associated with an object.

## 例子

那么哪些表达式是左值，哪些又是右值呢？

左值例子：

* 变量
* 返回左值引用的函数
* 赋值运算符`=`
* 下标运算符`[]`
* 解引用运算符`*`
* **前置**递增/递减运算符`++` `--`

右值例子：

* 返回非引用类型的函数
* 算术运算符`+` `-` `*` `/`
* 关系运算符`>` `<` `==` `!=`
* 位运算符
* 后置递增/递减运算符`++` `--`

考察左值与右值表达式的列表，两者的区别就比较明显了：左值具有持久的状态，而右值要么是字面常量，
要么是在表达式求职过程中创建的临时对象。

## 左值引用和右值引用

左值引用`&`就是我们平时说的引用（主要是为了与右值引用区分开来），只能绑定到一个左值上；而右值
引用`&&`只能绑定到一个右值上。这里有一个特殊情况，就是const的左值引用可以绑定到一个右值上。下面
是具体的例子：

```C++
int i = 42;
int &r = i;             //正确：r引用i
int &&r = i;            //错误：不能将一个右值引用绑定到一个左值上
int &r2 = i * 42        //错误：i*42是一个右值
const int &r3 = i * 42  //正确：可以将一个const的引用绑定到一个右值上
int &&rr2 = i * 42      //正确
```

右值引用主要用于移动对象，这是C++11新标准的一个特性。在很多情况下，对象的赋值或构造都会发生
拷贝操作。在某些情况下，对象拷贝后就会立即被销毁。在这些情况下，移动而非拷贝对象会大幅度的
提升性能。而右值引用指向的都是将要被销毁的对象，因此，我们可以从绑定右值引用的对象“窃取”状态。
详细内容可以参考《C++ Primer, 5th》中13.6章节。