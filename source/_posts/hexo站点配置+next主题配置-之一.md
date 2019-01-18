---
title: hexo站点配置+next主题配置（一）
id: a-2019-01-10-15-31-50
tags:
  - 博客
  - hexo
  - next
  - 主题
categories:
  - 博客
keywords: 
  - 博客
  - hexo
  - next
  - 主题
description: hexo站点配置+next主题配置（一）
comments: true
copyright: true
date: 2019-01-10 15:31:50
---

<script type="text/javascript" src="/js/src/baidu.js"></script>


# 写在前面

大家一定要区分站点配置文件和主题配置文件所在位置，因为两个文件的文件名相同，都是 **_config.yml** ，刚开始接触时容易混淆。如果大家按照步骤做完之后，没能达到效果，请优先检查所配置项的格式，比如 **是否在需要加空格的地方忘记加空格**。

> **站点配置文件：** 位置在主目录下（主目录就是你博客初始化的目录）    
> **主题配置文件：** 位置在themes文件夹下面的相应主题根目录（比如next主题的配置文件在 `/themes/next` 下面）

<!-- more -->

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

## 3.1 选择主题风格 Scheme

Scheme 是 NexT 提供的一种特性，借助于 Scheme，NexT 为你提供多种不同的外观。同时，几乎所有的配置都可以 在 Scheme 之间共用。目前 NexT 支持四种 Scheme，他们是：

* Muse： 默认 Scheme，这是 NexT 最初的版本，黑白主调，大量留白
* Mist： Muse 的紧凑版本，整洁有序的单栏外观
* Pisces： 双栏 Scheme，小家碧玉似的清新
* Gemini： 双兰Scheme，简洁美观
Scheme 的切换通过更改 **主题配置文件**，搜索 scheme 关键字。你会看到有四行 scheme 的配置，将你需用启用的 scheme 前面注释 `#` 去除即可。

``` text
#scheme: Muse   
#scheme: Mist  
#scheme: Pisces
scheme: Gemini
```

## 3.2 设置语言

编辑 **站点配置文件**， 将 `language` 设置成你所需要的语言。建议明确设置你所需要的语言，例如选用简体中文，配置如下：

``` text
language: zh-Hans
```

目前 NexT 支持的语言如以下表格所示：

语言|代码|设定示例
---|---|---
English     |en       |language: en
简体中文    |zh-Hans  |language: zh-Hans
Français    |fr-FR    |language: fr-FR
Português   |pt       |language: pt or language: pt-BR
繁體中文    |zh-hk 或者 zh-tw |language: zh-hk
Русский язык|ru	      |language: ru
Deutsch     |de	      |language: de
日本語      |ja	      |language: ja
Indonesian  |id	      |language: id
Korean      |ko       |language: ko

## 3.3 设置菜单设置

