---
title: 从Ubuntu软件包管理器apt安装qt5记录
date: 2018-07-05 20:16:37
tags:
---
如果是在windows下安装，只需要到[网站](http://download.qt.io/archive/qt/)安装qtcreator即可。
在构建项目时需要把所需的动态链接库包含到文件夹中。
## 安装Qt

sudo apt-get install build-essential

sudo apt-get install qtcreator

sudo apt-get install qt5-default

## 配置Qt
- 运行`qtcreator`命令打开qtcreator;
- 工具-选项-kit，add一个新kit

## 其他配置
- 下载qt5的API文档
```shell
sudo apt install qt5-doc # 安装qt5的API文档
sudo apt install qt5-doc-html # 安装qt5的API文档，HTML格式版
sudo apt install qtbase5-doc-html # 安装qt5基础HTML文档
sudo apt install qtbase5-examples # 安装Qt5的基础示例
```
## 简单测试所有配置是否成功
创建一个qt工程文件，`helloWorld.pro`，还是用简单的HelloWorld。
程序如下:
```c++
#include <iostream>
using namespace std;

int main()
{
    cout<<"Hello World!"<<endl;
    return 0;
}
```
点击构建项目`helloWorld`.

## TroubleShooting
- `bits/c++config.h:No such directory or file.`:可能是编译器和你的系统环境不统一，将你的编译器变为32/64切换一下，或者`sudo apt-get install gcc-multilib g++-multilib`会自动解析你的gcc版本,编译命令会自动调用`-m32`进行。
## 参考贴
[Ubuntu上安装qt](https://stackoverflow.com/questions/48147356/install-qt-on-ubuntu)

[64bit程序在32bit机上跨环境编译](https://stackoverflow.com/questions/4643197/missing-include-bits-cconfig-h-when-cross-compiling-64-bit-program-on-32-bit)
