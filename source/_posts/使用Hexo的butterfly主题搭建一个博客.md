---
title: 使用Hexo的butterfly主题搭建一个博客
date: 2020-09-01 13:33:26
top_img: https://simon-bookcase.oss-cn-beijing.aliyuncs.com/domenico-loia-hGV2TfOh0ns-unsplash.jpg
cover: https://simon-bookcase.oss-cn-beijing.aliyuncs.com/domenico-loia-hGV2TfOh0ns-unsplash.jpg
tags: Butterfly
categories: 
  - 博客
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

------



#### 将博客部署到Git

**新建Git仓库**

在 Git 上新建一个名为 *用户名.github.io* 的仓库，这种方式是最简单的

或者新建一个任意名称的仓库，然后去设置*里面将 Github Pages 的仓库地址改为该仓库，注意，一个 Git 账户只能有一个 Github Pages



**安装Git插件**

```
npm install --save hexo-deployer-git
```



**配置文件**

在 _config.butterfly.yml 配置文件中添加以下内容

```
deploy:
  type: git
  repo: https://github.com/CK-shadow/CK-shadow.github.io.git
  branch: master
```



**项目部署**

执行以下指定

```
hexo g -d
```

部署完成后，就可以通过 https://用户名.github.io/ 在浏览器上打开你的博客了

[Simon的书柜](https://ck-shadow.github.io/)



## 给博客添加标签和分类

#### Front-matter



我们可以在文章的 Front-matter 添加这篇文章的信息，可添加的内容有很多，一般主要用到的有

* title：文章标题（必需）

* date：文章创建日期（必需）

* updated：文章更新日期

* tags：标签

* categories：分类

* keywords：关键字

* description：文章描述

* top_img：文章顶部图片

* cover：文章略缩图

  

除此之外，还以很多其它的可选信息，大家可以去官网查看并添加自己想要的内容

------



#### 添加标签

**给文章添加响应的标签内容**

```
title: 使用Hexo的butterfly主题搭建一个博客
date: 2020-09-01 13:33:26
tags: Butterfly
```



**创建tags文件**

在博客根目录下输入以下命令

```
hexo new page tags
```



**修改文件**

接下来就可以在项目中找到 source/tags/index.md 这个文件了，我们需要在其中添加以下内容

```markdown
---
title: 标签
date: 2018-01-05 00:00:00
type: "tags"
---
```

接下来重启项目，就可以看到自己添加的标签了

------



#### 添加分类

**给文章添加响应的分类内容**

```
title: 使用Hexo的butterfly主题搭建一个博客
date: 2020-09-01 13:33:26
categories: 
  - 博客
```

文章可以支持多个分类，最终会是层级展示效果，语法为

```
categories: 
	- 分类A
	- 分类B
```



**创建 categories 文件**

在博客根目录下输入以下命令

```
hexo new page categories
```



**修改文件**

接下来就可以在项目中找到 source/categories/index.md 这个文件了，我们需要在其中添加以下内容

```markdown
---
title: 分类
date: 2018-01-05 00:00:00
type: "categories"
---
```

接下来重启项目，就可以看到自己添加的标签了

------



## 添加顶部导航栏



在 _config.butterfly.yml 中添加以下配置内容，重启项目，就可以在博客顶部看到添加的导航栏了

```
menu:
  首页: / || fas fa-home
  时间轴: /archives/ || fas fa-archive
  标签: /tags/ || fas fa-tags
  分类: /categories/ || fas fa-folder-open
  清单 ||fa fa-heartbeat:
    - 音乐 || /music/ || fas fa-music
    - 照片 || /Gallery/ || fas fa-images
    - 电影 || /movies/ || fas fa-video
  友链: /link/ || fas fa-link
  关于: /about/ || fas fa-heart
```

