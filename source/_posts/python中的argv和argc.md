---
title: python中的argv和argc
tags:
  - python
categories:
  - Learning
date: 2018-03-17 22:24:44
---
<Excerpt in index | 首页摘要> 
## 主要问题
- 为什么`argv`中第一个，即index=0的内容就是文件名？
- python中`argc`是用什么实现的？
<!-- more -->
<The rest of contents | 余下全文>
## 概念解释
`argc`:argument counter，命令行参数个数
`argv`:argument vector，命令行参数向量(内容)
## 通过代码理解含义
创建一个文件`arg_exam.py`，其中内容如下：
```python
# argv
import sys
for i in sys.argv:
    print i

# argc
argc = len(sys.argv)
print argc
```
在shell中运行一个简单的例子
```bash
$ python arg_exam.py hello I am an example
```
输出为
```
arg_exam.py
hello
I
am
an
example
6
```
所以说，`argv`就是`python`命令后跟着的一系列命令参数的内容。
而`argc`（在C语言存在的变量）就是这些命令参数的个数了，在python中因为`argv`是个列表，其长度`len`自然就是`argc`了，所以python中并没有为`argc`特地设置一个方法或者属性。
## 结论
- argv是在命令行中运行程序时跟在`python`命令后的所有内容，以空格为分界，得到各元素。
- python中argc并不是一个特定属性或方法，而是可以直接通过`len(sys.argv)`获得。
