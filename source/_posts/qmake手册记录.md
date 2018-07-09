---
title: qmake手册记录
date: 2018-07-10 00:16:08
tags:
  - qmake
  - 跨平台编译
categories:
  - Documenting
---
<Excerpt in index | 首页摘要> 
- `qmake`基本概念介绍;
- `pro`文件编写示例;
- `pro`文件高级编写语法。
`qmake`是Trolltech公司创建的主要用来给qt项目在**不同平台和编译器书写Makefile的工具**。用`qmake`，开发者可以创建一个简单的“项目”(.pro文件)然后运行`qmake`在不同平台下生成适当的Makefile。
<!-- more -->
<The rest of contents | 余下全文>

## qmake相关基本概念
### 几个名词说明

- **`QMAKESPEC`环境变量**
  - **必须设置为你当前所使用的平台和编译器的组合**，否则跨平台编译不会成功。比如，如你使用的是win+msvc，就该把环境变量设为win32-msvc，如果使用的是Solaris和g++，就该设置为solaris-g++。
  - 在`qt/mkspecs`中的每一个目录里面，都有一个包含平台和编译器特定信息的`qmake.conf`文件，这些设置适用于你要使用`qmake`的任何项目，一般不要进行修改，

- 项目(.pro)文件
  - 用于告诉qmake关于为这个应用程序创建makefile所需要的细节，如，一个源文件和头文件的列表，任何应用程序特定配置(如一个必须要连接的额外库、或者一个额外的包含路径)，都应该放到`.pro`项目文件中。

- `#`注释
  - 可以用`#`号为项目文件添加注释。注释由"#"符号开始，直到这行结束。

### 模板

模板变量告诉`qmake`为这个应用程序生成哪种makefile，有以下可供使用的选择：
- `app`：建立一个应用程序的makefile。这是**默认值**，如果模板没有被指定，这个就会被使用;
- `lib`：建立一个库的makefile(库的文件结构关系，如何被应用程序链接);
- `vcapp`：建立一个库的Visual Studio项目文件;
- `vclib`：建立一个库的Visual Studio项目文件;
- `subdirs`：这是一个特殊的模板，它可以创建一个能够进入特定目录并且为一个项目文件生成makefile并且为它调用make的makefile。

#### "app"模板

"app"模板告诉qmake为建立一个应用程序生成一个makefile，当使用这个模板时，下面这些qmake系统变量是被承认的，这些信息应该在你的`.pro`文件中指定好。
- HEADERS 应用程序中所有头文件的列表
- SOURCES 应用程序中所有源文件的列表
- FORMS 应用程序中的所有`.ui`文件(由Qt设计器生成)的列表。
- LEXSOURCES 应用程序中的所有lex源文件的列表。
- YACCSOURCES 应用程序中的所有yacc源文件的列表。
- TARGET 可执行应用程序的名称，默认值为项目文件的名称。(如果需要扩展名，会自动被加上。)
- DESTDIR 放置可执行程序目标的目录。
- DEFINES 应用程序所需的额外的预处理程序定义的列表。
- INCLUDEPATH 应用程序所需的额外的包含理解路径的列表。
- DEPENDPATH 应用程序所依赖的搜索路径。
- VPATH 寻找补充文件的搜索路径。
- DEF_FILE 仅Windows需要：应用程序所需要连接的`.def`文件。
- RC_FILE 仅windows需要：应用程序所需要的资源文件。
- RES_FILE 仅windows需要：应用程序所要连接的资源文件。

如果不需要任何额外的INCLUDEPATH,那么就不需要指定它，`qmake`会提供默认值，实际的项目文件可能是下面这个样子：
```
TEMPLATE = app
DESTDIR = c:\helloapp
HEADERS += hello.h
SOURCES += hello.cpp \
			main.cpp
DEFINES += QT_DLL
CONFIG += qt warn_on release
```

