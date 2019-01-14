---
title: hexo站点配置+next主题配置
tags:
  - 博客
  - hexo
  - next
  - 主题
categories:
  - 博客
comments: true
date: 2019-01-10 15:31:50
---


# 写在前面

大家一定要区分站点配置文件和主题配置文件所在位置，因为两个文件的文件名相同，都是 **_config.yml** ，刚开始接触时容易混淆。

> **站点配置文件：** 位置在主目录下（主目录就是你博客初始化的目录）    
> **主题配置文件：** 位置在themes文件夹下面的相应主题根目录（比如next主题的配置文件在 `/themes/next` 下面）

# 1. 下载主题

 **[hexo主题下载](https://hexo.io/themes/)** ，里面有很多hexo的主题，选择一款喜欢的主题，点进去，会跳转到该主题的GitHub页面，download到本地。并将主题 **文件夹** 放到themes文件夹下面。然后在站点配置文件中更改为该主题，就可以使用了，下面会介绍如何更改。

# 2. 站点配置

## 2.1 站点文件的基本配置参数

**网站**

参数 | 描述 | 默认值
---|---|---
title       | 网站标题       |
subtitle    | 网站副标题     |
description | 网站描述       |
keywords    | 网站关键词     |
author      | 你的名字       |
language    | 网站使用的语言 |
timezone    | 网站时区       | Hexo 默认使用您电脑的时区

**网址**

参数|	描述	|默认值
---|---|---
url	                |网址                     |
root                |网站根目录               | 
permalink           |文章的 永久链接 格式     |:year/:month/:day/:title/
permalink_defaults	|永久链接中各部分的默认值 |	

> 如果您的网站存放在子目录中，例如 http://yoursite.com/blog，
则请将您的 url 设为 http://yoursite.com/blog 并把 root 设为 /blog/。

**目录**

参数|描述|默认值
---|---|---
source_dir    |资源文件夹，这个文件夹用来存放内容               |source
public_dir    |公共文件夹，这个文件夹用于存放生成的站点文件。   |public
tag_dir       |标签文件夹                                       |tags
archive_dir   |归档文件夹                                       |archives
category_dir  |分类文件夹                                       |categories
code_dir      |Include code 文件夹                              |downloads/code
i18n_dir      |国际化（i18n）文件夹	                            |:lang
skip_render   |跳过指定文件的渲染，您可使用 glob 表达式来匹配路径。	

> **注意：** 如果您刚刚开始接触Hexo，通常没有必要修改这一部分的值。

**文章**

参数|描述|默认值
---|---|---
new_post_name     |新文章的文件名称          |:title.md
default_layout    |预设布局                  |post
auto_spacing      |在中文和英文之间加入空格  |false
titlecase         |把标题转换为 title case   |false
external_link     |在新标签中打开链接        |true
filename_case     |把文件名称转换为 (1) 小写或 (2) 大写 |0
render_drafts     |显示草稿                  |false
post_asset_folder |启动 Asset 文件夹         |false
relative_link     |把链接改为与根目录的相对位址 |false
future            |显示未来的文章            |true
highlight         |代码块的设置

> **相对地址：** 默认情况下，Hexo生成的超链接都是绝对地址。例如，如果您的网站域名为 example.com，您有一篇文章名为hello，那么绝对链接可能像这样：http://example.com/hello.html， 它是绝对于域名的。相对链接像这样：/hello.html，也就是说，无论用什么域名访问该站点，都没有关系，这在进行反向代理时可能用到。通常情况下，建议使用绝对地址。

**分类 & 标签**

参数|描述|默认值
---|---|---
default_category  |默认分类   |uncategorized
category_map      |分类别名   |
tag_map           |标签别名   |

**日期 / 时间格式**

参数|描述|默认值
---|---|---
date_format |日期格式	|YYYY-MM-DD
time_format |时间格式	|HH:mm:ss

> Hexo 使用 Moment.js 来解析和显示时间。

**分页**

参数|描述|默认值
---|---|---
per_page       |每页显示的文章量 (0 = 关闭分页功能)	|10
pagination_dir |分页目录                            |page

**扩展**

参数|描述|备注
---|---|---
Plugins|插件|
Themes|当前主题名称|值为false时禁用主题

**部署**

参数|描述
---|---
type|部署类型
repo|仓库地址
branch|仓库分支

## 2.2 参数配置

我只介绍平时比较关注的配置，其它配置项大家自己摸索。

### 2.2.1 配置网站

以下是我的网站配置

``` text
title: Melville's blog
subtitle: 个人博客
description: 改革春风吹满地
keywords:
author: Melville
language: zh-Hans         # 网站语言设置为中文
timezone:
```

### 2.2.2 配置网址

以下是我的网址配置，**URL** 配置成自己的网址，[https:melville.club](https:melville.club) 是我的自定义域名，如果没有自定义域名，配置成GitHub Pages分配的网址即可。

``` text
url: https:melville.club
root: /
permalink: :title.html
permalink_defaults:
```

### 2.2.3 配置主题

默认主题是 **landscape**，替换成自己下载的主题的 **文件夹的名字** （如果主题文件夹的名字不是主题的名字，建议改成一致），我的是next主题（刚下载下来时，主题文件夹的名字是 **hexo-theme-next-master** ，我将其改成了 **next**），所以我的 **themes** 文件夹下面有 `/themes/landscape` 和 `/themes/next` 两个主题。使用一个主题，将其它主题注掉即可。

``` text
## theme: landscape
theme: next
```

### 2.2.4 配置部署参数

以下是我的部署参数配置，**repo** 在GitHub新建的GitHub Pages仓库中获取。

``` text
deploy:
  type: git
  repo: git@github.com:do-do-do/do-do-do.github.io.git
  branch: master
```

# 3. 主题配置

接下来就可以进行主题的配置了，本文以next主题为例子，其它主题配置方法类似。

## 3.1 增加阅读统计

添加RSS
添加标签、分类等页面
设置网站icon
添加侧边栏社交链接
添加侧边栏友情链接
底部显示建站时间和图标的修改
微信支付宝打赏功能
关闭网站动画效果
设置第三方JS库
添加评论系统
统计访客量以及文章阅读量
阅读次数统计（基于LanCloud）
字数统计
增加版权信息
添加文章分享功能
文章排序优先级设置
添加站内搜索功能
DaoVoice在线联系

2 个性化优化

设置字体
设置代码高亮主题
左上角或右上角的Github样式
添加背景动画
添加顶部加载条
点击出现小爱心效果
修改文章链接文本样式
修改文章底部标签样式
文章末尾统一添加“文本结束”标记
修改作者头像并旋转
文章添加阴影效果
修改打赏部分字体动画
自定义鼠标样式
添加看板娘


---