---
title: 使用Gulp压缩Hexo博客静态资源
date: 2020-05-07 21:06:42
tags: [Hexo,Gulp]
categories: Hexo
copyright: true
---
## 概述

​	由于博客使用的插件较多，文章内包含的图片越多越大，会影响到博客的加载速度，影响访问效果。其中图片对文章加载速度影响较大，如果可以的话，可以使用国内的一些图床，但如果图床挂了，也会导致图片无法访问，迁移麻烦等，所以本博客还是挂在Github上进行访问。为此开始从资源文件大小上进行优化，了解到可以使用**Gulp**对博客的js、css、img、html等静态资源文件进行压缩。

<!-- more -->

## Gulp全局安装

```bash
$ npm install gulp -g
```

​	本博客安装的**Gulp**版本为4.0.2。

## Gulp插件安装

​	在`blog`文件夹(站点根目录)下，安装必备的插件：

```bash
$ npm install gulp gulp-minify-css gulp-uglify gulp-htmlmin gulp-htmlclean gulp-imagemin --save
```

​	安装完成后，可以在`package.json`下查看到具体的插件版本信息，本博客的插件版本对应信息如下：

```json
"dependencies": {
		"gulp": "^4.0.2",
    "gulp-htmlclean": "^2.7.22",
    "gulp-htmlmin": "^5.0.1",
    "gulp-imagemin": "^7.1.0",
    "gulp-minify-css": "^1.2.4",
    "gulp-uglify": "^3.0.2"
}
```

## 创建gulpfile.js文件

​	在`blog`文件夹(站点根目录)下，新建`gulpfile.js`文件，并编写如下内容：

```javascript
// 引入需要的模块
var gulp = require('gulp');
var minifycss = require('gulp-minify-css');
var uglify = require('gulp-uglify');
var htmlmin = require('gulp-htmlmin');
var htmlclean = require('gulp-htmlclean');
var imagemin = require('gulp-imagemin');

// 压缩public目录下所有html文件, minify-html是任务名, 设置为default，启动gulp压缩的时候可以省去任务名
gulp.task('minify-html', function() {
    return gulp.src('./public/**/*.html') // 压缩文件所在的目录
        .pipe(htmlclean())
        .pipe(htmlmin({
            removeComments: true,
            minifyJS: true,
            minifyCSS: true,
            minifyURLs: true,
        }))
        .pipe(gulp.dest('./public')) // 输出的目录
});

// 压缩css
gulp.task('minify-css', function() {
    return gulp.src(['./public/**/*.css','!./public/js/**/*min.css'])
        .pipe(minifycss({
            compatibility: 'ie8'
        }))
        .pipe(gulp.dest('./public'));
});
// 压缩js
gulp.task('minify-js', function() {
    return gulp.src(['./public/**/.js','!./public/js/**/*min.js'])
        .pipe(uglify())
        .pipe(gulp.dest('./public'));
});
// 压缩图片
gulp.task('minify-images', function() {
    return gulp.src(['./public/**/*.png','./public/**/*.jpg','./public/**/*.gif'])
        .pipe(imagemin(
        [imagemin.gifsicle({'optimizationLevel': 3}), 
        imagemin.mozjpeg({'progressive': true}), 
        imagemin.optipng({'optimizationLevel': 5}), 
        imagemin.svgo()],
        {'verbose': true}))
        .pipe(gulp.dest('./public'))
});
// gulp 4.0 适用的方式
gulp.task('default', gulp.parallel('minify-html','minify-css','minify-js','minify-images'
));
```