菜单配置包括三个部分，第一是菜单项（名称和链接），第二是菜单项的显示文本，第三是菜单项对应的图标。 NexT 使用的是 [Font Awesome](http://fontawesome.io/) 提供的图标， `Font Awesome` 提供了 600+ 的图标，可以满足绝大的多数的场景，同时无须担心在 Retina 屏幕下 图标模糊的问题。

编辑 **主题配置文件**，修改以下内容：

**a.** 设定菜单内容，对应的字段是 menu。 菜单内容的设置格式是： **名称: 归档位置 || 图标**。其中名称并不直接显示在页面上，它将用于匹配图标以及翻译。默认是没有那么多菜单项的，下面会教大家怎么创建菜单项。

``` text
menu:
  home: / || home
  #about: /about/ || user
  #tags: /tags/ || tags
  #categories: /categories/ || th
  archives: /archives/ || archive
  #schedule: /schedule/ || calendar
  #sitemap: /sitemap.xml || sitemap
  #commonweal: /404/ || heartbeat
```

> 若你的站点运行在子目录中，请将链接前缀的 `/` 去掉

**b.** 添加标签、分类等页面
 
刚开始是没有那么多菜单项的，我们需要自己手动创建，比如添加 **分类（categories）** 页面，输入下面的指令

``` text
hexo new page categories
```

这时候会在hexo中的source目录下面生成一个 `categories` 文件夹，这是文件夹就是博客文章 **分类页面** 的归档。    
创建完成之后，我们需要在 **主题配置文件** 中，将 `menu` 中的 `categories` 前面的 `#` 去掉。重新 `hexo g -d` 部署后，就可以看到页面多了一个分类项。大家可以根据需要创建其它的菜单项，比如 `tags（标签）、about（关于）` 等等。

创建了这些菜单项有什么用呢？各个菜单项的用法各不相同，我只介绍三个常用的菜单项，**tags、archives、categories**。
这三个项是比较特殊的，他们的页面在 `/themes/layout/` 下面已经给出来了，就是相应的 `.swig` 文件。我们一般不用动它，只要知道如何使用就行了。

我们新建一片博客，只需要在博客开头给文章做好分类和标签即可，`archives` 不用关心，hexo自己会将你的博客归档。如下：

``` text
title: hexo站点配置+next主题配置
tags:
  - 博客
  - hexo
  - next
  - 主题
categories:
  - 博客
date: 2019-01-10 15:31:50
```

**c.** 设置菜单项的显示文本。在第一步和第二步中设置的菜单的名称并不直接用于界面上的展示。Hexo 在生成的时候将使用 这个名称查找对应的语言翻译，并提取显示文本。这些翻译文本放置在 NexT 主题目录下的 **languages/{language}.yml （{language} 为你所使用的语言）**。

以简体中文为例，若你需要添加一个菜单项，比如 `search`。那么就需要修改简体中文对应的翻译文件 `languages/zh-Hans.yml`，在 `menu` 字段下添加一项：

``` text
menu:
  home: 首页
  archives: 归档
  categories: 分类
  tags: 标签
  about: 关于
  search: 搜索
```

**d.** 设定菜单项图标。大家可以从 [Font Awesome](http://fontawesome.io/) 上查找喜欢的图标，并拷贝其图标名称，替换掉 `menu` 项中 `||` 后面的图标名称即可。

## 3.4 设置侧边栏位置及显示

默认情况下，侧栏仅在文章页面（拥有目录列表）时才显示，并放置于右侧位置。 可以通过修改 **主题配置文件** 中的 `sidebar`  字段来控制侧栏的行为。侧栏的设置包括两个部分，其一是侧栏的位置， 其二是侧栏显示的时机。

设置侧栏的位置，修改 `sidebar.position` 的值，支持的选项有：

* `left` - 靠左放置
* `right` - 靠右放置

``` text
sidebar:
  position: left
```

设置侧栏显示的时机，修改 `sidebar.display` 的值，支持的选项有：

* `post` - 默认行为，在文章页面（拥有目录列表）时显示
* `always` - 在所有页面中都显示
* `hide` - 在所有页面中都隐藏（可以手动展开）
* `remove` - 完全移除

``` text
sidebar:
  display: post
```

## 3.5 添加侧边栏社交链接

给侧边栏添加社交链接，可以通过修改 **主题配置文件** 中的 `social` 字段来添加。

``` text
#social:
  #GitHub: https://github.com/yourname || github
  #E-Mail: mailto:yourname@gmail.com || envelope
  #Weibo: https://weibo.com/yourname || weibo
  #Google: https://plus.google.com/yourname || google
  #Twitter: https://twitter.com/yourname || twitter
  #FB Page: https://www.facebook.com/yourname || facebook
  #VK Group: https://vk.com/yourname || vk
  #StackOverflow: https://stackoverflow.com/yourname || stack-overflow
  #YouTube: https://youtube.com/yourname || youtube
  #Instagram: https://instagram.com/yourname || instagram
  #Skype: skype:yourname?call|chat || skype
```

next主题默认是不打开 `social` 的，我们把 `social` 前面的 `#` 删掉，添加自己的社交链接即可，格式为 **“名称: 社交网址 || 图标”**, <font color=#FF0000>**注意空格**</font> 。

## 3.6 关闭网站动画效果

每次打开网页的时候，是不是感觉加载很慢？这是因为next主题的一些动画效果导致的，我们可以通过修改 **主题配置文件** 中的 `motion` 字段来关闭它。

``` text
motion:
  enable: false
```

部署后再打开网页试试，是不是快到飞起。

## 3.7 设置网站图标

自己的博客当然要用自己的网站图标，在没设置自己的网站图标时，浏览器的标签上显示的是next主题的图标 `N` ，下面是我自己设置的图标。

![](http://melville-images.oss-cn-shanghai.aliyuncs.com/blog/hexo%E7%AB%99%E7%82%B9%E9%85%8D%E7%BD%AE_next%E4%B8%BB%E9%A2%98%E9%85%8D%E7%BD%AE/%E7%BD%91%E7%AB%99%E5%9B%BE%E6%A0%87.png)

我们可以修改 **主题配置文件** 中的 `favicon` 字段来设置网站图标，首先我们需要先制作自己的图标，可以通过 [比特虫](http://www.bitbug.net/) 来制作图标，需要制作 `16x16` 和 `32x32` 两个大小的图标，分别重命名为 `favicon-16x16-next.ico` 和 `favicon-32x32-next.ico`,然后将两个图标文件放在 `/source/images` 目录下，并修改配置。

``` text
favicon:
  small: /images/favicon-16x16-next.ico
  medium: /images/favicon-32x32-next.ico
```

## 3.8 设置头像

选好自己喜欢的头像，将其命名为 `avatar.png` 也可以是 `.gif` 文件，并放在 `source/images` 目录下，修改 **主题配置文件** 的 `avatar` 字段。

``` text
# Sidebar Avatar
avatar:
  # in theme directory(source/images): /images/avatar.gif
  # in site  directory(source/uploads): /uploads/avatar.gif
  # You can also use other linking images.
  url: /images/avatar.png
```

比较老的next版本，是直接修改 `avatar` 字段

``` text
# Sidebar Avatar
# in theme directory(source/images): /images/avatar.gif
# in site  directory(source/uploads): /uploads/avatar.gif
avatar: /images/avatar.png
```

## 3.9 网页底部显示建站时间和图标修改

修改 **主题配置文件** 中的 `footer` 字段，将 `since` 修改成建站时间，`icon` 修改图标，可以从 [Font Awesome](http://fontawesome.io/) 中查找自己喜欢的图标。

``` text
footer:
  # Specify the date when the site was setup.
  # If not defined, current year will be used.
  since: 2018

  # Icon between year and copyright info.
  icon: snowflake-o      # snowflake-o是雪花的图标，默认是 user
```

## 3.10 去掉网站底部显示的 “由hexo强力驱动” 字样

修改 **主题配置文件** 中的 `footer` 字段，将 `powered` 设置为 `false`。

## 3.11 增加评论功能

next主题支持多种评论系统，像是多说、网易云跟帖、畅言、Gitment、Disqus、Hypercomments、valine，多说和网易云跟帖已经挂了，Gitment的服务器也不行了，Disqus、Hypercomments是国外的，速度慢，目前只有 `valine` 好用一些。首先进入 [leancloud官网](https://leancloud.cn/) 注册一个valine账号。

注册完之后需要创建应用，应用名字没有特别要求，然后进入应用，找到 **设置->应用Key** ，会看到有 `App ID` 和 `App Key` 。

![](http://melville-images.oss-cn-shanghai.aliyuncs.com/blog/hexo%E7%AB%99%E7%82%B9%E9%85%8D%E7%BD%AE_next%E4%B8%BB%E9%A2%98%E9%85%8D%E7%BD%AE/leancloud_ID.png)

进入 **主题配置文件** ，找到 `valine` 字段，将你的 `App ID` 和 `App Key` 分别填到 appid 和 appkey 的位置上，将 `enable` 改成 true。

``` text
valine:
  enable: true
  appid:  ???? # your leancloud application appid
  appkey: ???? # your leancloud application appkey
  notify: false # mail notifier , https://github.com/xCss/Valine/wiki
  verify: false # Verification code
  placeholder: 老铁，说点啥... # comment box placeholder
  avatar: mp # gravatar style
  guest_info: nick,mail,link # custom comment header
  pageSize: 10 # pagination size
```

然后进入 **设置->安全中心** ，服务开关只保留 **数据存储** 即可，Web安全域名中添加上自己的博客域名。

![](http://melville-images.oss-cn-shanghai.aliyuncs.com/blog/hexo%E7%AB%99%E7%82%B9%E9%85%8D%E7%BD%AE_next%E4%B8%BB%E9%A2%98%E9%85%8D%E7%BD%AE/leancloud_%E5%AE%89%E5%85%A8%E4%B8%AD%E5%BF%83.png)

修改完后，部署到网站上，此时文章底部应该出现了评论框。

## 3.12 禁用指定文章的评论功能

`valine` 评论系统默认所有页面都可以评论，包括关于页面、标签页面，很明显，我们并不是所有页面都需要评论功能。怎么关闭这些页面的评论功能呢？很简单，在文章开头加上 `comments` 限定就行。`"comments: false"` 关闭评论， `"comments: true"` 打开评论，比如禁止博客文章的评论：

``` text
title: hexo站点配置+next主题配置
tags:
  - 博客
  - hexo
  - next
  - 主题
categories:
  - 博客
comments: false
date: 2019-01-10 15:31:50
```

像是关于页面、标签页面，他们的文件是在 `source` 目录相应文件夹下的 `index.md` 文件。加上 `"comments: false"` 就可以了。

## 3.13 增加阅读统计

在 **3.11** 章节中我们创建了leancloud账户，阅读统计的功能使用leancloud就可以实现，登录leancloud，进入 **应用->存储->创建Class** ，

![](http://melville-images.oss-cn-shanghai.aliyuncs.com/blog/hexo%E7%AB%99%E7%82%B9%E9%85%8D%E7%BD%AE_next%E4%B8%BB%E9%A2%98%E9%85%8D%E7%BD%AE/leancloud_create_class.png)

创建Class时，名称一定要是 **Counter** ，这是为了适配 next 主题，由于LeanCloud升级了默认的ACL权限，如果你想避免后续因为权限的问题导致次数统计显示不正常，建议在此处选择 **无限制**。

![](http://melville-images.oss-cn-shanghai.aliyuncs.com/blog/hexo%E7%AB%99%E7%82%B9%E9%85%8D%E7%BD%AE_next%E4%B8%BB%E9%A2%98%E9%85%8D%E7%BD%AE/leancloud_create_class_name.png)

创建完成之后，左侧数据栏应该会多出一栏名为 **Counter** 的栏目，前面带下划线的栏目都是系统预定好的表。在 **主题配置文件** 中搜索 `leancloud_visitors` 修改配置，将你的 `appid` 和 `appkey` 填入相应位置。

``` text
leancloud_visitors:
  enable: true
  app_id: 
  app_key: 
```

## 3.14 开启微信支付宝打赏功能

越来越多的平台（微信公众平台，新浪微博，简书，百度打赏等）支持打赏功能，付费阅读时代越来越近，特此增加了打赏功能，支持微信打赏和支付宝打赏。只需要  **主题配置文件** 中填入微信和支付宝收款二维码图片地址，即可开启该功能。将微信和支付宝的收款二维码放在 `/themes/next/images` 目录下，并分别重命名为 **"wechatpay.jpg"** 和 **"alipay.jpg"** ，修改配置：

``` text
reward:
  enable: true
  wechatpay: /images/wechatpay.jpg
  alipay: /images/alipay.jpg
```

## 3.15 修改打赏部分字体动画

Next打赏部分的动画是鬼畜一般的不停地抖动，看着很难受，所以博主把它改为只循环三遍，打开文件 `themes/next/source/css/_common/components/post/post-reward.styl` ，把微信和支付宝的动画代码注释即可，如下：

``` text
/* 关闭文字闪动
#wechat:hover p{
    animation: roll 0.1s infinite linear;
    -webkit-animation: roll 0.1s infinite linear;
    -moz-animation: roll 0.1s infinite linear;
}
#alipay:hover p{
    animation: roll 0.1s infinite linear;
    -webkit-animation: roll 0.1s infinite linear;
    -moz-animation: roll 0.1s infinite linear;
}
*/
}
```

## 3.16 增加公益404页面

新建 `公益404页面` 

``` text
hexo new page 404
```

修改 `"/source/404/index.md"` ，添加以下代码：

``` text
<!DOCTYPE HTML>
<html>
<head>
  <meta http-equiv="content-type" content="text/html;charset=utf-8;"/>
  <meta http-equiv="X-UA-Compatible" content="IE=edge,chrome=1" />
  <meta name="robots" content="all" />
  <meta name="robots" content="index,follow"/>
  <link rel="stylesheet" type="text/css" href="https://qzone.qq.com/gy/404/style/404style.css">
</head>
<body>
  <script type="text/plain" src="http://www.qq.com/404/search_children.js"
          charset="utf-8" homePageUrl="/"
          homePageName="回到我的主页">
  </script>
  <script src="https://qzone.qq.com/gy/404/data.js" charset="utf-8"></script>
  <script src="https://qzone.qq.com/gy/404/page.js" charset="utf-8"></script>
</body>
</html>
```

在 **主题配置文件** 中，修改 `menu` ，添加404，如下：

``` text
menu:
  home: / || home
  tags: /tags/ || tags
  categories: /categories/ || th
  archives: /archives/ || archive
  about: /about/ || user
  commonweal: /404/ || heartbeat
```

[ **hexo站点配置+next主题配置（二）** ](https://melville.club/posts/a-2019-01-15-15-32-00.html)

---
