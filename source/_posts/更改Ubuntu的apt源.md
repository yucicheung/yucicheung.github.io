---
title: 更改Ubuntu的apt源
tags:
  - Linux
  - Ubuntu
categories:
  - Documenting
date: 2018-05-25 23:17:30
---

<Excerpt in index | 首页摘要> 
- 配置环境：**Ubuntu 16.04**;
- 实现目标：替换apt源为163源，以加速`apt install`速度;
- Bonus：替换apt源为Xidian源，实现免流量下载。
<!-- more -->
<The rest of contents | 余下全文>
## 写在前面
嗯，这是一篇应需而生的博文，好像有段时间没来更博了，因为在忙科研。
好好忙科研还是很充实的。

你知道吗，我发现如今科研不仅要你会matlab、python、C、C++会算法，会统计数据会画图会做ppt会报告会话术会word，还要会excel会PS会Premiere。
其实也蛮有趣的。

## 原理说明
apt源就是一个文件(Linux下一切都是文件)，位置是`\etc\apt\sources.list`，打开就可以看到你本机的apt源。

一般在没有更换apt源的时候(此时官方软件源`archive.canonical.com`的服务器在国外)，所以你要用`sudo apt install`安装软件，或者甚至是`sudo apt update`速度都会很慢，甚至可能由于实在速度过慢，系统认为你没有网络连接而直接导致操作失败。

所以为了提升我们下载的速度，一般需要把apt源更换成国内的镜像源，这样当你用`apt install`时系统就会从国内的服务器上去搜索和获取你所查找的资源，下载也就能快很多。

## 操作步骤
1. `sudo gedit /etc/apt/sources.list`打开文件，原来的软件源可以不用删除，我们只需要在原有内容前面添加我们需要的软件源就行了;
2. 添加163镜像源。在**文件最开头添加**以下内容，因为apt命令选择源的顺序就是按照`sources.list`中从前到后的顺序，即排在前面的软件源会优先被选择。
```shell
deb http://mirrors.163.com/ubuntu/ xenial main restricted universe multiverse
deb http://mirrors.163.com/ubuntu/ xenial-security main restricted universe multiverse
deb http://mirrors.163.com/ubuntu/ xenial-updates main restricted universe multiverse
deb http://mirrors.163.com/ubuntu/ xenial-backports main restricted universe multiverse
##测试版源
deb http://mirrors.163.com/ubuntu/ xenial-proposed main restricted universe multiverse
# 源码
deb-src http://mirrors.163.com/ubuntu/ xenial main restricted universe multiverse
deb-src http://mirrors.163.com/ubuntu/ xenial-security main restricted universe multiverse
deb-src http://mirrors.163.com/ubuntu/ xenial-updates main restricted universe multiverse
deb-src http://mirrors.163.com/ubuntu/ xenial-backports main restricted universe multiverse
##测试版源
deb-src http://mirrors.163.com/ubuntu/ xenial-proposed main restricted universe multiverse
```
3. 添加Xidian镜像源。如果你这么巧跟我一样是西电学生，那将以下内容添加到源文件的最开始处就能成功省下很多流量(再加上hosts一个月10G的流量限制完全可以无视)。
```
deb http://linux.xidian.edu.cn/mirrors/ubuntu/ xenial main multiverse restricted universe

deb http://linux.xidian.edu.cn/mirrors/ubuntu/ xenial-backports main multiverse restricted universe

deb http://linux.xidian.edu.cn/mirrors/ubuntu/ xenial-proposed main multiverse restricted universe

deb http://linux.xidian.edu.cn/mirrors/ubuntu/ xenial-security main multiverse restricted universe

deb http://linux.xidian.edu.cn/mirrors/ubuntu/ xenial-updates main multiverse restricted universe

deb-src http://linux.xidian.edu.cn/mirrors/ubuntu/ xenial main multiverse restricted universe

deb-src http://linux.xidian.edu.cn/mirrors/ubuntu/ xenial-backports main multiverse restricted universe

deb-src http://linux.xidian.edu.cn/mirrors/ubuntu/ xenial-proposed main multiverse restricted universe

deb-src http://linux.xidian.edu.cn/mirrors/ubuntu/ xenial-security main multiverse restricted universe

deb-src http://linux.xidian.edu.cn/mirrors/ubuntu/ xenial-updates main multiverse restricted universe
```
