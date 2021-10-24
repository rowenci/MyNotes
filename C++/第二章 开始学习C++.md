# 第二章 开始学习C++

## 2.1. 进入C++

C语言输入和输出

在C语言当中，一般会引入stdio.h这个头文件，然后使用printf和scanf两个函数来进行输出和输入的操作。但是在C++当中，对C语言进行了很多的改进，所以C++的输入输出就会使用cout和cin来输出输入。

### 2.1.1. main()函数

在c++当中，要求main()函数的定义必须是int类型的。因为main()被启动代码调用，而启动代码是由编译器添加到程序中的，是程序和操作系统之间的桥梁。

可以使用int和void作为main的函数头，但是因为标准限制，如果用void的话，在某些系统当中就无法工作，所以尽量使用int。

### 2.1.2. 注释

// 123123

/* 123123 */

### 2.1.3. C++预处理器和iostream文件

如果c++程序需要使用输入/输出工具，那么就需要在文件开头添加

```c++
#include <iostream>
using namespace std;
```

c++和c一样，也使用一个预处理器在进行主编译前对源文件进行处理。

### 2.1.4. 头文件名

由c语言转换成为c++的头文件一般会在前面加上c，如cmath

c++新式风格的头文件一般没有.h后缀

### 2.1.5. namespace

namespace是用来在组合不同的程序时进行区分的。

比如两个文件当中都有wanda()函数，就得使用namespace来进行区分。

### 2.1.6. 使用cout进行输出

插入运算符：<<

```c++
cout << "come up and c++ me some time."
```

将字符串come up and c++ me some time.插入到输出流中。

这个插入运算符和按位左移运算符一样，属于是运算符的重载。编译器通过上下文来确定运算符的含义。



endl

表示endline，重起一行。导致屏幕光标移到下一行的开头。

因此，endl的意思不是结束输出流，因为cout只是往输出流中插入东西。endl只是结束一行。

endl这种对cout有特殊含义的符号被称为控制符。



换行符\n



## 2.2. C++语句

### 2.2.1. 声明语句和变量

需要注意一点的就是，C/C++都是比较接近底层的语言，最好学习一下计算机组成原理，这样才能更好地理解语法为什么要这样写。

至少得知道各种数据类型所占字节个数。

动态变量和静态变量各有各的好处，如果有其他语言的基础，要随时转换编程思维。

### 2.2.2. 赋值语句

这一点需要注意的就是“左值”和“右值”，只需要记住概念就行。

左值就是被赋值的变量。

右值就是提供值的变量。

有些参数，比如由const修饰的变量为常量。常量就只能当做右值，如果把它当做左值的话就会报错。原因很明显。。。

### 2.2.3. cout的新花样

cout和printf比起来很灵活，因为你不需要一些格式输出，比如“%s”“%d”。

cout << rowenci

上述语句执行了两个步骤。

1.cout将rowenci替换为rowenci所指内存空间当中的数据。

2.cout将数据转换为合适的输出字符，插入输出流当中进行输出。

## 2.3. 其他C++语句

### 2.3.1. 使用cin

cin >> rowenci

输入一个值，然后回车。就会将这个值赋给rowenci变量。

需要注意的只有一点，就是cin需要>>右移运算符。

### 2.3.2. 使用cout进行拼接

三种拼接输出的方法。

cout << "hello " << "world " << rowenci << endl;

***

cout << "hello ";

cout << "world ";

cout << rowenci;

cout << endl;

***

cout << "hello "

​		<< "world "

​		<< rowenci

​		endl;

### 2.3.3. 类简介

不介绍，自己去学OOP相关知识。

需要注意的是

iostream也是一个对象。要想调用cout和cin有两种方法。

第一种是直接调用该类的方法。

第二种就是重新定义运算符。cin和cout就是用的这种方法。

## 2.4. 函数

### 2.4.1. 使用有返回值的函数

C++程序应当为程序中使用的每个函数提供原型。

什么是原型？

double sqrt(double);

这个就是原型。C语言中，如果在main中调用了写在main函数后面的函数，就必须在main函数前面加上这个函数原型，否则会报错。（这是我自己的理解，请指正。）

在程序中使用sqrt时，也必须提供原型。

1.在源代码文件中输入函数原型

2.包含头文件cmath，其中定义了原型

### 2.4.2. 函数变体

有些语言中，有返回值的函数被称为函数，没有返回值的函数被称为过程。但C++和C当中，这两种变体都被称为函数。

### 2.4.3. 用户定义的函数

对于库函数，在使用之前必须提供其原型，通常把原型放到main函数之前。这时候就引入头文件来代替手动输入原型。

而如果是自己定义的函数写在了main函数后面，就需要在前面加上该函数的原型。

### 2.4.4. 用户定义的有返回值的函数

记住函数声明和return就行。

### 2.4.5. 在多函数程序中使用using编译指令

和变量一样，using写在什么地方，什么地方的代码就能访问该命名空间。

如果只需要在main当中访问std命名空间，就只需要在main当中使用using

```c++
#include <iostream>
using namespace std;
int main(){
    int a;
    cin >> a;
    cout << a << endl;
    return 0;
}
```

命名约定：建议驼峰命名法。

## 2.5. 总结

无

## 2.6. 复习题

1. C++程序的模块叫什么？

函数

2. 下面的预处理器编译指令是做什么用的？
   #include <iostream>

