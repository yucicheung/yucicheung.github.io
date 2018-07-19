---
title: cmake笔记
date: 2018-07-10 00:16:08
tags:
  - cmake
  - 跨平台编译
  - c++
  - Linux
categories:
  - Documenting
---
<Excerpt in index | 首页摘要> 
- `cmake`简介;
- 简单语法;
- 几个项目示例。
<CMake Practice>笔记内容汇总，需要结合项目代码进行理解，代码在[我的仓库](https://github.com/yucicheung/LearningNotes/tree/master/xmake/cmake)。
<!-- more -->
<The rest of contents | 余下全文>
本文主要参考[cmake practice](http://sewm.pku.edu.cn/src/paradise/reference/CMake%20Practice.pdf),项目代码在[我的仓库](https://github.com/yucicheung/LearningNotes/tree/master/xmake/cmake)(因为原书内容有点过时，所以代码做出了修正)，笔记中加入了自己的理解。
另，原书未更新完，所以这里只有前六章内容，但是日常也够用了。

## 简单的HelloWorld(文件夹t1)
cmake以指定目录下的`CMakeLists.txt`为根据生成相应的中间文件(包括CMakeCache.txt,cmake_install.cmake,Makefile,CMakeFiles文件夹)，之后运行`make`（或者运行`make VERBOSE=1`观察make命令构建的详细过程）。
### 基本语法规则
CMakeLists.txt必须在工程的每个目录下都存在一份，其最基本语法规则为：
- 变量用`${}`方式取值，但在**IF控制语句中**直接使用变量名;
- `指令(参数1 参数2)`，参数用空格或分号分开;
- 指令大小写无关，但是变量名大小写相关，但**推荐全部使用大写指令**;
- 运行`make clean`即可对构建结果进行清理(二进制可执行文件);`make distclean`在cmake中不可用(因为中间文件不可追踪，于是干脆禁用命令)。

**注意**：
1. 工程名和生成的可执行文件名之间没有关系(因为一个项目中可以生成多个可执行文件)。
2. 推荐外部构建(out-of-source build)而不推荐内部构建(in-source-build)，这样在需要重新构建时将整个构建文件夹删除即可。(最主要删除`CMakeCache.txt`)

## 更像工程的HelloWorld(文件夹t2)
**一些注意：**
- 代码安装时用**cmake -DCMAKE_INSTALL_PREFIX=/tmp/t2/usr ..**选项;默认的CMAKE_INSTALL_PREFIX=/usr/local
- 在INSTALL后，注意文件的权限会发生变更。

默认以后采用外部构建方式。要使项目更像工程：
1. 添加一个子目录src存放工程源代码;
  - 主目录下`CMakeLists.txt`填写`ADD_SUBDIRECTORY(src bin)`;
  - 子目录下用`ADD_EXECUTABLE()`;
2. 添加一个子目录doc存放工程文档;
3. 工程目录下添加文本文件COPYRIGHT,README;
4. 工程目录添加一个`runhello.sh`脚本用于调用hello二进制文件;
5. 安装这些文件：将hello二进制文件与runhello.sh安装至/usr/bin，将doc目录内容及COPYRIGHT/README安装到/usr/share/doc/cmake/t2。
  - 方式1:`make install`;方式2:打包时指定目录安装，如果是手工编写的MakeFile会看起来如下：
  - 对于`cmake`,需要使用指令`INSTALL`和一个很有用的变量`CMAKE_INSTALL_PREFIX`，下面具体说明
### cmake install
`cmake`需要使用指令`INSTALL`和一个很有用的变量`CMAKE_INSTALL_PREFIX`：
1. `INSTALL`指令用于定义安装规则，安装内容可以包括目标二进制、动态库、静态库以及文件、目录、脚本等，具体安装指令在下面介绍;
2. `CMAKE_INSTALL_PREFIX`的使用格式一般是`cmake -DCMAKE_INSTALL_PREFIX=/usr .`

#### INSTALL命令
INSTALL命令(不管是make install或者install命令)，都是负责copy files and set attributes。

1. 目标文件(动态静态库及可执行文件)的安装
```
INSTALL(TARGET targets...			#TARGETS后面跟ADD_EXECUTABLE/ADD_LIBRARY定义的目标文件，可能是可执行二进制、动态库、静态库，targets可以有多个
		[[ARCHIVE|LIBRARY|RUNTIME]	# 对应目标类型为ARCHIVE静态库、LIBRARY动态库、RUNTIME可执行目标二进制
		[DESTINATION <dir>]			# 如果路径以/开头，那么就是绝对路径，此时CMAKE_INSTALL_PREFIX无效;相对路径不要以/开头，则${CMAKE_INSTALL_PREFIX}/<DESTINATION>为安装后的路径
		[PERMISSIONS perssions...]
		[CONFIGURATIONS [Debug|Release|...]]
		[COMPONENT <component>] [OPTIONAL]]
		[...]
)
```
举个例子
```
INSTALL (TARGETS myrun  mylib mystaticlib
		RUNTIME DESTINATION bin			# 将二进制myrun安装到${CMAKE_INSTALL_PREFIX}/bin目录
		LIBRARY DESTINATION lib			# 动态库libmylib安装到${CMAKE_INSTALL_PREFIX}/lib目录
		ARCHIVE DESTINATION libstatic	# 静态库libmystaticlib安装到${CMAKE_INSTALL_PREFIX}/libstatic目录
	)
```
上述例子中，不需要写出库文件生成的全称(lib-)，而只要写出库文件的名字即可。

2. 普通文件的安装
```
INSTALL (FILES files... DESTINATION <dir>
		[PERMISSIONS permissions...]
		[CONFIGURATIONS [Debug|Release|...]]
		[COMPONENT <component>]
		[RENAME <name>] [OPTIONAL])
```
其中，文件名(FILES)是此指令所在路径下的相对路径，默认PERMISSIONS为644权限，cmake中描述为OWNER_WRITE,OWNER_READ,GROUP_READ,WORLD_READ。

3. 非目标文件的可执行程序安装(如脚本之类)：
```
INSTALL(PROGRAMS files... DESTINATION <dir>
		[PERMISSIONS permissions...]
		[CONFIGURATIONS [Debug|Release|...]]
		[COMPONENT <component>]
		[RENAME <name>] [OPTIONAL])
```
跟上述FILES指令使用方法一致，唯一不同的是安装后的权限为：OWNER_EXECUTE,GROUP_EXECUTE,WORLD_EXECUTE,即755权限。

4. 目录的安装
```
INSTALL(DIRECTORY dirs... DESTINATION <dir>
	[FILE_PERMISSIONS permissions...]		# permissions总是需要一连串指令指明
	[DIRECTORY_PERMISSIONS permissions...]
	[USE_SOURCE_PERMISSIONS]
	[CONFIGUTATIONS [Debug|Release|...]]
	[COMPONENT <component>]
	[[PATTERN <pattern> | REGREX <regex>]
	[EXECLUDE] [PERMISSIONS permissions...]] [...])
```
其中
- `DIRECTORY`:后面接所在Source目录的相对路径，但**注意**：`abc`表示将整个目录拷贝，`abc/`表示将目录下的所有文件拷贝过去;
- `PATTERN`:用于正则表达式过滤;
- `PERMISSIONS`：用于指定对前面的正则表达式过滤出来的文件的权限。

举个例子：
```
INSTALL(DIRECTORY icons scripts/ DESTINATION share/myproj
		PATTERN "CVS" EXECLUDE
		PATTERN "scripts/*" PERMISSIONS OWNER_EXECUTE OWNER_WRITE OWNER_READ GROUP_EXECUTE GROUP_READ
		# 可以看到每次出现一个关键词，将其后面的内容存储为其中内容，直到碰到下一个关键词
)
```
上述执行结果会是：icons目录安装到<prefix>/share/myproj，将`scripts/`文件夹中所有内容安装到<prefix>/share/myproj;对含有"CVS"的目录不包含，对符合`srcipts/*`模式的文件。
如果要换个地方存放目标二进制，可以通过`SET`命令重新定义`EXECUTABLE_OUTPUT_PATH`/`LIBRARY_OUTPUT_PATH`变量来指定最终的目标二进制的位置(指最终生成的二进制可执行文件或最终共享库，不包含编译生成的中间文件。记住:**在哪里`ADD_EXECUTABLE()`/`ADD_LIBRARY()`，就在哪里加入上述修改目标文件夹的定义。**


## 静态库与动态库构建(t3文件夹)
### 任务
1. 建立一个静态库和动态库，提供HelloFunc函数供其他程序编程使用，HelloFunc向终端输出Hello World字符串。
2. 安装头文件及共享库。 
### 相关指令
- `ADD_LIBRARY`添加构建动态静态库
```
ADD_LIBRARY(libname [SHARED|STATIC|MODULE] [EXCLUDE_FROM_ALL] source1 source2 ... sourceN)
```
其中，
- libname不需要写全`libhello.so`，只需要写`hello`就可以，cmake会自动生成`libhello.X`;
- 库的三种类型
  - `SHARED`动态库(`.so`后缀)
  - `STATIC`静态库(`.a`后缀)
  - `MODULE`在使用dyld的系统有效，如果不支持dyld，则被当作SHARED对待
- `EXCLUDE_FROM_ALL`表示该库不会被默认构建，除非有其他组建依赖或者手工构建
**问题**：要构建的TARGET(可执行二进制文件/动态静态链接库等)是不能重名的，但是输出库的名字可以被修改,以使输出的动态静态库可以同名。

- `SET_TARGET_PROPERTIES`设置输出的名称，对于动态库还可以用来指定动态库版本和API版本
```
SET_TARGET_PROPERTIES(target1 target2 ...
		PROPERTIES prop1 value1
		prop2 value2...)
```

在/t3/build中进行cmake，就可以同时输出`libhello.so`和`libhello.a`两个库。
举几个例子

```
SET_TARGET_PROPERTIES(hello_static PROPERTIES OUTPUT_NAME "hello")#以下命令用来表示不清除文件中的同名目标,但在3.5.1中已经可以生成同名的目标文件而不会被清除
SET_TARGET_PROPERTIES(hello PROPERTIES CLEAN_DIRECT_OUTPUT 1)
SET_TARGET_PROPERTIES(hello_static PROPERTIES CLEAN_DIRECT_OUPUT 1)
```

**问题**：动态库一般是包含一个版本号的，如果我们想生成以下链接，我们应该怎么实现呢?
```
libhello.so.1.2
libhello.so->libhello.so.1
libhell.so.1->libhello.so.1.2
```
- 采用以下指令添加动态库版本:
```
SET_TARGET_PROPERTIES(hello PROPERTIES VERSION 1.2 SOVERSION 1) #SOVERSION表示API版本，VERSION表示动态库版本
```
最终我们需要**将动态库和头文件案安装到系统目录，才能被人开发使用**，按照如下命令将hello共享库安装到<prefix>/lib目录，将`hello.h`安装到<prefix>/include/hello目录下。为下一节准备，将文件安装在`/usr/lib`和`/usr/include/hello`下。

## 使用外部共享库和头文件(t4目录)
按照构建可执行二进制文件的方式构建文件夹，此时`#include<hello.h>`需要用`<>`符号(因为已经在系统文件夹中,但是不在系统标准的头文件路径/usr/include中)，因此直接在`cmake`、`make`之后会出现错误,改用**`make VERBOSE=1`**来构建。
**注意**：其实只要写成`#include<hello/hello.h>`会在`/usr/include`下查找相应文件。

此时我们要**引入头文件搜索所路径**，用指令`INCLUDE_DIRECTORIES`或者是`CMAKE_INCLUDE_PATH`选项
- `INCLUDE_DIRECTORIES`
  - 用于向工程添加多个特定的**头文件搜索路径**，多个路径之间用空格分割，如果路径中包含空格，可以用双括号括起来，**默认**的行为是**追加到当前的头文件搜索路径后面**;
  - 可以通过两种方式来进行控制搜索路径添加的方式:
	1. CMAKE_INCLUDE_DIRECTORIES_BEFORE,通过SET这个cmake变量为on，可以将添加的头文件搜索路径放在已有路径的前面;
	2. 通过AFTER或者BEFORE参数，也可以控制是追加或者是置前。
```
INCLUDE_DIRECTORIES([AFTER|BEFORE] [SYSTEM] dir1 dir2 ...)
```
**问题**：上述步骤完成后，会出现一个新的错误`undefined reference to 'HelloFunc'`，因为还没有link到共享库libhello上（gcc -lhello）。

- `TARGET_LINK_LIBRARIES`用于为target(库/可执行二进制文件)添加需要链接的共享库。
```
TARGET_LINK_LIBRARIES(target library1
					<debug | optimized> library2
					...)
```
其中library可以写成`hello`(会优先链接到动态库`.so`)或者库全称`libhello.so`/`libhello.a`。
以下还有另外一个命令：
- `LINK_DIRECTORIES`添加非标准的共享库搜索路径。
  - 如在工程内部同时存在共享库和可执行二进制，在编译时就需要指定一下这些共享库的路径。
```
LINK_DIRECTORIES(directory1 directory2 ...)
```

- 特殊的**环境变量**`CMAKE_INCLUDE_PATH`和`CMAKE_LIBRARY_PATH` 
  - 两个环境变量可以设置到`.bashrc`中并且`export`生效，或者在CMakeLists.txt中用`SET`设置相应变量值;
  - `CMAKE_INCLUDE_PATH`配合`FIND_PATH`(用来在指定路径中搜索文件名)使用;
	- 当头文件没有存在常规路径`/usr/include`,`usr/local/include`中时，可以通过这些变量弥补。
  - `CMAKE_LIBRARY_PATH`配合`FIND_LIBRARY`(在指定路径中搜索库名)使用。
则更自动化的`INCLUDE_DIRECTORIES()`如下：
```
SET(CMAKE_INCLUDE_PATH /usr/include/hello)
FIND_PATH(myHeader hello.h)
IF(myHeader)
INCLUDE_DIRECTORIES(${myHeader})
ENDIF(myHeader)
```
## 模块的使用和自定义模块
对于系统与定义的Find<name>.cmake模块，使用方法如下：
每一个模块都会定义以下几个变量(系统预定义)：
- <name>_FOUND:判断模块是否找到
- <name>_INCLUDE_DIR or <name>_INCLUDES：存储include路径，当FOUND为真，INCLUDE_DIRECTORIES();
- <name>_LIBRARY or <name>_LIBRARIES：存储library路径,当FOUND为真时，TARGET_LINK_LIBRARIES()。

以下看一个复杂的例子，用<name>_FOUND控制工程特性：
```
SET(mySources viewer.c)
SET(optionalSources)
SET(optionalLibs)


FIND_PACKAGE(JPEG)# 当输入选项-DENABLE_JPEG_SUPPORT以下代码生效
IF(JPEG_FOUND)
	SET(optionalSources ${optionalSources} jpegview.c)
	INCLUDE_DIRECTORIES(${JPEG_INCLUDE_DIR})
	SET(optionalLibs ${optionalLibs} ${JPEG_LIBRARIES})
	ADD_DEFINITIONS(-DENABLE_JPEG_SUPPORT)
ENDIF(JPEG_FOUND)


FIND_PACKGAE(PNG)# 当输入选项-DENABLE_PNG_SUPPORT以下代码生效
IF(PNG_FOUND)
	SET(optionalSources ${optionalSources} pngview.c)
	INCLUDE_DIRECTORIES(${PNG_INCLUDE_DIR})
	SET(optionalLibs ${optionalLibs} ${PNG_LIBRATIES})
	ADD_DEFINITIONS(-DENABLE_PNG_SUPPORT)
ENDIF(PNG_FOUND)

ADD_EXECUTABLE(viewer ${mySources} ${optionalSources})
TARGET_LINK_LIBRARIES(viewer ${optionalLibs})
```

## 编写自己的FindHello模块(t6目录)
在CMakeLists.txt中调用的是`.cmake`模块中的变量，而cmake模块中的变量可以系统定义，也可以用户自己定义。

- `FIND_PACKAGE(<name> [major.minor] [QUIET] [NO_MODULE] [REQUIRED|COMPONENTS] [components...]])`
  - 如果指定QUIET参数，就对应编写的FindHELLO.cmake中的HELLO_FIND_QUIETLY;
  - 指定REQUIRED参数，则找不到该链接库时不能编译，对应FindHELLO.cmake中的HELLO_FIND_REQUIRED变量。

## 指令汇总
项目中用到的指令如下：
- `PROJECT(projectname [CXX] [C] [JAVA])`
  - 语言列表可以忽略，默认支持所有语言;
  - 两个隐式的cmake变量，`<projectname>_BINARY_DIR`、`<projectname>_SOURCE_DIR`,或者就直接用`PROJECT_BINARY_dir`、`PROJECT_SOURCE_DIR`;
- `SET(VAR [VALUE] [CACHE TYPE DOCSTRING [FORCE]])`
  - 如给`SRC_LIST`设置变量时`main.cpp`/`"main.cpp"`均可;
- `MESSAGE([SEND_ERROR | STATUS | FATAL_ERROR] "message to display"...)`
  - SEND_ERROR，产生错误，生成过程被跳过;
  - STATUS，输出前缀为`--`的信息;
  - FATAL_ERROR，立即终止所有cmake过程。
- `ADD_EXECUTABLE(hello ${SRC_LIST})`