**Note**:如果条目是单值的，如template或工程目录，用“=”赋值;但如果是多值条目，使用"+="来为这个类型添加条目。

#### "lib"模板

“lib”模板告诉qmake为建立一个库而生成makefile(根据makefile进行make之后库会被编译成可调用格式)，除了"app"模板中提到的系统变量，**还有一个VERSION是被支持的**，也需要在".pro"文件中使用它们

- VERSION 目标库的版本号，如2.3.1。

#### "subdirs"模板

“subdirs”模板告诉qmake生成一个makefile，可以进入到特定子目录并为这个目录中的项目文件生成makefile并为它调用make。

在这个模板中只有一个系统变量*SUBDIRS*可以被识别，这个变量中包含了所要处理的含有项目文件的子目录的列表。这个项目文件的名称和子目录是同名的，这样qmake就可以发现它，如，子目录名是"myapp"，则目录中的项目文件就该叫做`myapp.pro`。

### CONFIG变量
配置变量指定了编译器所要使用的选项和所需要被连接的库。配置变量的下面这些选项可以被qmake识别。

- 这些选项控制着编译器相关标志
  - `release` 应用程序以release模式连编，如果"debug"被指定，它将被忽略(优先级低于debug)。
  - `debug` 应用程序以debug模式连编。
  - `warn_on` 编译器会输出尽可能多的警告信息，如"warn_off"被指定，它将被忽略

- 这些选项定义了所需要连编的库/应用程序的类型
  - `qt` 应用程序是一个Qt应用程序(非console)，则Qt库会被连接;
  - `thread` 应用程序是一个多线程应用程序;
  - `X11` 应用程序是一个X11应用程序或库;
  - `windows` 只用于“app”模板：应用程序是一个窗口应用程序;
  - `console` 只用于“app”模板：应用程序是一个控制台应用程序;
  - `dll` 只用于“lib”模板：库是一个共享库(dll);
  - `staticlib` 只用于“lib”模板：库是一个静态库。
  - `plugin` 只用于“lib”模板：库是一个插件，这将会使dll选项生效。

那比如，如果你的应用程序使用Qt库，并且你想把它连编为一个可调试的多线程的应用程序，`pro`文件中的CONFIG就应该如下设置：
```
CONFIG += qt thread debug
```

**注意**：必须使用"+="号。

## 手动安装qmake

当Qt被连编时，默认情况下qmake也会被连编。
在手工连编Qt之前，下面**这些环境变量必须被设置**：
在设置QMAKESPEC时，可以从下面的可能的环境变量列表中进行选择(在`/usr/lib/x86_64-linux-gnu/qt5/mkspecs`可以查看到各平台的`qmake.conf`)：
```
aix-64 hpux-64 irix-032 netbsd-g++ solaris-cc unixware7-g++ aix-g++ hpux-g++
linux-cxx openbsd-g++ solaris-g++ win32-borland aix-xlc hpux-n64 linux-g++
openunix-cc sunos-g++ win32-g++ bsdi-g++ hpubx-o64 linux-icc qnx-g++ tru64-cxx
win32-msvc dgux-g++ hurd-g++ linux-kcc reliant-64 tru64-g++ win32-watc freebsd-g++
irix-64 macx-pbuilder reliant-cds ultrix-g++ win32-visa hpux-acc irix-g++ macx-g++
sco-g++ unixware-g hpubx-acc irix-n32 solaris-64 uninxware7-cc
```

![makespec](pics/qt_makespec.png)

当envvar是以下之一时，环境变量要设置到`qws/envvar`:
```
linux-arm-g++ linux-generic-g++ linux-mips-g++ linux-x86-g++ linux-freebsd-g++ 
linux-ipaq-g++ linux-solaris-g++ qnx-rtp-g++
```

- `QTDIR`:必须设置到Qt被安装(或将被安装)到的地方。
在上述环境变量被设置后，在自己的编译环境下运行`make`(linux)或者`nmake`(win)。此时`qmake`就可以使用了。


