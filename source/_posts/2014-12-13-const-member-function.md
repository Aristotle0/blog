title: const成员函数
date: 2014-12-13 22:06:04
tags: C++
description:
---
const成员函数重要的原因：

* 使class接口比较容易理解，得知哪个函数可以修改对象而哪个不可以。
* 使操作const对象成为可能

<!--more-->

两个成员函数如果只是常量性不同，可以被重载，例如：

```C++
class TextBlock {
public:
    ...
    const char& operator[](std::size_t position) const
    { return text[position];}
    char & operator[](std::size_t position)
    { return text[position];}
private:
    std::string text;
};
```
为什么需要两个版本的operator[]，因为当对象是const时，必须调用const版本的operator[]。
例如下面这个例子：

```C++
void print(const TextBlock &ctb) {
    std::cout << ctb[0];
    ...
}
```
因为它是low-level const所以必须有low-level const与之对应才可以。

对于简单的只有几行代码的成员函数这样写没有问题，但如果不是，导致两个版本
的成员函数有大量的重复，那么就需要运用const成员函数实现non-const成员函数。具体做法如下：

```C++
class TextBlock {
public:
    ...
    const char &operator[](std::size_t position) const //和原来一样
    {
        ...
        return text[position];
    }
    char &operator[](std::size_t position)
    {
        return
            const_cast<char&>(
                static_cast<const TextBlock&>(*this)[position]);
    }
    ...
};
```
这里做了两次转型，第一次将*this从原始的TextBlock转型为Const TextBlock，使得
可以调用const版本的operator[]；第二次是从const operator[]返回值中移除const。

如果反过来，用const成员函数调用non-const成员函数，就不能保证不改动对象，所以
不能反过来使用。

