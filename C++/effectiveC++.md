# Effective C++

## 条款01

**视C++为一个语言联邦（View C++ as a federation of languages.）**

C++是一个多重范型编程语言，包括过程、面向对象、函数、泛型、元编程

将C++视为一个由相关语言（次语言）组成的联邦，而不是单一的一个语言。因此，在C++代码当中，是可以存在不同的编程范式的。对于这些次语言，只需要记住4个：

1. C。 这是基础
2. Object-Oriented C++。 面向对象的C with Classes
3. Template C++。 C++的泛型编程。template metaprogramming（TMP，模板元编程）这个元编程就是编写代码的代码。因为一类泛型相关代码可以让编译器生成不同的拥有实际类型的代码，所以叫做“meta”。
4. STL。 这是一个template程序库。

综上，C++不是一个带有一组守则的一体语言，而是4个次语言组成的联邦语言。C++的高效编程守则视状况而变化，这取决于你使用的是哪个次语言。

## 条款02

**尽量以const, enum, inline替换#define（Prefer const, enums, and inlines to #defines）**

“宁可以编译器替换预处理器”（尽量交给编译器）

因为或许#define不被视为语言的一部分，因为在预处理器当中，#define的类型会被直接替换，所以编译器是没办法看见#define出来的类型的。

那么如果出现编译错误，这个错误当中就不会出现你定义的这个#define的记号名。

解决方法就是用一个常量来进行替换。有两种特殊情况。

第一，在定义常量指针的时候。由于常量定义通常是在头文件当中，所以有必要也将指针声明为const

```c++
const char* const str = "helloworld";
```

这个情况下，指针和指针指向的地址中的内容，都不会被改变。

第二，在类的成员变量当中使用常量的时候，说明这个常量在所有的类实例当中都是相同的，那么这个时候可以直接把这个常量用static来修饰

```c++
class test{
    static const int batch = 1;
    int scores[batch];
};
```

对于上述这个代码，如果说编译器的版本较老的话，可能会不允许给batch一个初始值。

这个时候就需要直接在实现文件.cpp当中给batch进行赋初始值。

或者，将batch变成enum类型。

```c++
class test{
    enum {batch = 1};
    int scores[batch];
}
```

这个做法叫”the enum hack“补偿做法。理论基础是：一个属于枚举类型的数值可权充ints被使用。

如果不想让别人获得一个pointer或者reference指向某个整数常量，就可以使用enum来实现这个约束。



还有一个#define常见的误用情况是用它来实现macros。宏看起来跟函数差不多，但是它不会导致函数调用。因为是直接替换的。

最好的做法是使用模板来代替宏。同时使用inline关键字来进行修饰。

综上：对于单纯常量，使用const或enum来替换#define。

对于形式函数的宏，使用inline函数替换#define。

## 条款03

**尽可能使用const（use const whenever possible）**

const允许programmer指定一个语义约束，也就是指定一个“不应该被改动”的对象。编辑器会强制实时这个约束。

const可以修饰很多东西

如果const出现在\*左边，表示被指物是常量。如果出现在\*右边，表示指针自身是常量。如果两边都出现了，表示被指物和指针都是常量

int* const a = 0; // 指针是常量 a = &i 会报错

const int* a = 0; // 被指物是常量 *a = 1会报错

const int* const a = 0; // 两者都是常量

上面这个例子说明，指针和const一起用的时候可能有点反直觉。注意区分。

const最具威力的用法是在函数声明时的应用。

const可以和函数返回值、各个参数、函数自身（成员函数）产生关联。

1.对函数返回值进行修饰。可以降低因客户错误而造成的意外，而又不至于放弃安全性和高效性。

```c++
class Rational {...};
const Rational operator* (const Rational& lhs, const Rational& rhs);
```

上述代码对*运算符进行了限定，这个时候，下面的代码就会报错

```c++
Rational a, b, c;
...
(a * b) = c; // 因为 a * b 返回的是const对象，无法对其进行赋值，这个时候这行语句就会报错
```

2.对参数进行修饰。没有什么特别的，参数不能修改。

3.对成员函数进行修饰。为了确认该成员函数可以作用于const对象身上。也就是，这个函数可以操作const对象。

当const和non-const成员函数有着实质等价的实现时，令non-const版本调用const版本可以避免代码重复。

```c++
const_iterator find(const T& value) const
{
    // actual implementation of find
    // 真正的实现在 const 版本的函数中实现
}

iterator find(const T& value)
{
    return const_cast<iterator>(static_cast<const container*>(this)->find(value));
    // const_cast<>：消除常量性
    // static_cast<const container*>：增加常量性
}
```

这种情况，如果是非常量调用find的话，因为return的返回值是把常量给转除了，所以是可以的。

如果是常量调用find，那么直接调用的就是上面那个版本了。