## 10分钟学会使用qmake

### 1. 创建一个项目文件

`qmake`使用储存在项目(`.pro`)文件中的信息来决定Makefile中应该生成什么。

一个基本的项目文件包含关于应用程序的信息，如编译应用程序需要哪些文件，并且使用哪些基本配置设置。

- 一个简单的示例项目文件
```
SOURCES = hello.cpp
HEADERS = hello.h
CONFIG += qt warn_on release
```
对以上例子进行解析：

- `SOURCES = hello.cpp`：**指定实现应用程序的源文件程序**。*当有多个文件时*
1. 可以把所个文件列在一行中，以空格分隔，如
```
SOURCES = hello.cpp main.cpp
```

2. 又或者将每个文件列在一个分开的行中，通过反斜线`\`另起一行
```
SOURCES = hello.cpp \
			main.cpp
```

3. 或者单独列出每一个文件
```
SOURCES += hello.cpp
SOURCES += main.cpp
```

- `HEADERS = hello.h`：**指定为这个应用程序创建的头文件**。当有多个文件时，上述适用`SOURCES`的方法也一样适用于`HEADERS`文件。

- `CONFIG += qt warn_on release`：**告诉qmake**关于应用程序的配置信息。*用`+=`添加选项比做`=`替换是安全得多的*。
  - `qt`部分：告诉`qmake`这个应用程序是使用Qt来连编的，即`qmake`在为连接和为编译添加所需要的包含路径时就会考虑到Qt库;
  - `warn_on`部分：告诉`qmake`把编译器设置为输出警告信息;
  - `release`部分：告诉`qmake`应用程序需要被连编成一个发布的应用程序。那在开发过程中，也可以用`debug`来替换这里的`release`;

**项目文件就是扩展名为`.pro`的纯文本文件**，`qmake`就是根据这个文件确定不同平台下项目的连编规则。发布的应用程序的执行文件的名称必须和项目文件的名称一样，但是扩展名是跟着平台而改变的。举例来说，一个叫做"hello.pro"的项目文件将会在Windows下生成`hello.exe`，而在Unix下生成"hello"。

### 2. 生成Makefile
根据`.pro`项目文件，用`qmake`就可以生成Makefile：
```
qmake -o Makefile hello.pro
```

对于VS用户，`qmake`也可以生成`.dsp`(developer studio project)文件：
```
qmake -t vcapp -o hello.dsp hello.pro
```

## qmake教程
### pro文件编写配合`qmake`和`make`使用
以一个工程实现一个qmake的编写(在qtcreator中创建工程时，所有的配置信息都会被qt自动在`.pro`中写好)。当你的工程已经创建了以下文件：
- `hello.cpp`
- `hello.h`
- `main.cpp`

我们按照以下顺序编写项目的`.pro`文件：
- 添加源文件：
```
SOURCES += hello.cpp \
			main.cpp
```

- 添加头文件
```
HEADERS += hello.h
```

- 目标名称
  - 会被设置为和项目文件一样的名称，但是为了适合平台所需要的后缀。如，win下应该目标名称为“hello.exe”，在Unix下应该是“hello”。
  - 如果想为项目设置一个不同的名字，可以在项目文件中设置它：
```
TARGET = helloworld
```

- 设置`CONFIG`变量(当在qtcreator中创建工程时，配置信息在进行工程生成时已经被用户选择好且将相关信息配置到`pro`文件中)
  - 因为这是个qt程序，我们需要把"qt"放到CONFIG这一行中，这样`qmake`才会在连接时添加相关的库，并且保证`moc`和`uic`的连编行也被包含到Makefile中。
```
CONFIG += qt
```

- `pro`文件编写完成后，可以在应用程序目录下的命令行中输入`qmake`命令来为应用程序生成`Makefile`:
```
qmake -o Makefile hello.pro
```

- 根据自己使用的编译器，输入`make`或者`nmake`。

### 使程序可以调试

应用程序的发布版本不包含任何调试符号或其他调试信息(发行版本进行了代码优化，也是为了运行流畅)。

在开发过程中，要是要生成一个含有相关调试信息的应用程序的调试版本，只要在项目文件的`CONFIG`变量中添加一个"debug"就可以简单实现。比如对于`hello.pro`
```
CONFIG += qt debug
HEADERS += hello.h
SOURCES += hello.cpp
SOURCES += main.cpp

