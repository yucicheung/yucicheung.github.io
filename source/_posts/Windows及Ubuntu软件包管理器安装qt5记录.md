---
title: Windows及Ubuntu软件包管理器安装qt5记录
date: 2018-07-05 20:16:37
tags:
  - qt
  - Ubuntu
  - linux
categories:
  - Documenting
---
<Excerpt in index | 首页摘要> 
- 在windows下安装qt5;
- Ubuntu下安装qt5;
- qt的环境变量配置;
- 测试运行是否成功。
<!-- more -->
<The rest of contents | 余下全文>

## windows下安装qt5
如果是在windows下安装，只需要到[网站](http://download.qt.io/archive/qt/)安装相应版本qt和qtcreator即可。
再按照后文`环境变量配置`部分介绍的方法配置即可。

## Ubuntu下安装qt5
```
sudo apt-get install build-essential # 安装编译需要的软件列表
sudo apt-get install qtcreator # 安装qtcreator
sudo apt-get install qt5-default # qt5相关包安装
```
## 环境变量配置
- linux下运行`qtcreator`命令打开qtcreator，win下打开可执行文件;
- 打开`工具`——>`选项`——>`构建和运行`——>`构建套件(kit)`手动设置套件相关环境变量：根据自己的电脑配置进行。
  - `设备类型`：桌面;
  - `编译器`：按自己的电脑配置设为`GCC(x86_64bit in /usr/bin)`;
  - `Qt mkspec`:**重要**，linux的话设为`linux-g++`，windows的话设为`win32-msvc`(如果你用的是VC)。设置错误会导致连接编译出错。
- 点击`Apply`即可。

## 其他配置
- 下载qt5的API文档
```
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
- 连接编译出错一定要检查自己的`mkspec`环境变量是否合适设置。
## 参考贴
[Ubuntu上安装qt](https://stackoverflow.com/questions/48147356/install-qt-on-ubuntu)

[64bit程序在32bit机上跨环境编译](https://stackoverflow.com/questions/4643197/missing-include-bits-cconfig-h-when-cross-compiling-64-bit-program-on-32-bit)