使用iostream当中的内容来替换该指令（引入头文件）

3.	下面的语句是做什么用的？
using namespace std;

使用std命名空间。使得程序可以使用std命名空间当中的定义。

4.	什么语句可以用来打印短语“Hello,world"，然后开始新的一行？

cout << "Hello, world" << endl;

5. 什么语句可以用来创建名为cheeses的整数变量？

int cheeses;

6. 什么语句可以用来将值32赋给变量cheeses?

cheeses = 32;

7.	什么语句可以用来将从键盘输入的值读入变量cheeses中？

cin >> cheeses;

8. 什么语句可以用来打印“We have X varieties of cheese,",其中X为变量cheeses的当前值。

cout << "We have " << cheeses << " varieties of cheese" << endl;

9. 下面的函数原型指出了关千函数的哪些信息？
   int froop(double t);

   froop函数的返回值为int，参数类型为double

   void rattle(int n);

   rattle函数没有返回值，参数类型为int

   int prune (void) ;

   prune函数返回值为int，没有参数

10. 定义函数时，在什么情况下不必使用关键字return?

使用int作为返回值。

11. 假设您编写的main(）函数包含如下代码：

    cout << "Please enter your PIN: ";

而编译器指出cout是一个未知标识符。导致这种问题的原因很可能是什么？指出3种修复这种问题的方法。

using namespace std;

#include <iostream>



## 2.7. 编程练习（未验证）

1. 编写一个C++程序， 它显示您的姓名和地址。

```c++
#include <iostream>
#include <string>
using namespace std;
int main(){
    string name;
    string address;
    name = "rowenci";
    address = "ZZU";
    cout << name << "  " << address;
    return 0;
}
```



2. 编写一个C++程序，它要求用户输入一个以long为单位的距离，然后将它转换为码(1 long等千220码）。

```c++
#include <iostream>
using namespace std;
int main(){
    long distance;
    cin >> distance;
    float yard;
    yard = distance / 220;
    cout << yard;
    return 0;
}
```



3. 编写一个C++程序， 它使用3个用户定义的函数（包括main()）， 并生成下面的输出：
   Three blind mice
   Three blind mice
   See how they run
   See how they run
   其中一个函数要调用两次， 该函数生成前两行； 另一个函数也被调用两次， 并生成其余的输出。

```c++
#include <iostream>
using namespace std;
void first(void);
void second(void);
int main(){
    first();
    first();
    second();
    second();
    return 0;
}
void first(){
    cout << "Three blind mice" << endl;
}
void second(){
    cout << "See how they run" << endl;
}
```



4. 编写一个程序， 让用户输入其年龄， 然后显示该年龄包含多少个月， 如下所示：
   Enter your age: 29

```c++
#include <iostream>
using namespace std;
int main(){
    int age, month;
    cin >> age;
    month = age * 12;
    cout << month << endl;
    return 0;
}
```



5. 编写一个程序， 其中的main(）调用一个用户定义的函数（以摄氏温度值为参数， 并返回相应的华氏温度值）。该程序按下面的格式要求用户输入摄氏温度值， 并显示结果：
   Please enter a Celsius value: 20
   20 degrees Celsius is 68 degrees Fahrenheit.
   下面是转换公式：

   华氏温度＝1.8X摄氏温度＋32.0

```c++
#include <iostream>
using namespace std;
float getFahrenheit(float celsius);
int main(){
    float celsius;
    cout << "Please enter a Celsius value: ";
    cin >> celsius;
    cout << celsius << " degrees Celsius is " << getFahrenheit(celsius) << " degrees Fahrenheit." << endl;
    return 0;
}
float getFahrenheit(float celsius){
    float fahrenheit = 1.8 * celsius + 32.0;
    return fahrenheit;
}
```



6. 编写一个程序，其main(）调用一个用户定义的函数（以光年值为参数， 并返回对应天文单位的值）。
   该程序按下面的格式要求用户输入光年值， 并显示结果：
   Enter the number of light years: 4.2
   4.2 light years = 265608 astronomical units.
   天文单位是从地球到太阳的平均距离（约150000000公里或93000000英里）， 光年是光一年走的距离（约10万亿公里或6万亿英里） （除太阳外， 最近的恒星大约离地球4.2光年）。请使用double类型（参见程序清单2.4)， 转换公式为：
   1光年＝63240天文单位

```c++
#include <iostream>
using namespace std;

int main(){
    float lightYear;
    cout << "Enter the number of light years: ";
    cin >> lightYear;
    cout << lightYear << " light years = " << getAstronomical(lightYear) << " astronomical units." << endl;
    return 0;
}
float getAstronomical(float lightYear){
    float astronomical = lightYear * 63240;
    return astronomical;
}
```



7. 编写一个程序， 要求用户输入小时数和分钟数。在main(）函数中， 将这两个值传递给一个void函数， 后者以下面这样的格式显示这两个值：
   Enter the number of hours: 9
   Enter the number of minutes: 28
   Time: 9:28

```c++
#include <iostream>
using namespace std;
void printTime(int hours, int minutes);
int main(){
    int hours;
    int minutes;
    cout << "Enter the number of hours: ";
    cin >> hours;
    cout << "Enter the numbe of minutes: ";
    cin >> minutes;
    printTime(hours, minutes);
    return 0;
}
void printTime(int hours, int minutes){
    cout << "Time: " << hours << ":" << minutes << endl;
}
```