```

再`qmake -o Makefile hello.pro`就可以生成相应的`Makefile`，再用`make`或`nmake`就可以生成debug版本的可执行程序了。

### 添加特定平台的源文件
应用程序中与平台相关的文件，如现在有两个新文件`hello_win.cpp`、`hello_x11.cpp`要添加到项目中，不能仅仅添加到`SOURCES`变量中就结束，因为那会把两个文件都添加到Makefile中。这里需要做的事是**根据`qmake`所运行的平台来使用相应的作用域进行处理**。

- **为Windows平台添加的平台依赖的文件**的简单的作用域看起来如下：
```
win32{
	SOURCES += hello_win.cpp
}
```
当`qmake`运行在windows上，它会把`hello_win.cpp`添加到源文件列表中，如果`qmake`运行在其他平台时，这部分就会被跳过。

- **添加一个X11依赖文件的作用域**
```
x11{
	SOURCES += hello_x11.cpp
}
```

之后像前面一样使用`qmake`来生成Makefile，之后是`make`/`nmake`。此时项目文件应该像下面这样：
```
CONFIG += qt debug
HEADERS += hello.h
SOURCES += hello.cpp \
			main.cpp
win32{
	SOURCES += hello_win.cpp
}

x11{
	SOURCES += hello_x11.cpp
}
```

### 如果一个指定文件不存在，停止qmake

当某个文件不存在时，也许不想生成一个Makefile。

我们可以通过使用`exists()`函数来检查一个文件是否存在，然后使用`error()`函数把正在运行的`qmake`停下来，这和作用域的工作方式一样，只是要将这个函数替换作用域条件。*其实前面的作用域条件都是bool值,bool值为真就执行作用域中语句，否则不执行*。

```
!exists(main.cpp){
	error("No main.cpp file found")
}
```
那么此时使用`qmake`生成Makefile文件，当缺少`main.cpp`文件时，error信息显示，并且`qmake`会停止处理。


### 检查多于一个的条件
假设使用Windows并且在命令行运行你的应用程序时你想**显示debug语句**，这就需要在连编程序时使用console设置，否则看不到输出，将`console`添加到CONFIG行中。那如果我们想要(1)在程序运行在win下;(2)且debug在CONFIG行中时;添加console。
- 这就需要两个嵌套的作用域，然后将设置放在最里面的作用域中，如下：
```
win32{
	debug{
		CONFIG += console
	}
}
```
- **嵌套的作用域也可以使用冒号连接起来**
```
win32:debug{
	CONFIG += console
}
```

到这里，你就已经准备好为你的开发项目写项目文件了。


## qmake高级概念--语法
qmake提供了更强大的功能，如可以使用一个简单的项目文件为多个平台生成makefile。

### 操作符
- `=` 简单分配一个值给一个变量，如同赋值操作(**覆盖**原有值)
- `+=` 向一个变量的值的列表中**添加**一个值。 
- `-=` 从一个变量的值的列表中移去一个值。
- `*=` 仅在一个值**不存在**于一个变量的值的列表中时，把它**添加**进去。如`DEFINES *= QT_DLL`只在QT_DLL未被定义在预处理定义的列表中时，才被添加进去。
- `~=` 替换任何与指定正则表达式匹配的任何值(与vim命令相同，模板匹配替换)。如`DEFINES ~= s/QT_[DT].+/QT`对所有QT_D或QT_T开头的变量，替换成QT。

### 作用域
功能类似于条件判断语句，当前面的表达式为bool真，作用域中的语句会被执行。

- 例如，以下语句作用是在windows下使用qmake时，将QT_DLL添加到makefile中。
```
win32{
	DEFINES += QT_DLL
}
# 也可以用冒号换成以下形式表示
win32:DEFINES += QT_DLL
```

- 又比如在除了win平台以外的平台做些处理，
```
!win32{
	DEFINES += QT_DLL
}
# 或者
!win32:DEFINES += QT_DLL
```

- CONFIG行中任何条目都是一个作用域。如`CONFIG += warn_on`其中的`warn_on`就是一个作用域。这个特性使得考虑所有可能的编译器标志成为可能：可以针对不同编译器标志采取不同的措施，使项目易于维护和移植。如下例
```
CONFIG += qt warn_on debug
debug{
	TARGET = myappdebug
}
release{
	TARGET = myapp
}
```

- 也可以**定义嵌套作用域**，为避免写出过多嵌套作用域，可以**使用冒号来嵌套作用域**。如以下语句检查平台是windows且线程设置是否设定。
```
win32{
	thread{
		DEFINES += QT_THREAD_SUPPORT
	}
}
# 用冒号嵌套作用域
win32:thread{
	DEFINES += QT_THREAD_SUPPORT
}
```

- 还可以用`else`作用域提供替代声明，当之前的作用域是false时，对应的`else`作用域会被处理。

```
win32:xml{
	message(Building for Windows)
	SOURCES += xmlhandler_win.cpp
}else:xml{
	SOURCES += xmlhandler.cpp
}else{
	message("Unknown configuration")
}

