---
title: C++相关知识点及建议总结(一)
date: 2018-08-18 11:37:51
tags:
  - c++
  - 编译
  - 书写规范
categories:
  - Learning
---
<Excerpt in index | 首页摘要> 
这些天在gist上记录了很多知识点，本来想等秋招结束后，统一整理，但是笔记慢慢也积累得很长了，担心到最后整理起来太麻烦，所以先把目前为止记录的内容整理好，之后做成一个小系列，可能会比较方便一些。
正在看的*Effective C++*中有很多很好的内容，但是没有总结在这里面，这部分内容主要是平时从*C++ Primer*、stackOverflow、wikipedia、MSDN和博客上了解到的相关内容的汇总。

本文包含：`1.`编译相关;`2.`语言特性;`3.`编写规范。
<!-- more -->
<The rest of contents | 余下全文>

## 编译相关
- 在`g++`编译一系列文件时，不要包含`.h`文件，因为`.h`内容都已经包含在`.cpp`文件中。**Header files are never compiled.**
- [#ifdef and #ifndef Directives](https://zh.cppreference.com/w/cpp/preprocessor/conditional):主要用于预处理器控制编译。
  - 头文件中的#ifndef是一个很关键的东西，比如两个c文件都include同一个头文件，而两个头文件要编译成同一个可运行文件，此时会有大量的声明冲突，所以一般把头文件内容放在#ifndef和#endif中。
  - identifier理论上可以是自由命名的，对于头文件来说，每个标识都应该是唯一的。标识的命名规则一般是**头文件名全大些，文件中的"."变成下划线**，如"stdio.h"变为"STDIO_H"(也就是说，如果没有定义这个identifier，就将identifier定义为以下到#endif为止的内容)。
```c++
#ifdef identifier
#ifndef identifier

//equivalent to
#if defined identifier
#if !defined identifier                                                        
```
- [关于程序语言是如何被编译，被解释执行的过程](https://www.zhihu.com/question/21853681/answer/74134768)
  - 字节码和二进制机器码不是一个概念，字节码是在程序语言和二进制机器码中间的一个桥梁，最终被机器执行的仍然是二进制代码。

- VS中解决方案solution和工程project之间的区别：本质上，解决方案是由多个工程组成的，(每个工程是一个独立的软件模块，如一个程序和一个代码库)，这样做的好处是解决方案可以共享文件和代码库。通常为解决方案创建一个主文件夹，包括所有的工程文件夹，但对于之包含一个工程的解决方案来说，习惯于把解决方案和项目放在一个解决方案中。
- `hpp`文件是将`.cpp`的实现代码混入`.h`头文件当中，定义与实现都包含在同一文件，则该类的调用者只需要include该hpp文件即可，无需再将cpp加入到project中进行编译。而实现代码**将直接编译到调用者的obj文件中，不再生成单独的obj**,采用hpp将大幅度减少调用 project中的cpp文件数与编译次数，也不用再发布烦人的lib与dll,因此非常适合用来编写公用的开源库。
  - 优点：一般已预编译;
  - 缺点：不能包含全局对象和全局函数;类之间不可循环调用;不可使用静态成员。`redefined identifier`。
- `make`:根据Makefile编译源代码，连接，生成目标文件，可执行文件。
   - `make clean`：清除之前编译产生的可执行object文件(".o")；
   - `make distclean`:要清除所有生成的文件，包括configure生成的文件，如Makefile; 
   - `make install`:将编译成功的可执行文件安装到系统目录中，一般为`/usr/local/bin`目录;
   - `make dist`:产生发布软件包文件(distribution package)，这个命令产生可执行文件及相关文件打成一个`tar.gz`压缩文件作为发布软件的软件包;一般会在当前目录下生成一个名字为`PACKAGE-VERSION.tar.gz`的文件，其中`PACKAGE`/`VERSION`是我们在configure.in中定义的`AM_INIT_AUTOMAKE(PACKAGE,VERSION)`。
   - `make distcheck`：生成发布软件包并对其进行测试检测，以确定发布包的正确性。该操作将自动打开压缩包文件，执行configure，执行make确认编译不出现错误，并且提示软件已经准备好，可以发布了。

- 宏定义`_WIN32`和`WIN32`
  - `WIN32`是由工具包或者构建环境定义(user-defined)的，所以它不使用执行预留的命名空间中;
  - `_WIN32`是编译器定义的，所以使用下划线把它放置在执行预留的命名空间中。

- C++关于DLL文件的导入导出
  - 使用`__declspec(dllexport)`关键字从DLL导出数据、函数、类或类成员函数。`__declspec(dllexport)`会将导出指令添加到对象文件中，因此您不需要使用`.def`文件，仅当解决任何命名约定更改时才必须重新编译DLL和依赖.exe文件;
  - 生成DLL时，通常创建一个包含正在导出的函数原型和/或类的头文件，并将`__declspec(dllexport)`添加到头文件中的声明中，若要提高代码的可读性，请为`__declspec(dllexport)`定义一个宏并且对正在导出的每个符号使用该宏，如`#define DllExport __declspec(dllexport)`,则该命令将函数名存储在DLL的导出表中。
  - 为使用DLL生成的应用程序创建头文件时，在公共符号的声明上使用`__declspec(dllimport)`(不论是.def文件导出还是用__declspec(dllexport)关键字导出均有效),为使导入的可执行文件能够访问DLL的公共数据符号和对象，必须使用`__declspec(dllimport)`;
  - 如要提高代码的可读性，请为`__declspec(dllimport)`定义一个宏，然后使用该宏声明每个导入的符号。
```
  #define DllImport __declspec(dllimport)
  
  DllImport int j;
  DllImport void func();
```
  - 对DLL和客户端应用程序可以使用相同的头文件，因此需要使用特殊的预处理器符号来指示是*生成DLL*还是*生成客户端应用程序*，例如
```
  #ifdef _EXPORTING
	    #define CLASS_DECLSPEC __declspec(dllexport)
  #else
	    #define CLASS_DECLSPEC __declspec(dllimport)
  #endif
  
  class CLASS_DECLSPEC CExampleA : public CObject
  {...class definition...};
```
- 关于`#if defined()`用法
```c++
#if defined(CREDIT)
  credit();
#elif defined(DEBIT)
  debit();
#else
  printerror();
#endif
```
## 语言特性
- C++中`int main(int argc, char* argv[])`
  - `argc`表示命令行输入参数的总个数，文件名本身也占1;
  - `argv`表示所有命令行输入参数的内容，其中`arg[0]`即文件名。

- `size_t`:SIZE_T is a ULONG_PTR representing the maximum number of bytes to which a pointer can point.This type is declared as `typedef ULONG_PTR SIZE_T;`,
  - size_t代表目标平台能够使用的最大的类型，不仅考虑到跨平台特性，还兼顾了效率。
- `<stdint.h>`中带有`_t`(t for type)后缀数据类型的介绍
  - 用以下`_t`的数据类型，即我们可以以确定字节数表示一个数据类型，而不用为了适应不同CPU，而去改变数据类型。
```
typedef unsigned char uint8_t
typedef unsigned int uint16_t
typedef unsigned long int uint32_t
typedef unsigned long long int uint64_t

typedef signed char int8_t
typedef signed int int16_t
typedef signed long int int32_t
typedef signed long long int64_t
```

- 关于C++中的左值lvalue和右值rvalue：
  - 每个C++表达式是左值/右值;
  - 表达：
     - 凡是真正存在于内存当中，而不是寄存器当中的值就是左值，其余的都是右值。(**dereference`&`可以成功的都是左值，其余都是右值**)。
     - **左值**，在定义它的表达式以外保留的对象;**右值**，在定义它的表达式以外不保留的临时值。
     - **编译器将已命名的右值引用视为左值，而将未命名的右值引用视为右值**。
     - 左值引用符号`&`
     - 右值引用符号`&&`
   - 完美转发：在进行数值引用时，保留参数的左值/右值属性。
```c++
int x = 3 + 4;//x是左值，3+4是右值
//perfect forwarding
//usage：factory function or constructor
template <typename T，typename T1>
T createObject(T1&& t1){
  return T(forward<T1>(t1));
}
int myFive2=createObject<int>(5);   //rvalue
int five=5;
int myFive=createObject<int>(five); //lvalue
```

- `nullptr`是C++11的新标准，表示空指针，等同于`NULL`。如果在qt中支持，需要添加`CONFIG+=c++11`。

- [关于`g++`和`gcc`的差异](https://stackoverflow.com/questions/172587/what-is-the-difference-between-g-and-gcc/172592#172592):
> g++ is equivalent to  gcc -xc++ -lstdc++ -shared-libgcc (the 1st is a compiler option, the 2nd two are linker options). This can be checked by running both with the -v option (it displays the backend toolchain commands being run).
- [`volatile`关键字](https://www.cnblogs.com/yc_sunniwell/archive/2010/07/14/1777432.html)**用来设定某个对象的存储位置在内存中，而不是寄存器中**。因为一般的对象编译器可能会将其的拷贝放在寄存器中用以加快指令的执行速度
  - 当对象的值可能在程序的控制或检测之外被改变时，应该将该对象声明为volatile，该关键字告诉编译器不应该对这样的对象进行优化。
  - 什么情况下要使用这个关键字？
    - 代码中“内嵌汇编操纵栈”这种方式属于编译无法识别的变量改变；
    - 中断服务程序中修改的供其它程序检测的变量需要加volatile；
    - 多任务环境下各任务间共享的标志应该加volatile；
    - 存储器映射的硬件寄存器通常也要加volatile说明，因为每次对它的读写都可能由不同意义；
  - volatila限定符的用法和const很类似，起到对类型额外修饰的作用：同样地，如果出现在`*`左边，修饰被指物，如果出现在`*`右边，修饰的就是指针
  - 就像可以定义const限定符一样，也可以定义相应的volatile函数，它只能被volatile对象调用(与const类比)。
```c++
volatile int display_register;  //该int值可能发生改变
volatile Task * curr_task;      //curr_task指向一个volatile对象
```
- 元编程Meatprogramming：是指某类计算机程序的编写，这类计算机程序编写或操控其他程序作为它们的数据(比如cmake操控cpp文件或shell操控程序文件)，或者在运行时完成部分本该在编译时完成的工作。
  - 编写元程序的语言称之为元语言，被操作的语言称为目标语言。
  - 一门语言同时也是自身的元语言的能力称为反射。

## 建议
- 在`.h`文件中不要写`using namespace std`。
-  对成员变量的命名不要采用下划线开头(下划线开头的变量通常是编译器自留的)，而应该采用下划线结尾。
