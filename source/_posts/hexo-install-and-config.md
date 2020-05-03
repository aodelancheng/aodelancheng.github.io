---
title: 在GitHub上搭建Hexo个人博客
date: 2020-05-03 10:04:45
tags: hexo
copyright: true

---

## 概述

​	近来发现，之前学习过、实践过的东西，要用的时候经常一时想不起来。习惯性添加进谷歌浏览器的书签，也会因为对方博客文章删除等无法继续看到。特别的是，部署一个东西需要看几个网上资源才可以完成，书签列表越来越长，难以维护。为此，觉得还是很有必要通过写博客的方式进行分类记录，也方便分享一下一些经验给其他人。本着**能折腾就不闲着**的宗旨，在网上查阅有关资料后，决定在**GitHub**上搭建**Hexo**个人博客，顺便学习**Markdown**语法。

<!-- more -->

## 准备工作

1. 安装**Git**

   ​	在**Git**的官方[下载地址](https://git-scm.com/downloads)上选择要安装平台的安装文件进行安装

   ![Git官方下载地址截图](https://alderaan.xyz/2020/05/03/hexo-install-and-config/20200503-111152.png)

   ​	上述图片是在Macox上访问网页的截图，官网一般会自动检测当前电脑的系统，如果是windows上浏览网页右侧显示会略有不同，当然也可以在Downloads下选择特定平台的版本安装文件。

2. 安装**Node.js**

   ​	在**Node.js**的官方[下载地址](https://nodejs.org/en/)上选择要安装平台的安装文件进行安装

   ![Node.js官方下载地址截图](https://alderaan.xyz/2020/05/03/hexo-install-and-config/20200503-114433.png)

   ​	这里我选择的是12.16.3 LTS版本进行安装。

## 安装Hexo

1. 在命令行执行以下命令(Mac、Linux下需要加上sudo权限)

   > npm install -g hexo

2. 初始化

   ​	在磁盘上创建好一个文件夹(本文章文件夹名为blog)，用于保存网站文件，并cd到文件夹后，执行一下命令

   > cd blog
   >
   > hexo init

3. 生成默认网页

   > hexo g

4. 启动本地预览服务

   > hexo s

   ​	默认的本地预览服务地址为 http://localhost:4000 ，需要先确保端口4000没有被占用，以免影响访问网页。

5. 不出意外，可以看到如下默认网页(Hello World)

   

6. 修改主题

   ​	若对默认主题风格不满意，可以在hexo的[官网](https://hexo.io/themes/)上寻找自己喜欢的主题，并下载到theme目录下面。本博客使用的是NexT主题，可执行如下代码下载

   > git clone https://github.com/iissnan/hexo-theme-next themes/next

   ​	在 Hexo 中有两份主要的配置文件，其名称都是 `_config.yml`。 其中，一份位于站点根目录下，主要包含 Hexo 本身的配置；另一份位于主题目录下，这份配置由主题作者提供，主要用于配置主题相关的选项。

   ​	为了描述方便，在以下说明中，将前者称为 **站点配置文件**， 后者称为 **主题配置文件**。

   ​	打开 **站点配置文件**， 找到 `theme` 字段，并将其值更改为 `next`,以启用 NexT 主题

   > theme: next

   ​	在切换主题之后、验证之前， 我们最好使用 `hexo clean` 来清除 Hexo 的缓存。重新执行`hexo g`和`hexo s即可查看应用新主题后的默认网页`。

## 部署到GitHub