```

### 变量

除了DEFINE、SOURCES和HEADERS这些系统变量外，用户还可以创建自己的变量，然后在作用域中使用它们。
当对一个给定的名字进行赋值时，qmake就会**创建新的变量**。例如
```
MY_VARIABLE = value
```

也可以通过在其他任何一个变量的变量名前加`$$`来把这个变量的值分配给当前的变量。例如
```
MY_DEFINES = $$DEFINES
```
现在MY_DEFINES变量包含了项目文件在此时DEFINES变量的值，这也和下面的语句等价：
```
MY_DEFINES = $${DEFINES}
```
第二种方法允许你将变量的内容附加到另一个值后，而不在两者之间添加空格(相当于拼接)。比如
```
TARGET = myproject_$${TEMPLATE}
```
变量可以被用于存储环境变量的内容，这些值在`qmake`被运行时会被评估(evaluated)，或者会被包含在生成的Makefile中而在项目被构建时进行评估。

要在运行qmake时获取环境变量的内容，使用`$$(...)`操作符：
```
DESTDIR = $$(PWD)
message(The project will be installed in $$DESTDIR)
```
在上面的赋值中，PWD环境变量的值会在工程文件被处理时被读出。

如果要在处理生成的Makefile时才去获取一个环境变量的值，使用`$(...)`操作符，*推测主要差别是什么时候用常量值去取代变量，节省空间*：
```
DESTDIR = $$(PWD)
message(The project will be installed in $$DESTDIR)

