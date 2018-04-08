---
title: Linux系列1-概述
tags:
  - Linux
categories:
  - Learning
date: 2018-04-09 00:45:03
---

<Excerpt in index | 首页摘要> 
- Linux起源和发行版本介绍
- GNU和GPL概念介绍
- 硬盘和分区描述
- 修复受损Grub
如要下载笔记和代码请到[我的github](https://github.com/yucicheung/LearningNotes/tree/master/Linux)。
<!-- more -->
<The rest of contents | 余下全文>

## Linux和Unix
- Linux是对Unix的重新实现。
- Linux开发人员最初是借鉴了UNIX的技术和用户界面，并且融入了很多独创的技术改进，从这方面可以说Linux是UNIX的一个变体。但是从开发形式(社区支持)和最终产生的源代码来看，Linux不属于BSD和AT&T风格中的任一种，因此严格说来，Linux是有别于UNIX的操作系统。
- Linux实际上**只定义一个操作系统内核**，以同一个基础开始，却衍生了不同的发行版本。以下表格列出著名的Linux发行版本(按**源版本**和衍生版本划分)

| 发行版本 | 官方网站 | 说 明 |
| --- | --- | --- |
| **Red Hat Enterprise** | www.redhat.com | Red Hat公司的企业级商业化发行版本 |
| Fedora | fedoraproject.org | Red Hat公司赞助的社区项目免费发行版本 |
| CentOS | www.centos.org | 模仿Red Hat Enterprise Linux的非商业发行版本 |
| **Debian** | www.debian.org | 免费的非商业发行版本 |
| Ubuntu | www.ubuntu.com | 类似Debian的免费发行版本 |
| **SUSE Linux Enterprise** | www.suse.com/linux | Novell公司的企业级商业化Linux发行版本 |
| openSUSE | www.opensuse.org | SUSE Linux的免费发行版本 |

## GNU&GPL
- `GNU`(GNU's not UNIX)是使软件自由的计划;
- 它的开源协议是`GPL`(GNU Public License)，是包括Linux在内的一批开源软件遵循的许可证协议。
## Linux对硬盘及分区的表述
- 硬盘一般分为IDE硬盘、SCSI硬盘和SATA硬盘
  - Linux中，IDE的接口被称为hd，SCSI和SATA接口的设备则被称为sd。第1块硬盘称为sda,第2块称为sdb，以此类推。
  - Linux规定，**一块硬盘上只能存在4块主分区**，分别命名为sda1、sda2、sda3、sda4。**逻辑分区则从5开始标识**，每多一个逻辑分区，就在末尾的分区号加1。逻辑分区没有数量限制。
- 一般来说，每个系统都需要一个主分区来**引导**。这个分区中存放着**引导**整个系统所必需的程序和参数。
  - 操作系统可以按照光在主分区也可以安装在逻辑分区，但**引导程序必须安装在主分区内**。
- **安装提示**:“安装类型”界面允许用户进行分区，创建两个分区就可以，一个主分区挂载点为'/'('/boot'等挂载点会自动安装在其中)，另一个交换空间(相当于虚拟内存，用于缓冲数据)。
## 进阶：修复受损的Grub
- Linux默认使用的默认操作系统引导加载器Grub，可以引导包括Linux、Windows、FreeBSD等多种操作系统。
- Linux安装程序会在一切准备稳妥之后安装Grub，并加入对硬盘中原有操作系统的支持。这一切都是自动完成的。但是后安装Windows的话，win的引导程序却会自动将Grub覆盖。导致Linux无法启动。
- 万一Grub失效，需要用修复盘（即安装盘）以LiveCD模式修复，即“Try Ubuntu without installing”以命令行重新安装Grub。依次用以下命令安装Grub:

| 命令 | 含义 |
| --- | --- |
| grub | 启动光盘上的grub程序 |
| find /boot/grub/stage1 | 查找硬盘上的Linux系统将/boot目录存放在哪个硬盘分区中，grub安装时需要读取这个目录中的相关配置文件 |
| root (hdx,y) | 指示Linux内核文件所在的硬盘分区（/boot所在分区），<br>将这里的(hdx,y)替换为上一行中查找到的那个分区。<br>注意括号中不能存在空格。 |
| setup (hd0) | 在地一块硬盘上安装引导程序Grub |
| quit | 退出Grub程序 |

**提示**:Grub对磁盘分区的表示方式和Linux有所不同。Grub将所有硬盘都表示为(hd#)的形式，`#`从0开始编号。对任一块硬盘，(hd#,0)~(hd#,3)依次表示它的主分区，随后的(hd#,4)....则是逻辑分区。(sd[a-z]从1开始编号)
## 参考文献
*Linux从入门到精通 刘忆智 著*
