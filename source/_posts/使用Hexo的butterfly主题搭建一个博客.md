---
title: 使用Hexo的butterfly主题搭建一个博客
date: 2020-09-01 13:33:26
tags:
---

## 安装Git

#### 安装Git



Hexo 的主题需要通过 Git 来进行下载，同时生成的静态页面也需要部署到 GitHub Page，因此Git是必需的

网上 Git 的安装和配置教程很多，大家可根据自己的需求选择合适的教程，这是我推荐的一个安装及配置教程

[Git的下载、安装与环境配置](https://blog.csdn.net/huangqqdy/article/details/83032408)



------

## 安装npm

#### 安装npm



Hexo 是一个前端框架，因此 npm 就更是必需的了，相比于 Git，npm 的安装就更简单了，这里同样也给出一个安装教程

[npm的安装与配置](https://blog.csdn.net/u011342720/article/details/81267908)

一般情况下，执行到第 5 步，npm 就算安装完成了，如果你的 npm 下载速度太慢或者你有更高的要求的话，可以考虑继续后面的配置



------

## butterfly初体验

#### 创建 hexo 博客项目



butterfly 是 hexo 博客模板的一个主题，要是用 butterfly 主题，需要先创建一个 hexo 博客项目



**安装 hexo**

```
npm i -g hexo
```

**查看 hexo 版本**

```
hexo -v
```

**通过 hexo 创建一个博客项目**

```
hexo init 项目名
```

**下载项目所需的包**

```
cd 项目名
npm install
```



------

#### 替换博客主题



hexo 的默认博客主题是 landscape，我们需要将其替换为我们想要的博客主题



**下载博客主题**

在项目根目录下执行

```
git clone -b master https://github.com/jerryc127/hexo-theme-butterfly.git themes/butterfly
```

**复制配置文件**

将项目根目录下的 _config.yml 的配置文件复制一份，更名为 _config.butterfly.yml，之后就可以通过修改 _config.butterfly.yml 配置文件来自定义配置了。使用这种方式，不会在更新 Hexo 覆盖配置文件，当两个配置文件的配置冲突时，也会以 _config.butterfly.yml 的为准，此方法只支持 Hexo 5.0.0 及以上版本 

![1599311824](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/1599311824.jpg)

**修改配置文件**

修改 _config.butterfly.yml 的内容

```
# 修改主题
theme: butterfly
# 修改博客项目名
title: Simon的书柜
# 修改作者
author: Simon
# 修改语言为中文
language: zh-CN
# 修改博客顶部背景图
default_top_img: 图片路径
```

**启动项目**

```
# 清除缓存
hexo clean
# 生成静态化页面
hexo g
# 启动项目
hexo s
```

启动完成后，就可以在本地浏览器通过 http://localhost:4000/ 来访问自己的项目了



如果页面上出现的是一下内容的话，则需要安装 pug 及 stylus 的渲染器

extends includes/layout.pug block content include includes/recent-posts.pug include includes/partial

**安装指令**

```
npm install hexo-renderer-pug hexo-renderer-stylus --save
```

