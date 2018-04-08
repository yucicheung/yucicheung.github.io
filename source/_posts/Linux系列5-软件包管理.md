---
title: Linux系列5-软件包管理
tags:
  - Linux
  - Shell
categories:
  - Learning
date: 2018-04-09 00:45:08
---

<Excerpt in index | 首页摘要> 
- 什么是软件包
- 介绍`dpkg`软件包管理工具
- 介绍`apt`高级软件包管理工具和相关命令
- 从源码编译安装的基本流程
如要下载笔记和代码请到[我的github](https://github.com/yucicheung/LearningNotes/tree/master/Linux)。
<!-- more -->
<The rest of contents | 余下全文>
## 概述
- 软件包是将应用程序、配置文件和管理数据打包的产物，常用的软件包格式有两种：
  - rpm(Red Hat Package Manager)：适用SUSE、Red Hat、Fedora等;
  - deb：适合Debian和Ubuntu。
- 使用软件包系统安装软件同样需要考虑依赖性问题，只有依赖的所有库和支持都已经正确安装好了，软件才能被正确安装。
- 高级软件包管理工具可以简化软件包的安装过程，常见的通用版本有**APT**和**yum**(其中yum只能用于RPM)。职责：
  - 简化定位和下载软件包的过程; 
  - 自动进行系统更新和升级;
  - 方便管理软件包的依赖关系。
- `dpkg`:管理.deb软件包。`dpkg --force`忽略依赖问题强制安装，一般应避免这种做法。
  - `dpkg -i`：安装且卸载旧版本;
  - `dpkg -l`：列出符合模式的安装包;
  - `dpkg -r`：删除，可能包含其他软件依赖的库和数据文件导致严重后果;
  - `dpkg -S`：搜索所安装的文件向系统复制了哪些内容;
- rpm和dpkg软件管理包的出现大大减少了安装软件的工作量，但是不能有效解决依赖性问题。
## 高级软件包工具：APT
- APT,Advanced Package Tool，高级软件包工具。适合rpm格式和deb格式。
- 以前APT工具最常用命令：
  - `apt-get`:用于执行和软件包安装有关的所有操作;
  - `apt-cache`:主要用于查找软件包的相关信息。
  - `apt`：用于软件包管理和查询等，**可以执行上述两条命令的所有功能**,但更适合交互。用`apt -h`查看`apt`的简要用法。
- 安装软件：
  - 系统第一次启动时，需要运行`apt update`更新当前`apt`缓存中的软件包信息;
  - 建议每一次安装前都运行`apt update`以确保获得的软件包是最新的;
  - [optional]用`apt search`或`apt list`搜索需要的软件包名字，用`apt depends`查看需要依赖的包;
  - `sudo apt install`安装软件包，需要root的原因是安装过程需要将文件复制到某些系统文件夹中。
- *安装源*是所有apt用于下载软件的地址，被放在/etc/apt/sources.list中，其中内容包括:
  - deb和deb-src：表示软件包的类型，src表示源码。如果是RPM的软件包，应是rpm和rpm-src;
  - URL:HTTP、FTP服务器或CD-ROM的地址，表示从哪获得软件包;
  - 其他：表示软件包的发行版本和分类，用于帮助`apt`遍历软件库。
- 暂时禁止一个安装源时，应考虑先注释掉。
## 进阶：从源代码编译软件
- 某些情况下，编译源码安装软件是唯一的选择：
  - 某些厂商没有提供为此发行版提供二进制软件包;
  - 软件的源代码经过修过，必须重新编译;
  - 从源代码编译软件通常能让编译者获得更多控制，例如软件安装位置，开启和禁用功能。
编译流程：
  1. 源代码安装软件前的配置：
	- 首先要仔细阅读安装文档如README或INSTALL;
	- Linux上所有的软件都使用`configure`这个脚本来配置以源代码形式发布的软件;
	- configure依据设置参数生成相应的makefile，指导make命令编译源代码;
	- 一般configure脚本提供一个--prefix选项，用于指定软件安装位置，如`./configure --prefix=`;
	- 将软件安装在/usr/local下是一个好习惯，可以同安装在/usr下的系统工具区分开来。
  2. 执行`make`，make是一个高级编译工具，依据makefile文件中的规则调用合适的编译器编译源码;
  3. `make install`安装软件。
	- 出错时应多参考论坛。

## 参考文献
*Linux从入门到精通 刘忆智 著*
