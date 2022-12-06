# C++静态库、动态库生成与调用

## 静态库

### 生成

1. 创建空项目，编写代码，实际写起来和普通的代码没有区别
1. 唯一需要注意的就是要h和cpp分开

### 调用

调用代码里面需要引入静态库的头文件，通过头文件去调用里面的方法。但是还需要在项目设置里面配置lib文件的路径

## 动态库

### 生成

同静态库一样，h和cpp分开

```c++
#pragma once

#define MYDLL __declspec(dllexport)

extern "C" MYDLL int sub(int a, int b);
```

通过上述的方法进行声明

函数定义的方法和普通的一样

### 调用

1. 引入Windows.h头文件
2. 定义函数指针
3. 通过函数指针来调用对应的函数

```c++
#include "iostream"
#include "People.h"
#include "StaticLibrary1.h"
#include "Windows.h"
typedef int(*PSUB) (int a, int b);
typedef void(*PTEST) (People people);

int testP(int a, int b, int c)
{
    return a + b + c;
}


int main(int argc, char* argv[])
{
    std::cout << "hello world" << std::endl;
    std::cout << add(1, 2) << std::endl;
    
    HMODULE hDLL = LoadLibrary(L"../x64/Release/DynamicLibrary1.dll");
    if (hDLL == NULL)
    {
        std::cout << "dynamic library not found" << std::endl;
        return -1;
    }
    PSUB pSub = (PSUB)GetProcAddress(hDLL, "sub");
    std::cout << pSub(2 , 1) << std::endl;

    PTEST pTest = (PTEST)GetProcAddress(hDLL, "testClass");
    People peo;
    peo.name = "rowenci";
    peo.age = 24;
    pTest(peo);
    
    int (*p) (int, int, int) = &testP;
    std::cout << p(1, 2, 3) << std::endl;
    return 0;
}
```

这种方法无需进行项目配置

目前探索出来的是，基础类型肯定可以传

尝试了传一个对象，成功