​	这里要注意，压缩过程中排除`*min.css`和`*min.js`这两类文件，因为这些文件其他人已经经过处理，不需要再进行压缩，否则可能无法正常使用。其他人网上的脚本使用的是`imagemin.jpegtran`，由于`gulp-imagemin`在7.0.0开始，已经被替换为 `mozjpeg`,具体可以在[release](https://github.com/sindresorhus/gulp-imagemin/releases)版本说明中查看。

## 压缩指令

```bash
$ hexo clean			// 可以先清除缓存文件和已生成的静态文件（特别是更换主题后需要执行此操作）
$ hexo generate		// 生成博客
$ gulp default		// 执行压缩，可简写为 gulp
$ hexo deploy			// 压缩完成无错误后，就可以发布了
```

​	在执行压缩过程中，可能会遇到压缩jpg、压缩png、压缩gif时的错误，或者提示类似于`imagemin-*`组件无法找到的错误。此时应该注意，`gulp-imagemin`也有对应的相关依赖，如本博客中版本为`7.1.0`，有以下特定版本的[依赖](https://github.com/sindresorhus/gulp-imagemin/commit/67cceb3cfcef05a9523f0a89c71d56e42e2c3981)：

```json
"optionalDependencies": {
		"imagemin-gifsicle": "^7.0.0",
		"imagemin-mozjpeg": "^8.0.0",
		"imagemin-optipng": "^7.0.0",
		"imagemin-svgo": "^7.0.0"
}
```

​	若对应依赖组件的版本有问题，可能会导致压缩对应格式的图片出错，为此建议当出现某个组件压缩失败时，手动安装对应特定的版本，`npm`安装软件包特定版本的命令格式如下：

```bash
$ npm install imagemin-mozjpeg@8.0.0 // 在软件包后面加上@版本号
```

## 效果

​	一次压缩过程输出内容如下：

```bash
$ gulp
[21:55:01] Using gulpfile ~/Documents/blog/gulpfile.js
[21:55:01] Starting 'default'...
[21:55:01] Starting 'minify-html'...
[21:55:01] Starting 'minify-css'...
[21:55:01] Starting 'minify-js'...
[21:55:01] Starting 'minify-images'...
[21:55:02] Finished 'minify-js' after 1.01 s
[21:55:03] Finished 'minify-css' after 1.75 s
[21:55:03] gulp-imagemin: ✔ images/apple-touch-icon-next.png (saved 190 B - 12.3%)
[21:55:03] gulp-imagemin: ✔ images/favicon-16x16-next.png (saved 150 B - 34.5%)
[21:55:03] gulp-imagemin: ✔ images/avatar.jpg (saved 3.69 kB - 18.6%)
[21:55:03] gulp-imagemin: ✔ images/favicon-32x32-next.png (saved 152 B - 23.8%)
[21:55:04] gulp-imagemin: ✔ 2020/05/03/hexo-install-and-deploy/20200503-114433.jpg (saved 51.1 kB - 54.5%)
[21:55:04] gulp-imagemin: ✔ 2020/05/03/hexo-install-and-deploy/20200503-202546.jpg (saved 21.3 kB - 47.3%)
[21:55:04] gulp-imagemin: ✔ 2020/05/03/hexo-install-and-deploy/20200503-200236.jpg (saved 40.2 kB - 51%)
[21:55:04] gulp-imagemin: ✔ 2020/05/03/hexo-install-and-deploy/20200503-193801.jpg (saved 51 kB - 53%)
[21:55:04] gulp-imagemin: ✔ 2020/05/03/hexo-install-and-deploy/20200503-111152.jpg (saved 80.1 kB - 56.7%)
[21:55:04] gulp-imagemin: ✔ images/avatar.gif (saved 8 B - 0.4%)
[21:55:04] gulp-imagemin: ✔ 2020/05/03/hexo-install-and-deploy/20200503-203256.jpg (saved 17.2 kB - 43.5%)
[21:55:04] gulp-imagemin: ✔ 2020/05/03/hexo-install-and-deploy/20200503-205440.jpg (saved 18.5 kB - 47.4%)
[21:55:04] gulp-imagemin: ✔ 2020/05/03/hexo-install-and-deploy/20200503-200812.jpg (saved 49.4 kB - 52.7%)
[21:55:04] gulp-imagemin: ✔ 2020/05/03/hexo-install-and-deploy/20200503-202952.jpg (saved 49.6 kB - 52.5%)
[21:55:04] gulp-imagemin: Minified 14 images (saved 383 kB - 51.3%)
[21:55:04] Finished 'minify-html' after 3.13 s
[21:55:04] Finished 'minify-images' after 3.13 s
[21:55:04] Finished 'default' after 3.14 s
```

​	`index.html`在压缩前的大小为`37,326`字节，压缩后为`33,537`字节；utils.js在压缩前的大小为15,982字节，压缩后仍为 `15,982`字节；main.css在压缩前大小为49,538字节，压缩后为`38,183`字节。

​	由本次压缩可看出，Gulp对图片的压缩效果比较明显，在html、css、js上也有一定的效果，总体上讲还是有比较好的优化。