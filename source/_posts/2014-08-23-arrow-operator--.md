title: 'Arrow Operator ->'
date: 2014-08-23 08:17:59
tags: C++
description:
---

这两天看到《C++ Primer》重载运算符部分，其他的都还简单，就是成员访问运算符稍显复杂，特别是箭头运算符`->`。
<!-- more -->

```cpp
class StrBolbPtr {
public:
    std::string &operator*() const {
        auto p = check(curr, "dereference past end");
        return (*p)[curr];
    }
    std::string *operator->() const {
        return &this->operator*();
    }
    ...
}
```
其中`->`函数的返回语句可以这样理解：对`StrBolbPtr`类的一个对象调用解引用运算符`*`，对返回值取地址，得到一个指向该地址的指针。
```cpp
StrBlob a1 = {"h1", "bye", "now"};
StrBlobPtr p(a1);
cout << p->size() << endl;
cout << (*p).size() << endl;
```
从这里看不到与声明的对应，就像`a+b`形式的加运算符，编译器会解释为`a.operator+(b)`；那么`a->size()`应该解释为`a.operator->(size())`才对，但声明中并没有参数，而是直接返回一个指针，这是为什么呢？

《C++ Primer》中给出的解释：
> 对于形如`point->mem`的表达式来说，`point`必须是指向类对象的指针或者是一个重载了`operator->`的类的对象。

> 1. 如果`point`是指针，则应用**内置**的箭头运算符，表达式等价于`(*point).mem`。首先解引用该指针，然后从所得的对象中获取指定的成员。
2. 如果`point`是定义了`operator->`的类的一个对象，则使用`point->operator->()`的结果来获取`mem`。其中，如果该结果是一个指针，则执行第一步；如果该结果本身含有重载的`operator->()`， 则重复第二步。

这样，我们重新对`p->size()`解释：

1. 首先执行上述第二步，调用`p.operator->()`返回指向`a1`的首元素的指针。
2.  然后执行第一步，应用`string`内置的箭头运算符，取内置类型`string`的成员函数`size()`。

这里再给出一个两层箭头运算符的例子，更好的说明第二步[1]

```cpp
#include <iostream>

struct Origin {
    int a;
};

struct Wrapper {
    Origin *orig;
    Origin *operator->() const {
        return orig;
    }
};

struct Wrapper2 {
    Wrapper *wrap;
    Wrapper& operator->() const {
        return *wrap;
    }
};

int main() {
    Origin o;
    o.a = 7;
    Wrapper w;
    w.orig = &o;
    Wrapper2 w2;
    w2.wrap = &w;
    std::cout << "w->a" << "\t\t\t\t" << w->a << std::endl;
    std::cout << "w.operator->()" << "\t\t\t" << w.operator->() << std::endl;
    std::cout << "w.operator->()->a" << "\t\t" << w.operator->()->a << std::endl;
    std::cout << "w2->a" << "\t\t\t\t" << w2->a << std::endl;
    std::cout << "&w2.operator->()" << "\t\t" << &w2.operator->() << std::endl;
    std::cout << "w2.operator->()->a" << "\t\t" << w2.operator->()->a << std::endl;
    std::cout << "w2.operator->().operator->()" << "\t" << w2.operator->().operator->() << std::endl;
    std::cout << "w2.operator->().operator->()->a" << "\t" << w2.operator->().operator->()->a << std::endl;
}
```
运行结果为
```
w->a                7
w.operator->()          0x7fff6b46d6a0
w.operator->()->a       7
w2->a               7
&w2.operator->()        0x7fff6b46d690
w2.operator->()->a      7
w2.operator->().operator->()    0x7fff6b46d6a0
w2.operator->().operator->()->a 7
```
重点是`w2->a`，它相当于`w2.operator->().operator->()->a`。具体过程就是：先应用`Wrapper2`的成员函数`operator->()` 得到一个`Wrapper1`类的对象，然后继续应用`Wrapper1`的成员函数`operator->()`得到一个指针，最后获取指针指向`Origin`类的对象的数据成员`a`。

[1]: http://kelvinh.github.io/blog/2013/11/20/overloading-of-member-access-operator-dash-greater-than-symbol-in-cpp/



