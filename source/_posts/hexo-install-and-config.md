---
title: 在GitHub上搭建Hexo个人博客
date: 2020-05-03 10:04:45
tags: hexo
categories: hexo
copyright: true
---
# 概述

​	近来发现，之前学习过、实践过的东西，要用的时候经常一时想不起来。习惯性添加进谷歌浏览器的书签，也会因为对方博客文章删除等无法继续看到。特别的是，部署一个东西需要看几个网上资源才可以完成，书签列表越来越长，难以维护。为此，觉得还是很有必要通过写博客的方式进行分类记录，也方便分享经验。本着`能折腾就不闲着`的宗旨，在网上查阅有关资料后，决定在**GitHub**上搭建**Hexo**个人博客，顺便学习使用**Markdown**。

<!-- more -->

# 准备工作

## 安装**Git**

​	在**Git**的官方[下载地址](https://git-scm.com/downloads)上，选择要安装平台的安装文件进行安装

![Git官方下载地址截图](https://alderaan.xyz/2020/05/03/hexo-install-and-config/20200503-111152.jpg)

​	上述图片是在Mac OS上访问网页的截图，官网一般会自动检测当前电脑的系统，如果是Windows上浏览网页右侧显示会略有不同，当然也可以在Downloads下选择特定平台的版本安装文件。

## 安装**Node.js**

​	在**Node.js**的官方[下载地址](https://nodejs.org/en/)上，选择要安装平台的安装文件进行安装

![Node.js官方下载地址截图](https://alderaan.xyz/2020/05/03/hexo-install-and-config/20200503-114433.jpg)

​	这里我选择的是12.16.3 LTS版本进行安装。

# 安装Hexo

## 执行安装命令

​	在命令行执行以下命令(Mac、Linux下需要加上sudo权限)

```bash
$ npm install -g hexo
```

## 初始化网站

​	在磁盘上创建好一个文件夹(本文章文件夹名为blog)，用于保存网站文件，并切换到文件夹后，执行一下命令

```bash
$ cd blog
$ hexo init
```

## 生成默认网页

```bash
$ hexo g
```

## 启动本地预览服务

```bash
$ hexo s
```

​	默认的本地预览服务地址为 http://localhost:4000 ，需要先确保端口4000没有被占用，以免影响访问网页。不出意外，在浏览器上可以看到如下默认网页。

![Hexo博客默认主题Hello World页面](https://alderaan.xyz/2020/05/03/hexo-install-and-config/20200503-205440.jpg)

## 修改主题

​	如果对默认主题风格不满意，可以在hexo的[官网](https://hexo.io/themes/)上寻找自己喜欢的主题，并下载到theme目录下面。本博客使用的是NexT主题，可执行如下代码下载

```bash
$ git clone https://github.com/iissnan/hexo-theme-next themes/next # 若通过git clone下载，后续如果修改了主题配置，则无法提交修改后子模块源码到GitHub，建议下载后手动拷贝到themes文件夹下
```

​	在 Hexo 中有两份主要的配置文件，其名称都是 `_config.yml`。 其中，一份位于站点根目录下，主要包含 Hexo 本身的配置；另一份位于主题目录下，这份配置由主题作者提供，主要用于配置主题相关的选项。

​	为了描述方便，在以下说明中，将前者称为 **站点配置文件**， 后者称为 **主题配置文件**。打开 **站点配置文件**， 找到 `theme` 字段，并将其值更改为 `next`,以启用 NexT 主题

> theme: next

​	在切换主题之后、验证之前， 我们最好使用 `hexo clean` 来清除 Hexo 的缓存。重新执行`hexo g`和`hexo s`即可查看应用新主题后的默认网页。

# 部署到GitHub

## 配置免密SSH登陆

​	由于Hexo博客文章是通过提交代码实现，所以需要拥有github传输权限，但是直接使用用户名和密码不够安全，所以我们使用SSH Key来解决本地和服务器的连接问题。

```bash
$ cd ~/.ssh 
$ ls #检查本机已存在的ssh密钥
```

​	如果没有任何结果，说明你是第一次使用git；如果仅有known_hosts文件，没有包含`id_rsa`字样文件，则需要创建SSH Key：

```bash
$ ssh-keygen -t rsa -C "邮件地址"
```

​	然后连续3次回车(默认不添加密码)，最终会生成两个文件(`id_rsa`和`id_rsa.pub`)在用户目录下，打开用户目录，找到`.ssh/id_rsa.pub`文件，执行

```bash
$ cat id_rsa.pub
```

​	并复制里面的内容(`ssh-rsa`开头、你输入的`邮件地址`内容结尾)，打开你的github主页，进入Settings -> SSH and GPG keys -> New SSH key：

![SSH配置截图](https://alderaan.xyz/2020/05/03/hexo-install-and-config/20200503-193801.jpg)

​	将刚复制的内容粘贴到Key那里，Title按实际需要填写内容，保存后测试是否配置成功。

```bash
$ ssh -T git@github.com #注意地址不用改
```

​	如果提示`Are you sure you want to continue connecting (yes/no)?`，输入yes，如果返回如下信息，说明SSH已配置成功：

> Hi XXX! You've successfully authenticated, but GitHub does not provide shell access.

## 创建个人主页仓库

![创建Github仓库截图](https://alderaan.xyz/2020/05/03/hexo-install-and-config/20200503-202952.jpg)


​	这里需要注意的是，仓库名字格式为`xxx.github.io`，且前缀需要和Username一致，如果你的github账户名为blog，那么对应的的仓库名为blog.github.io。新建成功后，复制SSH地址。

![SSH地址截图](https://alderaan.xyz/2020/05/03/hexo-install-and-config/20200503-203256.jpg)

## 绑定个人域名(可选)

​	如果不绑定域名，只能通过默认的 `xxx.github.io` 来访问，如果你想更个性一点，可以在阿里云上注册一个域名(需要实名认证)，并到控制台->域名->解析->解析设置->添加记录。

​	域名解析配置最常见有2种方式，CNAME和A记录，CNAME填写域名，A记录填写IP，由于不带www方式只能采用A记录，所以必须先ping一下`你Username.github.io`的IP，然后到你的域名DNS设置页，将A记录指向你ping得到的IP，将CNAME指向`Username.github.io`，这样可以保证无论是否添加www都可以访问，如下所示：

![域名解析设置截图](https://alderaan.xyz/2020/05/03/hexo-install-and-config/20200503-200236.jpg)

​	此时需要到仓库下面，选择`Settings`->`options`，并下拉滚动条到**GitHub Pages**->`Custom domain`内填写上你的个人域名地址，如下所示：

![GitHub Pages配置截图](https://alderaan.xyz/2020/05/03/hexo-install-and-config/20200503-200812.jpg)

​	可以开启**Enforce HTTPS**，个人页面Github会自动添加HTTPS证书，不会提示连接不安全的问题。

> 此时还不能直接通过域名验证，由于阿里云免费的域名解析需要10分钟时间才能完全同步，且Github会自动申请添加HTTPS证书，需要一定时间。建议等待十分钟后，再查看是否可以正常访问。

## 上传Hexo生成的博客

​	hexo可以通过命令直接上传到git，但需要安装一个插件：

```bash
$ npm install hexo-deployer-git --save
```

​	安装后，打开**站点配置文件**，找到`deploy`部分，修改`repository`项内容：

```yaml
deploy:
	type: git  
	repository: git@github.com:Username/Username.github.io.git  
	branch: master
```

​	此时，到blog下输入`hexo d`就会将本次有改动的代码全部提交到`master`:

![hexo d命令结果截图](https://alderaan.xyz/2020/05/03/hexo-install-and-config/20200503-202546.jpg)

## 上传Hexo博客源码(可选)

​	为方便在MacOS、Windows不同电脑上随时编辑博客，且防止当前电脑故障，导致找不到Hexo配置及博文原始md文件，可以在存放Hexo博客生成的页面上创建一个新分支source，用于专门提交博客源码。

​	操作步骤如下：

```bash
$ cd blog # 切换到博客文件夹下
$ git init # 初始化git
$ git remote add origin https://github.com/Username/Username.github.io.git # 替换为你实际的git地址
$ git checkout -b source # 创建并切换到新分支source
$ git add . # 添加当前所有文件(Hexo博客源码自带上传规则，会自动排出public等博客会自动生成的文件)
$ git commit -m "your description" # 添加提交版本描述
$ git push origin source #提交Hexo博客源码到source分支
```

​	由于**站点配置文件**指定了上传博客网页的是master分支，所以提交Hexo博客源码切换的`source`分支，不会影响到`hexo d`的正常提交，两者互不冲突。

​	至此，Hexo博客的安装部署工作已经完成！