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

## 条款04

确定对象被使用之前已先被初始化（Make sure that objects are initialized before they're used.）

对于一个整型变量，如果是一个普通语句，那么如果不给这个x进行赋初始值，那么编译器就会给这个x自动赋个0；

但是如果这个x是在对象当中以成员变量存在的话，就没有办法保证了。

因此，最好的处理方法就是，永远在使用对象之前，先将它初始化。

对于内置的成员类型，比如int、double、指针等，需要手工去初始化。

对于内置类型以外的其他东西，初始化就需要在构造函数当中进行，确保每一个构造函数都将对象的每一个成员初始化。

在使用构造函数初始化时需要注意

```c++
Test::Test(int x){
    _x = x;
}
```

这种写法不是初始化，而是赋值。这个构造函数首先为成员变量设置初始值，然后才为他们赋值成x。

下面是成员初始值列（member initialization list）的做法

```c++
Test::Test(int x)
    :_x(x)
{}
```

这个才是初始化。

不同编译单元内定义之non-local static对象的初始化次序。

函数内的static对象成为local static；其他的成为non-local static对象

编译单元就是产出单一目标文件的源码。就是一个同名的.h和.cpp

那么上述情况就至少会包括两个不同命的源码了。每一个内含至少一个non-local static对象。

那么问题就是：如果某编译单元内的某个non-local static对象的初始化动作使用了另一个编译单元的某个non-local static对象，那么它使用到的这个对象可能尚未被初始化。

```c++
class FileSystem{
    public:
    std::size_t numDisks() const;
};
extern FileSystem tfs;
```

```c++
class Directory{
    public:
    Directory(params);
    ...
};
Directory::Directory(params){
    ...
    std::size_t disks = tfs.numDisks();
}
```

上述这种情况，Directory中成员函数可能会引用到未被初始化的tfs。

可以采用单例模式的手法来调用tfs。也就是通过一个函数来调用这个变量。这个函数返回一个引用来指向这个变量。

```c++
class FileSystem{...};
FileSystem& tfs(){
    static FileSystem fs;
    return fs;
}
class Directory{...};
Directory::Directory(params){
    ...
    std::size_t disks = tfs().numDisks();
    ...
}
Directory& tempDir(){
    static Directory td;
    return td;
}
```

这样就采用函数来对tfs进行调用，他们使用函数返回的“指向static对象”的reference，而不再使用static对象本身。

```c++
静态局部变量的说明：
(1) 静态局部变量在静态存储区内分配存储单元。在程序整个运行期间都不释放。而自动变量（即动态局部变量）属于动态存储类别，存储在动态存储区空间(而不是静态存储区空间)，函数调用结束后即释放。

(2) 为静态局部变量赋初值是在编译时进行值的，即只赋初值一次，在程序运行时它已有初值。以后每次调用函数时不再重新赋初值而只是保留上次函数调用结束时的 值。而为自动变量赋初值，不是在编译时进行的，而是在函数调用时进行，每调用一次函数重新给一次初值，相当于执行一次赋值语句。

(3) 如果在定义局部变量时不赋初值的话，对静态局部变量来说，编译时自动赋初值0(对数值型变量)或空字符(对字符型变量)。而对自动变量来说，如果不赋初 值，则它的值是一个不确定的值。这是由于每次函数调用结束后存储单元已释放，下次调用时又重新另分配存储单元，而所分配的单元中的值是不确定的。

(4) 虽然静态局部变量在函数调用结束后仍然存在，但其他函数是不能引用它的，也就是说，在其他函数中它是“不可见”的。
```

总结：

1. 为内置的变量进行手工初始化，c++不保证初始化它们
2. 构造函数最好使用成员初值列，不要再构造函数内使用赋值操作，初值列列出的成员变量排序次序应该和它们在class中的声明次序相同
3. 为免除“夸编译单元之初始化次序”问题，请以local static对象替换non-local static对象。

## 条款05

了解C++默默编写并调用哪些函数（Know what functions C++ silently writes and calls）

如果创建了一个完全空的类，里面没有任何东西，当C++处理过它之后，编译器就会为它声明：

