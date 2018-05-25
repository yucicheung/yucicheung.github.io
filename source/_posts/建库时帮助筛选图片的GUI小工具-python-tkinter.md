---
title: 建库时帮助筛选图片的GUI小工具(python+tkinter)
tags:
  - Linux
  - python
  - 自制小工具
categories:
  - Coding
date: 2018-04-11 23:43:42
---
<Excerpt in index | 首页摘要> 
一个帮助筛选图片自制GUI程序，方便建立图片数据库时使用。
- python2.7编写;
- 使用tkinter+Pillow;
代码下载请到[我的github仓库](https://github.com/yucicheung/Mini_Tools/tree/master/ClassifyGUI)。
<!-- more -->
<The rest of contents | 余下全文>
## 设计目的
建立数据库过程中，需要对收集到的图片进行筛选，查看每一张图片，将合适的图片移动到最终的`category`目录中，而不合适的图片移动到`categoryDump`目录下。
这样一来方便建立数据库，二来不需要的图片也可以整理好(以便日后使用,如做负目标等)。
对于这个需求，就需要一边能实现查看图片，针对查看图片即使做出移动的动作，因此设计了一个GUI程序实现功能。
## 依赖条件
Written in **python2.7.12**：
- python-tk 2.7.12-1~16.04
- PILLOW_VERSION = '5.0.0'


## 功能描述
程序实现如下功能：
1. 当点击单选按钮时(请保证源数据目录都已正确设置)，程序会检查目录中的图片数量：
  - 如果图片总数是0,告知用户目录为空(即图片已处理完成);
  - 如果大于0,显示其中一副图片，并且程序会确保源目录下有`category`目录及`categoryDump`目录存在。
2. 当点击`category`按钮时，将图片移动到`category`目录下;
3. 当点击`categoryDump`钮时,将图片移动到`categoryDump`目录下。

## 使用方法

如果要测试程序,运行 `main.py`。

否则,在运行 `main.py`前你需要:
- 根据自己的需求修改`main.py`中的参数;
- **如果有多于4类要分**，请修改`ClassifyGUI.py`中相关参数.


## 示例

GUI的实际大小是*900x750*,以下展示图片按照0.5倍率缩放。

- 程序初始化

![Initialization](/img/initialization.png)

- 当点击单选按钮

![clickRadioButton](/img/clickRadioButton.png)

- 当处理完当前类别的所有图片

![doneProcessing](/img/doneProcessing.png)

## 源代码
需要下载请到[我的github](https://github.com/yucicheung/Mini_Tools/tree/master/ClassifyGUI)。
- `main.py`：
```python
# -*- coding: utf-8 -*-
from ClassifyGUI import ClassifyGUI
import os

# config vars
config = {}
config["destinationDir"] = './dataset'
config["searchPathList"] = ['./dataset/hm','./dataset/hz','./dataset/ys','./dataset/jq']
config["types"] = ["AircraftCarrier",'Bomber','TransportAircraft','DestroyerAndFrigate']
config["dumpPath"] = [os.path.join(config["destinationDir"], config["types"][i] + 'Dump') for i in xrange(4)]
config["picSize"] = (500, 500)
config["windowTitle"] = 'Classify pics'
config["windowGeometry"] = '900x750'

if __name__=="__main__":
    ClassifyGUI(config)
```

- `utils.py`：
```python
import os
import tkinter as tk
import tkinter.messagebox

def checkdir(pathToCheck):
    if not os.path.isdir(pathToCheck):
        os.mkdir(pathToCheck)
        return 0
    else:
        return len(os.listdir(pathToCheck))


def checkSourceEmpty(picList):
    if len(picList)==0:
        tk.messagebox.showwarning(title='Finish', message='Finish processing all images of this type!')
        return False
    else:
        return True
```
- `ClassifyGUI.py`：
```python
# -*- coding: utf-8 -*-
from utils import *
import os
from PIL import Image, ImageTk

class ClassifyGUI():
    def __init__(self, config):
        self.config = config
        self.radioButtonPos = (100,150)
        self.imgBoxPos = (200,200)
        self.radioPad = 220
        self.gridPad = (30,10)
        self.setUpWindow()


    def setUpWindow(self):
        searchPathLists = self.config["searchPathList"]
        picLists = [[p for p in os.listdir(searchPathLists[i]) if p.endswith('.png')] for i in xrange(4)]
        destinationDir = self.config["destinationDir"]
        types = self.config["types"]
        dumpPaths = self.config["dumpPath"]


        def radioButtonTrigger():

            Index = pathIndex.get()
            processPath = searchPathLists[Index]
            picList = picLists[Index]
            if checkSourceEmpty(picList):
                destinationPath = os.path.join(destinationDir, types[Index])
                checkdir(destinationPath)
                checkdir(dumpPaths[Index])
                picpath = os.path.join(processPath, picList[-1])
                changeImg(picpath)


        def buttonTrigger0():
            typeIndex = 0
            picList = picLists[typeIndex]
            if checkSourceEmpty(picList):
                processImg = picList.pop()
                type = types[typeIndex]
                searchPathList = searchPathLists[typeIndex]
                srcImg = os.path.join(searchPathList, processImg)
                desImg = os.path.join(destinationDir, type, processImg)
                os.system('mv {} {}'.format(srcImg, desImg))
                if checkSourceEmpty(picList):
                    picpath = os.path.join(searchPathList, picList[-1])
                    changeImg(picpath)

        def dumpTrigger0():
            typeIndex = 0
            picList = picLists[typeIndex]
            if checkSourceEmpty(picList):
                processImg = picList.pop()
                searchPathList = searchPathLists[typeIndex]
                dumpPath = dumpPaths[typeIndex]
                srcImg = os.path.join(searchPathList, processImg)
                desImg = os.path.join(dumpPath, processImg)
                os.system('mv {} {}'.format(srcImg, desImg))
                if checkSourceEmpty(picList):
                    picpath = os.path.join(searchPathList, picList[-1])
                    changeImg(picpath)

        def buttonTrigger1():
            typeIndex = 1
            picList = picLists[typeIndex]
            if checkSourceEmpty(picList):
                processImg = picList.pop()
                type = types[typeIndex]
                searchPathList = searchPathLists[typeIndex]
                srcImg = os.path.join(searchPathList, processImg)
                desImg = os.path.join(destinationDir, type, processImg)
                os.system('mv {} {}'.format(srcImg, desImg))
                if checkSourceEmpty(picList):
                    picpath = os.path.join(searchPathList, picList[-1])
                    changeImg(picpath)

        def dumpTrigger1():
            typeIndex = 1
            picList = picLists[typeIndex]
            if checkSourceEmpty(picList):
                processImg = picList.pop()
                searchPathList = searchPathLists[typeIndex]
                dumpPath = dumpPaths[typeIndex]
                srcImg = os.path.join(searchPathList, processImg)
                desImg = os.path.join(dumpPath, processImg)
                os.system('mv {} {}'.format(srcImg, desImg))
                if checkSourceEmpty(picList):
                    picpath = os.path.join(searchPathList, picList[-1])
                    changeImg(picpath)
        def buttonTrigger2():
            typeIndex = 2
            picList = picLists[typeIndex]
            if checkSourceEmpty(picList):
                processImg = picList.pop()
                type = types[typeIndex]
                searchPathList = searchPathLists[typeIndex]
                srcImg = os.path.join(searchPathList, processImg)
                desImg = os.path.join(destinationDir, type, processImg)
                os.system('mv {} {}'.format(srcImg, desImg))
                if checkSourceEmpty(picList):
                    picpath = os.path.join(searchPathList, picList[-1])
                    changeImg(picpath)

        def dumpTrigger2():
            typeIndex = 2
            picList = picLists[typeIndex]
            if checkSourceEmpty(picList):
                processImg = picList.pop()
                searchPathList = searchPathLists[typeIndex]
                dumpPath = dumpPaths[typeIndex]
                srcImg = os.path.join(searchPathList, processImg)
                desImg = os.path.join(dumpPath, processImg)
                os.system('mv {} {}'.format(srcImg, desImg))
                if checkSourceEmpty(picList):
                    picpath = os.path.join(searchPathList, picList[-1])
                    changeImg(picpath)
        def buttonTrigger3():
            typeIndex = 3
            picList = picLists[typeIndex]
            if checkSourceEmpty(picList):
                processImg = picList.pop()
                type = types[typeIndex]
                searchPathList = searchPathLists[typeIndex]
                srcImg = os.path.join(searchPathList, processImg)
                desImg = os.path.join(destinationDir, type, processImg)
                os.system('mv {} {}'.format(srcImg, desImg))
                if checkSourceEmpty(picList):
                    picpath = os.path.join(searchPathList, picList[-1])
                    changeImg(picpath)

        def dumpTrigger3():
            typeIndex = 3
            picList = picLists[typeIndex]
            if checkSourceEmpty(picList):
                processImg = picList.pop()
                searchPathList = searchPathLists[typeIndex]
                dumpPath = dumpPaths[typeIndex]
                srcImg = os.path.join(searchPathList, processImg)
                desImg = os.path.join(dumpPath, processImg)
                os.system('mv {} {}'.format(srcImg, desImg))
                if checkSourceEmpty(picList):
                    picpath = os.path.join(searchPathList, picList[-1])
                    changeImg(picpath)


        def changeImg(imgPath):
            print imgPath
            im = Image.open(imgPath)
            processImage = ImageTk.PhotoImage(im)
            lbPic['image'] = processImage
            lbPic.image = processImage


        window = tk.Tk()
        window.title(self.config["windowTitle"])
        window.geometry(self.config["windowGeometry"])
        lbPic = tk.Label(window, text='image', width=500, height=500)
        lbPic.place(x=self.imgBoxPos[0], y=self.imgBoxPos[1], width=500, height=500)

        # set up radio buttons
        pathIndex = tk.IntVar()
        for i in xrange(4):
            tk.Radiobutton(window, text=self.config["searchPathList"][i], variable=pathIndex,
                           value=i, command=radioButtonTrigger).\
                place(x=self.radioButtonPos[0]+self.radioPad*i,y=self.radioButtonPos[1],anchor='nw')


        for i in xrange(2):
            for j in xrange(4):
                if i%2 == 0:
                    tk.Button(window, text=self.config["types"][j], width=20, height=1,
                            command=eval('buttonTrigger'+str(j))).\
                        grid(row=i, column=j, padx=self.gridPad[0], pady=self.gridPad[1])
                elif i % 2 == 1:
                    tk.Button(window, text='dump '+self.config["types"][j], width=20, height=1,
                              command=eval('dumpTrigger'+str(j))). \
                        grid(row=i, column=j, padx=self.gridPad[0], pady=self.gridPad[1])

        window.mainloop()
```
