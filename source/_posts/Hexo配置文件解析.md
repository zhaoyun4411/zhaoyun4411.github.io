---
title: Hexo配置文件解析
date: 2018-03-25 16:41:38
tags:
  - Hexo
  - github
  - gitpage
  - 博客
  - 配置文件
  - 模版
---

在前面一章讲解了如何通过Hexo搭建个人博客，并将博客部署到github上面。但是部署完成会发现博客页面中存在许多不尽人意的地方，这里可以通过Hexo文件目录下的配置文件进行修改，，或者通过更换主题文件实现个人博客网站界面的修改。

<!--more-->

## hexo的配置文件
hexo中主要存在两个配置文件，包括整个博客系统的配置文件`<博客目录>\_config.yml`和博客的主题配置文件`<博客目录>\themes\<主题名称>\_config.yml`下面将首先介绍博客系统的配置文件。(参考自hexo官方网站[hexo官方网站关于配置文件的描述](https://hexo.io/zh-cn/docs/configuration.html))

```yml
# Hexo Configuration
## Docs: https://hexo.io/docs/configuration.html
## Source: https://github.com/hexojs/hexo/

# Site
title: #网站标题
subtitle: #网站副标题
description: #网站描述
keywords: # 关键字
author: #您的名字
language: zh-CN #网站使用的语言
timezone: Asia/Hong_Kong	#网站使用的时区，默认为电脑时区

# URL 暂不使用
## If your site is put in a subdirectory, set url as 'http://yoursite.com/child' and root as '/child/'
url: http://yoursite.com
root: /
permalink: :year/:month/:day/:title/ #文章的永久链接格式
permalink_defaults: #文章永久链接格式中各部分的默认值

# Directory 暂不配置使用默认值
source_dir: source
public_dir: public
tag_dir: tags
archive_dir: archives
category_dir: categories
code_dir: downloads/code
i18n_dir: :lang
skip_render:

# Writing 文章布局等这里使用默认值
new_post_name: :title.md # File name of new posts
default_layout: post
titlecase: false # Transform title into titlecase
external_link: true # Open external links in new tab
filename_case: 0
render_drafts: false
post_asset_folder: false
relative_link: false
future: true
highlight:
  enable: true
  line_number: true
  auto_detect: false
  tab_replace:

# Home page setting 主页设置，暂不配置使用默认值
# path: Root path for your blogs index page. (default = '')
# per_page: Posts displayed per page. (0 = disable pagination)
# order_by: Posts order. (Order by date descending by default)
index_generator:
  path: ''
  per_page: 10
  order_by: -date

# Category & Tag #分类和标签页
default_category: uncategorized
category_map:
tag_map:

# Date / Time format # 时间格式使用默认值
## Hexo uses Moment.js to parse and display date
## You can customize the date format as defined in
## http://momentjs.com/docs/#/displaying/format/
date_format: YYYY-MM-DD
time_format: HH:mm:ss

# Pagination 分页
## Set per_page to 0 to disable pagination
per_page: 10
pagination_dir: page

# Extensions 扩展
## Plugins: https://hexo.io/plugins/
## Themes: https://hexo.io/themes/
theme: next

# Deployment 部署
## Docs: https://hexo.io/docs/deployment.html
deploy:
  type: git
  repository:
  branch: master

```

## 配置自己的主题

可以在[Hexo官方的主题页面](https://hexo.io/themes/)下载喜欢的主题代码。下面将以使用最广泛的next主题为例介绍如何配置自己的主题。这里主要参考[next官方帮助文档](http://theme-next.iissnan.com/getting-started.html)。

### 安装Next主题
Hexo的主题安装非常简单，只需要将主题文件下载并拷贝到站点的themes目录下，然后修改配置文件即可。如通过下面命令：
```shell
cd <您的博客目录>
git clone git@github.com:theme-next/hexo-theme-next.git
```

### 启用主题
下载完成后进入站点配置文件，将主题的配置项修改为next（参见第一章站点配置文件的讲解）既可以实现将主题切换为next，这里可以通过本地的静态页面验证是否成功切换。

### 主题配置

#### 设置菜单


* 编辑主题配置文件，设定菜单内容对应的字段的是menu。菜单格式的内容为`item name: link`。其中`item name`是一个名称，这个名称不直接显示在页面上，用于匹配图标以及国际化文件。

| item name | link | 显示文本（中文） |
|:---------:|:----:|:-------------:|
| home | / | 主页 |
| archives | /archives | 归档页 |
| categories | /categories | 分类页 |
| tags | /tags | 标签页 |
| about | /about | 关于页面 |
| commonweal | /404.html | 公益404 |
* 设置菜单的显示文本，第一步中设计的菜单名称并没有直接用于菜单界面中文本的显示。Hexo在编译时使用这个名称查找对应的国际化文件。这个国际化文件放在Next主题目录中的`language\{language}.yml`文件下{language}为您使用的语言名字。


 如果需要增加一个菜单选项，比如something。那么就需要在翻译文件的menu字段下面添加对应的显示文本。

 ```yml
menu:
  home: 主页
  archives: 归档页
  categories: 分类页
  tags: 标签页
  about: 关于
  search: 搜索
  commonweal: 公益404
  something: 其他
 ```
* 设定菜单标签页的图标，对应的字段时menu_icons，这块设定的格式为`item name: icon name`，这其中`item name`与上面配置的菜单名字对应，`icon name`是对应的Font Awesome图标的名字。

### Hexo基本命令

* hexo clean : 清除编译生成的文件，通常切换主题时需要执行此命令使主题生效
* hexo g : 编译命令
* hexo d : 将页面推送到git上面
* hexo s : 生成本地静态页面用于调试，页面地址 http://localhost:4000/
* hexo new <文章名字> : 新建博客

### 发表一篇文章

在控制台中执行`hexo new <文章名字>`即可创建一篇新的博客文章采用Atom编辑器打开对应的文档，文档位于sourc\_posts目录下，文档结构如下：
```markdown
title: <文章名字>
date: 2018-03-27 22:56:29 #发表日期，一般不改动
tags:
  - 标签1
  - 标签2
---
#这里是正文，用markdown写，你可以选择写一段显示在首页的简介后，加上
<!--more-->#在<!--more-->之前的内容会显示在首页，之后的内容会被隐藏，当游客点击Read more才能看到。
```
