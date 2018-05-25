---
title: tmux的安装和使用
tags:
  - Linux
  - Ubuntu
  - 工具
categories:
  - Documenting
date: 2018-05-25 23:17:16
---

<Excerpt in index | 首页摘要> 
- 配置环境：**Ubuntu 16.04**;
- 安装tmux;
- tmux基本使用命令。
tmux是一款分屏工具，可以将你的终端切分成几块利用。
<!-- more -->
<The rest of contents | 余下全文>
## 常用命令
1. 安装tmux,`sudo apt install tmux`。
2. 开启tmux窗口`tmux`，会自动进入tmux窗口，此时只分一个窗口。
3. tmux命令一般由prefix key+command key触发，使用方式是按住prefix key，松开后按下command key。prefix key默认是`Ctrl-b`，即同时按住`ctrl`和`b`键。而command key列表如下：
  1. 对窗格的操作：
	1. `%`:左右分窗格;
	2. `"`:上下分窗格;
	3. `<arrow key>`:窗格导航，如配合使用left方向键时会导航到当前窗格的左端;
  2. 对窗口的操作：
	1. `c`:创建新窗口;
	2. `p`:切换到前一个窗口;
	3. `n`:切换到下一个窗口;
	4. `<number>`:切换到<number>号窗口，窗口号在窗口下端的status bar上显示。
  3. 对会话的操作：
	1. `d`:脱离当前tmux会话，回到bash下，会话会运行在后台;
	2. `D`:从tmux选择一个会话进行脱离;
	3. bash敲入`tmux ls`可以查看到当前在运行的所有tmux sessions;
	4. bash敲入`tmux attach -t <number>`用于连接到<number>对应的会话;
	5. bash敲入`tmux new -s aMeaningfulName`创建一个会话并赋予名字;	
	6. bash敲入`tmux rename-session -t <number> aMeaningfulName`为第<number>号的session赋予一个新的名字。
	7. bash敲入`tmux attach -t sessionName`重新连接名字对应的session。
4. 关闭窗格：用`ctrl-d`或者是输入`exit`来关闭。
5. 查看相关的帮助:`ctrl-b`+`?`查看相关命令。
6. 更多常用命令，prefix key+以下command key:
  1. `z`:将一个窗格放大到全窗口/缩回原窗格大小;
  2. `Ctrl-<arrow-key>`:将窗格按照箭头方向放大/缩小;
  3. `,`:重命名当前窗口。
## 更多配置
1. 配置文件为`~/.tmux.conf`，每次开启新会话时，Tmux都会先读取该配置文件。
2. 如果希望新的配置能够立即生效，在配置文件中加入以下语句，并按下`C-b` `r`就可以使新配置生效：
```
bind R source-file ~/.tmux.conf; display-message "Config reloaded.."
```
3. 变更快捷键为`ctrl-a`，在`~/.tmux.conf`中添加以下语句：
```
unbind C-b
set -g prefix C-a
```
