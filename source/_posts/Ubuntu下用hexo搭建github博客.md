---
title: Ubuntu下用hexo搭建github博客
tags:
  - hexo
  - git
  - Ubuntu
categories:
  - Configuration
date: 2018-03-13 03:30:49
---
<Excerpt in index | 首页摘要> 
- 配置环境：**Ubuntu 16.04**
- 搭建目标：**Github page(username.github.io)**
- 搭建程序：**hexo**
- 在新建分支hexo下管理部署文件，最终部署到主分支master进行网站发布，涉及第三方应用绑定。
<!-- more -->
<The rest of contents | 余下全文>
## 基础配置
### 安装git
```bash
$ sudo apt install git-core # 已安装请略过
```
并且在你的github主页创建一个repo，命名为`username.github.io`，比如对我来说就是`yucicheung.github.io`。本帖不细述，请参考[github page基本指南](https://pages.github.com/)。
### 安装nodejs
```bash
$ wget -qO- https://raw.github.com/creationix/nvm/master/install.sh | sh
```
重新开启终端使`nvm`命令生效。
```bash
$ nvm ls-remote # 查看所有可用版本
$ nvm install v8.10.0 # 选择最新稳定版本
$ node -v #显示v8.10.0表示安装成功
```
### 安装hexo
```shell
$ sudo apt install npm # 安装package manager
$ npm install -g hexo-cli
```
## hexo基础配置
### 初始化模板
用`hexo`命令初始化一个空文件夹。
```bash
$ hexo init hexo
```
hexo安装所需的新文件。
```bash
$ cd hexo
hexo $ npm install
```
改变`hexo/`文件夹下的`_config.yml`的一些简单配置包括`title`,'author'等，之后就可以用以下命令预览。
```bash
$ hexo server # 默认端口4000
```
**Notice:**如果端口`4000`被占用，或者更换端口预览，或者解除占用。
```bash
# 方法1：更换端口
$ hexo server -p 5000
# 方法2：解除端口占用
$ lsof -i:4000
$ kill -9 PID
```
## 更改主题为yelee
首先要在[hexo-themes](https://hexo.io/themes/index.html)中选择一个喜欢的主题，从git上`clone`到本地`themes/`文件夹下并命名为`yelee`。
```bash
$ cd hexo/
$ git clone git@github.com:MOxFIVE/hexo-theme-yelee.git themes/yelee
```
然后更改`hexo/_config.yml`中`theme: landscape`改为`theme: yelee`，`themes/lanscape`文件夹可删除。
再用`hexo server`或`hexo s`就可以预览为新主题了。
具体theme中相应主题配置应参考对应theme的官方文档，如[yelee的官方文档](http://moxfive.coding.me/yelee/)。
## 部署到网站
**说明:网站的部署其实就是生成静态文件，`hexo`下所有生成的静态文件会放在`public/`文件夹中，所谓部署`deploy`其实就是将`public/`文件夹中内容上传到git仓库`yucicheung.github.io`中。**
要准备将静态文件部署到自己的git主页，首先需要安装一个用于部署的插件[hexo-deployer-git](https://github.com/hexojs/hexo-deployer-git)，这个插件可以自动将`public/`文件中内容上传到`master`下(即用于生成github.io界面的文件)。
**如果不装插件，也可以手动进行文件复制。**
```bash
npm install hexo-deployer-git --save
```
然后在`hexo/_config.yml`的`deploy`部分配置以下语句。
```txt
deploy:
  type: git
  repo: https://github.com/yucicheung/yucicheung.github.io
  branch: master
```
需要在根据需要修改`hexo/`和`hexo/themes/`下的`_config.yml`文件后，就可以进行生成和部署了。
```bash
$ hexo generate # 或hexo g
$ hexo deploy # 或hexo d
```
`deploy`时需要github的账户和密码，自动上传文件完成部署。
**Tips：**网站再次进行部署时，还需要清理`public`文件夹内容，重新生成部署，用以下命令。
```bash
$ hexo clean
$ hexo g
$ hexo d
```
## 管理hexo文件
最好的办法是在`username.github.io`主页创建两个分支，一个`master`分支(由deployer管理)，一个`hexo`分支（我们自己管理）。
在把自己的`username.github.io`仓库克隆到本地后，`cd username.github.io`，进行以下操作。
创建hexo分支并切换到该分支。
```bash
$ git checkout -b hexo
```
将`hexo/`文件夹下所有内容拷贝到`username.github.io/hexo/`下。 
```bash
$ cp -r ~/hexo/ ~/yucicheung.github.io/
```
将修改加入并push到分支`hexo`。
```bash
$ git add .
$ git commit -m "commet"
$ git push origin hexo
```
这样文件管理就很方便了。
## 第三方部署
因为第三方部署跟主题有很大关系，而[主题文档](http://moxfive.coding.me/yelee/5.Vendor/)中说明得比较清楚，我主要讨论一下文档中说明不清的Google站长认证问题。
### Google站长验证
1. 如果要按照主题定义的方法操作，需要在验证备用方法中选择`html验证`。
2. Google验证，默认方法是提供给你一个html文件，拷贝至`theme`下的`source`文件中，在`generate`的时候会原样生成。
**Tips:**同理，如果有每次生成都要保存的html文件，请都放置在此文件夹下。如果是`README.md`文件要保持不被渲染，请添加到`hexo/source/`下，并且在`hexo/_config.yml`中配置`skip_render: README.md`。
## 文章的发表
先生成draft，然后发表。
```bash
$ hexo new draft "title" #在source/_draft下生成md文件
$ hexo publish "title"
```
草稿默认不会显示在页面中，可在配置文件中把render_drafts 参数设为 true 来预览草稿。
**或**直接生成新文章。
```bash
$ hexo new "title" # 自动生成title.md在_posts下
```
如果要删除文章的话在source下删除文章后，重新生成和部署就可以了

## 参考资料
1. [hexo文档](https://hexo.io/zh-cn/docs)
2. [yelee主题配置帖](http://moxfive.coding.me/yelee)