1. copy构造函数
2. copy assignment操作符
3. 析构函数
4. default构造函数

```c++
class Empty{};

class Empty{
public:
    Empty(){...}	// default constructor
    Empty(const Empty& rhs) {...}	// copy constructor
    ~Empty(){...}	// destructor
    Empty& operator=(const Empty& rhs){...}	// copy assignment operator
};

// 只有当上述的这些函数需要被调用，他们才会被编译器创建出来
Empty e1; // default constructor
			// destructor
Empty e2(e1);	// copy constructor
e2 = e1	// copy assignment operator
```

default constructor 和 destructor会调用基类和non-static成员变量的构造函数和析构函数

对于内含reference成员和const成员的类，编译器就会拒绝生成相应函数。

如果基类将copy assignment operator声明为private，那么派生类将不会生成对应操作符。

## 条款06

若不想使用编译器自动生成的函数，就该明确拒绝（Explicitly disallow the use of comiler-generated functions you do not want.）

如果不想对象被复制，那么可以通过将copy constructor和copy assignment operator声明为private。编译器就不会创建相应的函数了。但是这种方法不绝对安全，因为成员函数和友元函数还是可以调用私有成员。可以声明上述构造函数和操作符，但不去定义他们。

上述做法就是生成了一个链接期的错误，因为调用上述函数（只有声明，没有定义）的话会报链接错误。

如果想在编译器就做上述的限制，只要将构造函数和操作符在一个专门为了阻止copying动作而设计的基类当中进行私有声明。

```c++
class Uncopyable{
protected:	// 允许派生对象构造和析构
    Uncopyable(){}
    ~Uncopyable(){}
private:	// 但是拒绝copying
    Uncopyable(const Uncopyable&);
    Uncopyable& operator=(const Uncopyable&);
}
```

只要继承这个Uncopyable类，这个对象就会被阻止拷贝。包括成员函数和友元函数也是如此。

总结：

不需要编译器自动提供的功能的话，可以将相应的成员函数声明成private且不进行实现。

使用Uncopyable这样的基类也可以。

## 条款07

为多态基类声明virtual析构函数（Declare destructors virtual in polymorphic base classes.）

如果派生类对象，由一个基类对象指针进行引用，且通过该基类指针来删除这个派生对象时。如果基类对象当中有non-virtual析构函数，那么这个删除操作，会导致对象的派生部分没被销毁，而且派生类的析构函数也没能只能。这就造成了一个“局部销毁”的对象。

解决方法就是给基类一个virtual析构函数。此后删除派生类就会正常了。

任何类，只要带有virtual函数都几乎确定应该也有一个virtual析构函数。

如果一个类不被当作基类的话，那么让它的析构函数变成virtual是个不好的做法。因为，如果一个对象中包含了虚函数，这个对象的体积就会增加。

“只有当类内存在至少一个virtual函数，才为它声明virtual析构函数”



有一个情况，如果你希望创建一个抽象类，但是这个类中现在想不到任何纯虚函数。可以声明一个纯虚析构函数

```c++
class AWOV{
    public:
    virtual ~AWOV() = 0; // pure virtual
};
```

同时，需要给这个析构函数提供一个定义

```c++
AWOV::~AWOV(){}
```

析构函数的运作方式是：最底层的类的析构函数最先被调用，然后是每一个基类的析构函数被调用。

总结：

1. polymorphic（带多态性质的）基类应该声明一个virtual 析构函数。如果类带有任何虚函数，那么这个类就用该拥有一个虚析构函数
2. 如果类不是作为基类或者不是为了具备多态性，就不应该声明虚析构函数。

## 条款08

别让异常逃离析构函数（Prevent exceptions from leaving destructors.）

不要让析构函数抛出异常。

如果非要这么做，有两个方法：

1. 在catch中调用abort()函数来直接结束程序。
2. 直接catch

总结：

1. 析构函数绝对不要抛出异常。如果一个被析构函数调用的函数可能抛出异常，应当由析构函数来catch这个异常，然后吞下他们，或结束程序。
2. 异常应该在普通函数中被捕捉。
