---
title: Linux系列9-网络配置和远程访问
tags:
  - Linux
  - Shell
categories:
  - Learning
date: 2018-04-09 00:45:13
---

<Excerpt in index | 首页摘要> 
- 连网方式的介绍
- `ifconfig`用于命令行下配置网络
- `route`配置静态路由
- 简单计算机网络概念
- `ftp`命令
- 基于SSH的文件传输`sftp`和`scp`
- 关于Linux系统的远程登录
如要下载笔记和代码请到[我的github](https://github.com/yucicheung/LearningNotes/tree/master/Linux)。
<!-- more -->
<The rest of contents | 余下全文>
## 连网方式
- 连网方式：
  - 局域网(以太网技术，基于载波侦听、多路访问和冲突检测的连网协议);
  - 无线连接(WPA加密);
  - 有线宽带连接;
	- DSL(Digital Subscriber Line)指数字用户线路，目前主要使用ADSL，A(asymmetric)表示非对称，即上传和下载速度不同。
  - 拨号上网(Modem)。
- 局域网：
  - DHCP(动态主机配置协议)：自动获取IP地址、网络掩码、默认网关和域名服务器;
  - 静态IP：需要手动输入上述内容(**服务器**一般需要设置静态IP)。
- ADSL：
  - 使用以太网PPPoE(Point to Point Protocol over Ethernet)调制解调器设置实现连接，是一种称作"点到点"的拨号方式;
  - 连接方式1：network manager,推荐，方便配合IPV6使用;
  - 连接方式2：
	```bash
	# 设置
	sudo pppoeconf
	# 开启
	sudo pon dsl-provider
	# 关闭
	sudo poff dsl-provider
	```
## 命令行下配置网络
### `ifconfig`配置接口
- `ifconfig`：用于启动或禁用一个网络接口，同时设置其IP地址、子网掩码及其他网络选项。
- `sudo ifconfig eth0 192.168.1.14 netmask 255.255.255.0 up`，表示设置网络硬件接口的IP地址(无线网络接口往往以`wlan`开头),掩码设置，同时启动该网络接口。
- `sudo eth0 down`关闭网络接口。
- 网络基础：
  - IP地址：一个长4个字节的二进制数，每个字节用10进制数表示就成了常见的IP地址形式;
  - IP地址分为网络部分和主机部分(如N.N.N.H)：
	- 网络部分：表示地址所指的逻辑网络;
	- 主机部分：表示网络中的一台计算机。
	- 通过对IP地址和子网掩码实施“与”运算，可以将网络号分离出来。
- `lo`网络接口是“环回网络”，是一个没有实际硬件接口的虚拟网络，`127.0.0.1`这个环回网络始终指向当前主机，也可以用`localhost`表示当前主机。
### 用`route`配置静态路由
- 路由是定义网络中两台主机间如何通信的一种机制。
- Linux内核中维护着一张路由表，需要发送数据包时，Linux将这个包的目标IP地址和路由表中的路由信息比较。
  - 找到匹配项时，该数据包就会发送到这条路由对应的网关，网关负责将数据包转发到目的地;
  - 找不到匹配项时，数据包会被发送到默认路由指定的网关上;
  - 总的路线是"数据包-->网关-->目的地";
  - **网关就是负责转发的主机，必须处在当前可以直接连接到的网络上(不需要转发)**。
- `netstat -r`查看当前系统的路由信息。
  - 路由表中网关`*`表示不需要网关,意味处于同一网络(一般是网络地址，末字节为0);
- `route`：用于增加/删除一条路由;
  - `sudo route add default gw 10.71.84.2`表示增加路由表项，一条默认路由;
  - `sudo route add -net 10.62.74.0/24 gw 10.71.84.51`增加一条目的网络路由：
	- `-net`表示增加的是网络地址，目的网络;
	- `10.62.74.0/24`前部分是网络地址，24表示IP的网络地址占据24位，对应子网掩码是255.255.255.0;
  - `-host`：用此选项增加一条发往主机地址的路由表项。
- 一般一个IP代表一台主机。全0主机地址保存为网络地址(整个网络)，全1的主机地址是广播地址(网络中的所有主机)。
- `del`用于删除路由，如`sudo route del default`。
### 主机和IP间的映射
- 主机名是为了方便记忆，而数据传送时需要的是IP地址，映射的方法主要有：
  - DNS:网络中存在DNS服务器，当用户发起查询时提供IP地址;
  - hosts:文件中指定本地映射关系(`localhost`和本地主机名对应的IP地址，均为`127.0.0.1`)。
### PPP协议
- PPP协议(Point-to-Point Protocol,点到点协议)是目前应用最广泛的数据传输协议之一。
- PPP建立网络连接的步骤：
  - 使用串行调制解调器拨号;
  - 登录远程主机(通常是运营商的接入服务器);
  - 启动远程PPP协议引擎;
  - 将串行端口配置为网络接口。
- ADSL使用的PPPoE(PPP over Ethernet)就是PPP的衍生物。

# 浏览网页
- `cookie`：
  - 在用户浏览网页时，一些服务器会在用户机器的特定目录(由浏览器指定)下存储一些信息用于确定用户的身份(因此用户在不同页面之间切换时，不用重复输入验证信息)，这些信息非常短小，因此被形象地称为`cookie`;
  - cookie由浏览器管理，可以设置失效期限;
  - 一些恶意程序会窃取保存在cookie中的个人信息，因此定期清理是个好习惯。
- RSS(聚合内容，Really Simple Syndication)提供了一种订阅信息的途径：
  - RSS是在线共享内容的一种简易方式，网站通过RSS输出，让用户获取网站内容的最新更新;
  - 实时书签，自动显示最近更新的文章标题。
- Lynx：一款基于文本的浏览器，工作在Shell下。
  - Lynx可以工作在多个操作系统平台上，是GNU/Linux中很流行的console浏览器;
  - 当图形界面无法使用时，Lynx是个很好的选择;
  - 访问网站：`lynx www.csdn.net`直接用lynx打开网址;
  - 查找字符串：`/`命令可以打开命令行查找网页中的字符串;
  - 退出浏览器：`q`退出;
  - 下载和保存文件：移动光标使链接高亮显示，按`d`指示Lynx下载该链接对应文件;
# 传输文件
- NFS是Linux间的网络硬盘。
  - NFS目前只用于在Linux和UNIX主机间共享文件系统;
  - "推荐"用`sudo mount -o rw`可读写方式安装文件系统;
  - 如果要在启动时自动安装文件系统`nfs`，应配置`/etc/fstab`文件加上`nfs`项，运行`sudo mount -a -t nfs`使配置生效;
  - 卸载文件系统前应确保没有其他进程在使用该文件系统;
  - 如果发现进程被占用，用`lsof`命令查询哪些进程在使用该文件系统;
  - 如果所有办法都不奏效，用`umount -f`强制卸载文件系统。
- CIFS(Common Internet File System，公共Internet文件系统)是Windows用来"共享"文件的协议机制。
- Samba能够将Windows包含到Linux网络中：
  - Samba提供了对CIFS的实现，用于Linux和Windows主机之间的文件共享;
  - 安装在Linux主机上的Samba服务器端程序向Windows机器提供Linux共享，Windows主机则不需要安装其他特殊工具。
- `nautilus`命令打开Linux下的“文本浏览器”。
- 用`FTP`在Linux系统下载文件：
  - 可以用Web浏览器/文件浏览器查看FTP服务器上的文件，只要在地址中加上"ftp://"前缀告诉浏览器要使用FTP协议，但是还是用FTP客户端最方便下载和上传;
  - `FileZilla`是对中文编码支持最好的FTP客户端;
  	- `FileZilla`在Ubuntu安装源也提供了下载;
	- 用户名必须提供主机名、用户名、口令和服务器端口(除主机名外，其他都是可选的);
	- 服务器端口默认是21;
	- 如果“用户名”和“口令”两个文本框中留白，则以“匿名用户”登录;
	- 如果是中文站点，将该站点使用的字符编码填入“站点管理器”——>"字符集"——>“使用自定义的字符集”——>“编码”框内(通常是gbk)。
  - `ftp`命令：是Linux自带的一个命令行的FTP工具，基本可以完成所有基本的FTP操作;
	- 登录：`ftp [host [port]]`;
	- 匿名连接：用户名应输入`anonymous`;
	- 连接服务器：`open`;
	- 关闭连接但不退出服务器：`close`/`disconnect`;
	- 删除远程服务器文件：`delete <file>`;
	- 远程浏览文件/换路径：与shell命令一致;
	- 下载一个文件：`get <filename>`下载文件当前路径;
	- 下载多个文件：`mget <file...>`下载多个文件，也可以用通配符指定多个文件;
	- 上传一个文件/多个文件：`put <filename...>`;
	- 关闭交互模式：`prompt off`;
	- 改变本地目录：`lcd`;
	- 本地执行命令：`!`在命令前表示本地执行;
	- 列出所有命令/特定命令：`?`/`? <command>`;
	- 退出：`quit`/`bye`。
### 基于SSH的文件传输：`sftp`和`scp`
- `sftp`：基于`SSH`的文件传输，和传统FTP沾不上什么关系，**同样支持多用户登录**;
  - 用`sftp`传输有助于保护用户账户和传输安全;
  - `sftp`建立连接和上传/下载文件的流程：
	- 首先要确保远程主机开启了SSH守护进程，用命令`sftp user@<ipaddress>`建立连接;
	- `sftp`的常见命令与`ftp`的基本命令大部分一致。

| 命令 | 说明 |
| --- | --- |
| `cd` | 切换远程所在的目录 |
| `ls`或`dir` | 显示当前目录下的文件列表 |
| `mkdir` | 建立目录 |
| `rmdir` | 删除目录 |
| `pwd` | 显示当前远程目录 |
| `chgrp` | 修改文件(目录)的属组 |
| `chown` | 修改文件(目录)的属主 |
| `chmod` | 修改文件(目录)的权限 |
| `rm` | 删除文件/目录 |
| `rename <oldname> <newname>` | 修改文件名 |
| `exit`或`bye`或`quit` | 关闭sftp客户端程序 |
| `lcd` | 切换本地所在目录 |
| `lls` | 显示本地所在目录下的文件列表 |
| `lmkdir` | 当地新建目录 |
| `lpwd` | 显示当地所在的文件目录 |
| `put` | 上传文件 |
| `get` | 下载文件 |

### 利用SSH通道复制文件:`scp`
- 使用`scp`前应先断开`SSH`连接。
- 从服务器复制文件到本地：`scp username@<ip_address>:<remote_file_path> <local_file_path>`;
- 从本地复制文件到服务器：`scp <local_file_path> username@<ip_address>:<remote_file_path>`。

# 远程登录
## 登录另一台Linux服务器
### SSH登录：命令行登录
- `OpenSSH`：是Linux下最常用的SSH服务器/客户端;所有的Linux发行版都附带此软件(client)，可以直接通过安装源安装;
  - 安装OpenSSH：`sudo apt install ssh`;
	- 完成安装后系统会自动启动SSH服务器，同时设置为随系统启动;
	- 如果服务器没有运行，手工执行带有`start`参数的`ssh`脚本，启动SSH服务器程序，`sudo /etc/init.d/ssh start`(守护进程)。
- SSH(Secure SHell)：
  - 支持多用户同时登录;
  - 会对用户身份验证，并加密两台主机之间的通信;
  - SSH商业化版本为SSH2，开源版本为OpenSSH。
- SSH远程登录：
  - 仅访问命令行：`ssh username@ip_address`/`ssh -l username ip_address`;
  - 需运行X应用程序：`ssh -X`启动X转发功能，将远程的X应用程序界面完整传输到本地;
  - 需指定端口：`ssh -p port`;
  - 退出:`exit`;
  - 登录后一切操作都发生在远程服务器。
- SSH密钥登录：避免每次登录时都需要输入口令;
  - 基本原理：
	- 产生一对互相匹配的密钥文件(公钥和私钥);
	- 管理员的PC上保存私钥文件的副本;
	- 与私钥文件匹配的公钥文件存放在服务器上;
	- 建立SSH时检查密钥对的匹配性。
  - 操作过程：
	- 生成密钥对：`ssh-keygen -t rsa`在用户主目录下`.ssh`目录中生成密钥对，`id_rsa`是私钥文件，`id_rsa_pub`是公钥文件;
	- 复制公钥至远程主机：`scp <local-ssh>/id_rsa.pub user@ip:<remote-ssh>/authorized_keys`。
### 登录X窗口系统：图形化的VNC
- `VNC`用于图形化的远程登录：
  - 大部分LInux发行版都附带这个软件的服务器端;
  - Ubuntu下，用命令`sudo apt install vnc4-common vnc4server`安装服务器端程序。
- VNC(Virtual Network Computing，虚拟网络计算)：如果要直接从X窗口登录服务器，用VNC登录。
  - 服务器端的配置：
	- `vncserver`：运行命令，设置命令，自动在主机用户目录下生成VNC配置文件。
  - 客户端的配置：
	- 需要下载特定的客户端的程序如`vncviewer`;
	- `vncviewer 127.0.0.1:1`：登录命令，冒号后数字代表桌面号。
## 从Windows登录Linux
- 开源代码的PuTTY使用最为广泛。
- VNC也有Windows版本。
### 登录Windows服务器
- Windows上安装`VNC Server`。
- 直接通过RDP协议连接Windows服务器：
  - 要确保Windows主机已开启“远程登录”(允许用户远程连接到此计算机)功能;
  - 登录：`rdesktop -u username ip-address`在Linux上会显示windows桌面窗口;
  - 修改端口：RDP协议默认端口是3389，要修改端口在IP地址+冒号`:`+端口号。

## 参考文献
*Linux从入门到精通 刘忆智 著*
