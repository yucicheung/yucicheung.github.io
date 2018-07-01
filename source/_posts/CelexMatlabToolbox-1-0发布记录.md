---
title: CelexMatlabToolbox-1.0发布记录
tags:
  - 工具
  - github
  - matlab
categories:
  - Documenting
date: 2018-06-30 00:31:16
---

<Excerpt in index | 首页摘要> 
- `CelexMatlabToolbox-1.0`工具箱说明;
- `Release`发布操作;
- 选择并添加`Apache`开源证书;
- 发起pull request向其他仓库做贡献。
<!-- more -->
<The rest of contents | 余下全文>

## 写在前面
关于我自己的博客，我其实一直觉得没有什么干货，前些天看到本科跟我同一级但是两年前选择工作而非读研的人的博客，发现人家的技术增长速度真是我们无法企及的。想象那种一切都是为了大用户量，高并发，大数据的工作，真的比我们瞎研究有意义多了，而且成就感恐怕也无法比拟，但是好在我现在的工作也不是只有空想和空谈，不是像本科一样只在看书，不做实践。
很期待我真的去到实际生产线接触用户需求的日子，希望那天顺利到来。
<br>总的一句话，**读研需谨慎**。
下面正文。

## 工具箱说明
### DVS介绍
DVS是一种与传统全帧成像的传感器(CMOS/CCD)不同的Sensor,它的主要特点是，画面中每一个像素点的强度信息可以被独立读出，只有该像素点的光强变化超过一定阈值时，才向外部电路发出请求，形成一个个package，这些package最终可以被解码为包含`x`列地址,`y`行地址,`a`强度信息,`t`时间戳格式的事件，从而被我们用来做二维/三维图像的可视化。
目前的DVS生产商在世界上有大概十家左右，具体可以参考[event-based resources仓库的'Devices & Companies Manufacturing them'目录](https://github.com/uzh-rpg/event-based_vision_resources#devices)，其中就包含我们用到的`CeleX`　DVS器件，目前在售的型号是`CeleX IV`。
### 工具箱内容
`CelexMatlabToolbox-1.0`是我为`CeleX IV`型号的DVS(Dynamic Vision Sensor，动态视觉传感器)编写的第一版Matlab工具箱。
<br>
工具箱的文件结构和功能如下：
- `createImgFromRawData.m`(即上一版发布的文件`bin2picByFixedAmountOfEvents.m`)
  - 针对bin文件实时解码
  - 二值图像的实时显示及存储；
  - 灰度图像的实时显示及存储；
  - 累积式灰度图的实时显示及存储；
- 函数集合`functions`
  - 对bin文件进行批量解码为`x,y,adc,t`格式(其中`t`为连续时间)；
  - 将解码事件转存为mat文件及其读取；
  - 二值图的显示及存储；
  - 灰度图的显示及存储；
  - 累积式灰度图的显示及存储；
  - 去噪二值图的显示及存储；
  - 去噪灰度图的显示及存储；
  - 事件流的三维动态显示。
- `demo.m`
  - 可运行的示例文件，提供对所有工具箱函数的调用示例。

**这一版的代码已经发布到[我的git仓库](https://github.com/yucicheung/CelexMatlabToolbox)。**

目前可预见的待更新内容有
- 基于时间片段的事件累积方式实现;
- 改进去噪效果
  - 考虑FPN的影响
  - 考虑空间域的事件关联性

## 添加开源协议
### 选择合适的开源协议
![一张很直观的开源协议选择图](/img/HowToChooseOSSL.png)<br>
我对开源协议的要求主要就是以下两点：
1. 所有引用或者修改自我的代码都必须加上版权说明;
2. 保留以此代码为基础申请专利的权利。
基于这两点，最终选择了`Apache-2.0 LICENSE`。
### 添加Apache-2.0 LICENSE
步骤如下：
- 到[官网](http://www.apache.org/licenses/LICENSE-2.0)下载Apache2.0 License源文件;
- 修改源文件，将其中`[]`包围部分替换为自己的信息;
- 将该文件保存为`LICENSE`文件存储在项目文件夹根目录下;
- 如果你还有引用到拥有协议保护的其他第三方库，请将其包含在`NOTICE`文件中并且做出一些说明，同样包含在项目文件夹的根目录下;
- 将license源文件最末的`copyright`开始的部分粘贴在项目的每个文件中(以注释形式)，一般放置在开头，我放在末尾。
- 以上都做完之后，就完成了添加协议，上传到github以后，在你的项目主页就会显示你所用的开源协议标签。

## Release in github
- **对当前版本打tag**
```shell
git tag -a v1.0 -m 'Comment'# -a选项描述版本名，-m选项描述对版本的基础描述
```
- **完善release描述**
  - 点击项目主页的`release`选项条，会显示你打过的所有tags;
  - 在对应版本号右边点击`edit release notes`;
  - 选择你要编辑的tag号并且添加相应的名字和描述，点击`Update release`即可发布。
最后在项目的`release`选项条下就会得到一个有详细描述的release版本，用户可以选择下载`zip`或者`.tar.gz`格式的版本文件，文件名上会自动加上你的标签号。

## 发起pull request
最后我希望将我自己的这点小成果添加到上述的[event-based resources仓库](https://github.com/uzh-rpg/event-based_vision_resources)中，所以向该仓库发起了pull request，很幸运最后也确实被批准和merge进去了。
<br>
步骤是
- 从源仓库fork到自己的仓库;
- 从远程仓库clone到本地;
- 在本地修改之后进行commit;
- 在你自己的远程仓库主页点击`new pull request`，确认修改信息，添加必要描述，就可以向源仓库发起请求了。
- 等待源仓库的reviewer对你的请求做出review，同意之后就等待你的commit被merge进去就可以了。当然也可能被要求修改或者是拒绝，此时你要对你的commit做出进一步修改。

## 结语
只是一点微小的工作，还有好多活没干完，当然了，活是干不完的。

## 参考资料
[如何为你的开源项目选择一个合适的开源协议？](https://www.oschina.net/news/74999/how-to-choose-a-license)