DESTDIR = $(PWD)
message(The project will be installed in the value of PWD)
message(when the Makefile is processed)
```
在上述的赋值操作中，`PWD`的值在项目文件(`pro`文件)被处理时会立即被读出，但是$(PWD)会在生成的Makefile中被赋值给`DESTDIR`。这使得构建过程更加灵活，只要环境变量在Makefile被处理时被正确设置了的话。

还有一个特殊的`$$[...]`操作符，可以被用来获取构建Qt时不同的设置选项。
```
message(Qt version:$$[QT_VERSION])
message(Qt is installed in $$[QT_INSTALL_PREFIX])
message(Qt resources can be found in the following loacations:)
message(Documentation:$$[QT_INSTALL_DOCS])
message(Header files:$$[QT_INSTALL_HEADERS])
message(Libraries:$$[QT_INSTALL_LIBS])
message(Binary files(executables):$$[QT_INSTALL_BINS])
message(Plugins:$$[QT_INSTALL_PLUGINS])
message(Data files:$$[QT_INSTALL_DATA])
message(Translation files:$$[QT_INSTALL_TRANSLATIONS])
message(Settings:$$[QT_INSTALL_SETTINGS])
message(Examples:$$[QT_INSTALL_EXAMPLES])
message(Demonstrations:$$[QT_INSTALL_DEMOS])
```
可以通过这个操作符获取的变量，经常被用于使得整合到Qt中的第三方的插件和组件可用。比如说，按照下面的声明，一个Qt Designer插件可以被安装为Qt Designes的内建插件目录下。
```
target.path = $$[QT_INSTALL_PLUGINS]/designer
INSTALLS += target
```
### 变量处理函数
`qmake`提供了一系列内建函数来处理变量的内容，这些函数处理提供给它们的变量并且返回一个值，或者一个列表的值作为结果。为了能将值赋给一个变量，**需要`$$`操作符来进行变量内容的取值**赋值操作。
```
HEADERS = model.h
HEADERS += $$OTHER_HEADERS
HEADERS = $$unique(HEADERS)
```

有可能需要一个你自己的函数，用于处理变量的内容，这些函数按照如下方式定义：
```
defineReplace(functionName){
	#function code
}
```
下面给出一个示例函数：输入一个变量名，用内建的`eval()`函数从中提取一系列值，并且编译一系列文件：
```
defineReplace(headersAndSources){
	variable = $$1
	names = $$eval($$variable)
	headers = 
	sources =

	for(name,names){
		header = $${name}.h
		exists($$header){
			headers += $$header
		}
		source = $${name}.cpp
		exists($$source){
			sources += $$source
		}
	}
	return($$headers $$sources)
}
```

### 测试函数
qmake提供了一些测试函数，这些函数也可以用作书写作用域时的条件，这些函数不返回一个值，而是指示着"success"或者"failure"(bool值)。

- `contains(variablename,value)`:如果value存在于一个叫做variablename的变量的值的列表中，则执行作用域中内容：
```
contains(VCONFIG,thread){
	DEFINES += QT_THREAD_SUPPORT
}
```

- `count(variablename,number)`:如果variablename变量的值的总数是number个，那么这个作用域中的设置会被执行。
```
count(DEFINES, 5){
	CONFIG += debug
}
```

- `error(string)`：输出指定字符串，然后使qmake退出。
```
error("An error has occured")
```

- `exists(filename)`:如果指定文件存在，那么这个作用域中的设置会被处理。**注意:可以不用考虑平台,一致使用`/`作为目录的分隔符**。
```
exists(/local/qt/qmake/main.cpp){
	SOURCES += main.cpp
}
```

- `include(filename)`:项目文件在执行到此语句时包含这个文件名的内容，那么作用域中的设置被执行。
```
include(myotherapp.pro){
}
```

- `isEmpty(variablename)`:如果variablename变量中没有任何元素，那么这个作用域中的设置会被处理。
```
isEmpty(CONFIG){
	CONFIG += qt warn_on debug
}
```
- `system(command)`:指令执行且如果返回值为1,则作用域中内容被执行。
- `infile(filename,var,val)`：如果filename文件包含一个值为val的变量var，那么这个函数将返回成功，第三个参数val省略时，只测试文件中是否有这样一个变量。

## 参考资料
- [qmake用户手册](http://read.pudn.com/downloads137/sourcecode/embed/583560/qt%E7%A8%8B%E5%BA%8F%E8%AE%BE%E8%AE%A1/qmake%E7%94%A8%E6%88%B7%E6%89%8B%E5%86%8C.pdf)
- [官方qmake manual](http://doc.qt.io/qt-5/qmake-manual.html)


