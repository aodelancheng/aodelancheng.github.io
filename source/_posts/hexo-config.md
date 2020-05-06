---
title: Hexo博客Next主题美化
date: 2020-05-06 15:01:22
tags: Hexo
categories: Hexo
copyright: true
---

​	本文主要说明在**Hexo**博客**nexT**主题下如何进行美化，具体对应效果可通过本博客页面进行查看。

- Hexo version： 4.2.0
- nexT version：7.8.0

<!-- more -->

---

## 更改nexT主题为中文

​	在**站点配置文件**中的`Site`配置进行如下修改：

```yaml
language: zh-CN # 简体中文
```

---

## 更改头像

​	将头像图片放在`/blog/themes/next/source/images/`下，然后在**主题配置文件**下搜索`avatar.gif`，修改为要替换的头像图片名称。

```yaml
# Replace the default image and set the url here.
url: /images/avatar.jpg # 头像图片名称 这里我的图片成名为avatar.jpg
```

---

## 更改头像显示框为圆形

​	在`/blog/themes/next/source/css/_common/outline/sidebar`文件夹下编辑`sidebar-author.styl`文件，做出如下修改：

```stylus
.site-author-image {
  border: $site-author-image-border-width solid $site-author-image-border-color;
  display: block;
  margin: 0 auto;
  max-width: $site-author-image-width;
  padding: 2px;
  
  /* 头像圆形 */
  border-radius: 50%; # 新增
  transition: 2s all; # 新增

  if (hexo-config('avatar.rounded')) {
    border-radius: 50%;
  }
}
```

---

## 更改博客标题和作者名称

​	在**站点配置文件**中的`Site`配置进行如下修改：

```yaml
# Site
title: Alderaan的博客 # 博客名称
subtitle: ''
description: ''
keywords:
author: Alderaan # 作者名称
language: zh-CN
timezone: ''
```

---

## 文章开启版权说明

​	新版**nexT**已增加版权说明模块，只需要在文章开头设置`copyright: true`即可，也可以修改`～/scaffolds/post.md` 文件，设置新建文章自动开启 `copyright`:

```yaml
---
title: {{ title }}
date: {{ date }}
copyright: true # 设置默认开启版权说明
---
```

---

## 添加「标签」页面

新建「标签」页面，并在菜单中显示「标签」链接。「标签」页面将展示站点的所有标签，若你的所有文章都未包含标签，此页面将是空的。 底下代码是一篇包含标签的文章的例子：

```
title: 标签测试文章
tags:
  - Testing
  - Another Tag
---
```

请参阅官方 [Hexo 的分类与标签文档](https://hexo.io/zh-cn/docs/front-matter.html#分类和标签)，了解如何为文章添加标签或者分类。

- [新建页面](http://theme-next.iissnan.com/theme-settings.html#new-page-tags)
- [设置页面类型](http://theme-next.iissnan.com/theme-settings.html#set-page-tags)
- [修改菜单](http://theme-next.iissnan.com/theme-settings.html#update-menu-for-tags-page)

在终端窗口下，定位到 Hexo 站点目录下。使用 `hexo new page` 新建一个页面，命名为 `tags` ：

```
$ cd your-hexo-site
$ hexo new page tags
```

**注意：**如果有集成评论服务，页面也会带有评论。 若需要关闭的话，请添加字段 `comments` 并将值设置为 `false`，如：

禁用评论示例

```
title: 标签
date: 2014-12-22 12:39:04
type: "tags"
comments: false
---
```

---

## 添加「分类」页面

​	新建「分类」页面，并在菜单中显示「分类」链接。「分类」页面将展示站点的所有分类，若你的所有文章都未包含分类，此页面将是空的。 底下代码是一篇包含分类的文章的例子：

```
title: 分类测试文章
categories: Testing
---
```

​	请参阅官方 [Hexo 的分类与标签文档](https://hexo.io/zh-cn/docs/front-matter.html#分类和标签)，了解如何为文章添加标签或者分类。

- [新建页面](http://theme-next.iissnan.com/theme-settings.html#new-page-categories)
- [设置页面类型](http://theme-next.iissnan.com/theme-settings.html#set-page-categories)
- [修改菜单](http://theme-next.iissnan.com/theme-settings.html#update-menu-for-categories-page)

  在终端窗口下，定位到 Hexo 站点目录下。使用 `hexo new page` 新建一个页面，命名为 `categories` ：

```
$ cd your-hexo-site
$ hexo new page categories
```

​	**注意：**如果有集成评论服务，页面也会带有评论。 若需要关闭的话，请添加字段 `comments` 并将值设置为 `false`，如：

​	禁用评论示例

```
title: 分类
date: 2014-12-22 12:39:04
type: "categories"
comments: false
---
```

---

## 开启网站计数

​	在**主题配置文件**中搜索`busuanzi_count`,做出如下修改：

```
# Show Views / Visitors of the website / page with busuanzi.
# Get more information on http://ibruce.info/2015/04/04/busuanzi
busuanzi_count:
  enable: true # 更改为true开启计数
  total_visitors: true
  total_visitors_icon: fa fa-user
  total_views: true
  total_views_icon: fa fa-eye
  post_views: true
  post_views_icon: fa fa-eye
```

---

## 开启文章底部标签图标显示

​	在**主题配置文件**中搜索`tag_icon`，做出如下修改：

```
# Use icon instead of the symbol # to indicate the tag at the bottom of the post
tag_icon: true # 更改为true开启图标显示，默认为'#'号
```

---

## 添加Live2D宠物

​	首先在博客目录下执行：

```cmd
#cmd 进入博客根目录
#安装 hexo-helper-live2d
npm install hexo-helper-live2d -save
```

​	在**站点配置文件**尾部新增：

```yml
# Live2d
live2d:
  enable: true
  scriptFrom: local
  pluginRootPath: live2dw/
  pluginJsPath: lib/
  pluginModelPath: assets/
  model:
    use: live2d-widget-model-hijiki
  display:
    position: right
    width: 150
    height: 300
  mobile:
    show: false
```

​	`npm`安装需要的宠物文件

```undefined
npm install {packagename} 
```

​	如本博客宠物名为hijiki， 则为 `npm install live2d-widget-model-hijiki`,其他宠物包点击[live2d-widget-models](https://github.com/xiazeyu/live2d-widget-models)。详细内容可参考[**hexo-helper-live2d**](https://github.com/EYHN/hexo-helper-live2d)。

---