---
title: hexo站点配置+next主题配置（二）
id: a-2019-01-15-15-32-00
tags:
  - 博客
  - hexo
  - next
  - 主题
categories:
  - 博客
keywords: [博客,hexo,next,主题]
description: hexo站点配置和next主题的配置
comments: true
copyright: true
date: 2019-01-15 15:32:00
---

<script type="text/javascript" src="/js/src/baidu.js"></script>

[ **hexo站点配置+next主题配置（一）** ](https://melville.club/posts/a-2019-01-10-15-31-50.html)

## 3.17 设置RSS

先安装 `hexo-generator-feed` 插件。在站点根目录打开 `Git Bash Here` ，安装插件：

``` text
npm install --save hexo-generator-feed
```

打开 **站点配置文件** ，添加插件

``` text
# Extensions
Plugins: 
- hexo-generate-feed
```
<!-- more -->

打开 **主题配置文件** ，修改 rss：

``` text
# Set rss to false to disable feed link.
# Leave rss as empty to use site's feed link.
# Set rss to specific value if you have burned your feed already.
rss: /atom.xml
```

## 3.18 侧边栏友情链接

打开 **主题配置文件** ，搜索 `links_icon` ，根据自己的需要添加友情链接，也可以把标题换成 **推荐阅读**：

``` text
# Blog rolls
links_icon: link           # 图标
links_title: 友情链接      # 标题
links_layout: block
#links_layout: inline
links:
    #Title1: http://example1.com
    #Title2: http://example2.com
```

## 3.19 增加背景动画

next主题提供了四种背景动画，想使用哪一种就把它的值改称 `true` ，打开 **主题配置文件** ，修改如下配置

``` text
# Canvas-nest
canvas_nest: true

# three_waves
three_waves: false

# canvas_lines
canvas_lines: false

# canvas_sphere
canvas_sphere: false
```

## 3.20 在文章底部增加版权信息

在目录 `next/layout/_macro/` 下添加 `my-copyright.swig` :

``` html
{% if page.copyright %}
<div class="my_post_copyright">
  <script src="//cdn.bootcss.com/clipboard.js/1.5.10/clipboard.min.js"></script>
  
  <!-- JS库 sweetalert 可修改路径 -->
  <script src="https://cdn.bootcss.com/jquery/2.0.0/jquery.min.js"></script>
  <script src="https://unpkg.com/sweetalert/dist/sweetalert.min.js"></script>
  <p><span>本文标题:</span><a href="{{ url_for(page.path) }}">{{ page.title }}</a></p>
  <p><span>文章作者:</span><a href="/" title="访问 {{ theme.author }} 的个人博客">{{ theme.author }}</a></p>
  <p><span>发布时间:</span>{{ page.date.format("YYYY年MM月DD日 - HH:mm") }}</p>
  <p><span>最后更新:</span>{{ page.updated.format("YYYY年MM月DD日 - HH:mm") }}</p>
  <p><span>原始链接:</span><a href="{{ url_for(page.path) }}" title="{{ page.title }}">{{ page.permalink }}</a>
    <span class="copy-path"  title="点击复制文章链接"><i class="fa fa-clipboard" data-clipboard-text="{{ page.permalink }}"  aria-label="复制成功！"></i></span>
  </p>
  <p><span>许可协议:</span><i class="fa fa-creative-commons"></i> <a rel="license" href="https://creativecommons.org/licenses/by-nc-nd/4.0/" target="_blank" title="Attribution-NonCommercial-NoDerivatives 4.0 International (CC BY-NC-ND 4.0)">署名-非商业性使用-禁止演绎 4.0 国际</a> 转载请保留原文链接及作者。</p>  
</div>
<script> 
    var clipboard = new Clipboard('.fa-clipboard');
      $(".fa-clipboard").click(function(){
      clipboard.on('success', function(){
        swal({   
          title: "",   
          text: '复制成功',
          icon: "success", 
          showConfirmButton: true
          });
        });
    });  
</script>
{% endif %}
```

在目录 `next/source/css/_common/components/post/` 下添加 `my-post-copyright.styl` ：

``` html
.my_post_copyright {
  width: 85%;
  max-width: 45em;
  margin: 2.8em auto 0;
  padding: 0.5em 1.0em;
  border: 1px solid #d3d3d3;
  font-size: 0.93rem;
  line-height: 1.6em;
  word-break: break-all;
  background: rgba(255,255,255,0.4);
}
.my_post_copyright p{margin:0;}
.my_post_copyright span {
  display: inline-block;
  width: 5.2em;
  color: #b5b5b5;
  font-weight: bold;
}
.my_post_copyright .raw {
  margin-left: 1em;
  width: 5em;
}
.my_post_copyright a {
  color: #808080;
  border-bottom:0;
}
.my_post_copyright a:hover {
  color: #a3d2a3;
  text-decoration: underline;
}
.my_post_copyright:hover .fa-clipboard {
  color: #000;
}
.my_post_copyright .post-url:hover {
  font-weight: normal;
}
.my_post_copyright .copy-path {
  margin-left: 1em;
  width: 1em;
  +mobile(){display:none;}
}
.my_post_copyright .copy-path:hover {
  color: #808080;
  cursor: pointer;
}
```

修改 `next/layout/_macro/post.swig` :

``` html
<div>
      {% if not is_index %}
        {% include 'my-copyright.swig' %}
      {% endif %}
</div>

# ---------在下面的代码之前，添加上面部分的代码----------
<div>
      {% if not is_index %}
        {% include 'wechat-subscriber.swig' %}
      {% endif %}
</div>
# ------------------------------------------------------
```

修改 `next/source/css/_common/components/post/post.styl` 文件，在最后一行增加代码：

``` html
@import "my-post-copyright"
```

写博客的时候，需要添加 `copyright` 做限制。

``` text
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
copyright: true
date: 2019-01-10 15:31:50
---
```

## 3.21 DaoVoice在线联系

在 [Daovoice官网](https://dashboard.daocloud.io) 注册账户，注册需要邀请码 `b3c7d22e` ，注册完成之后会获得一个 `appid` ，位置在

![](http://melville-images.oss-cn-shanghai.aliyuncs.com/blog/hexo%E7%AB%99%E7%82%B9%E9%85%8D%E7%BD%AE_next%E4%B8%BB%E9%A2%98%E9%85%8D%E7%BD%AE/DaoVoice_app_id.png)

然后打开 `themes/next/layout/_partials/head.swig` 文件，在文件中加入以下代码，位置没有要求。

``` html
{% if theme.daovoice %}
  <script>
  (function(i,s,o,g,r,a,m){i["DaoVoiceObject"]=r;i[r]=i[r]||function(){(i[r].q=i[r].q||[]).push(arguments)},i[r].l=1*new Date();a=s.createElement(o),m=s.getElementsByTagName(o)[0];a.async=1;a.src=g;a.charset="utf-8";m.parentNode.insertBefore(a,m)})(window,document,"script",('https:' == document.location.protocol ? 'https:' : 'http:') + "//widget.daovoice.io/widget/faff8786.js","daovoice")
  daovoice('init', {
      app_id: "{{theme.daovoice_app_id}}"
    });
  daovoice('update');
  </script>
{% endif %}
```

下面是我的配置

![](http://melville-images.oss-cn-shanghai.aliyuncs.com/blog/hexo%E7%AB%99%E7%82%B9%E9%85%8D%E7%BD%AE_next%E4%B8%BB%E9%A2%98%E9%85%8D%E7%BD%AE/DaoVoice_location.png)

打开 **主题配置文件** 添加以下代码（将 `daovoice_app_id` 替换成你自己的）

``` python
# Online contact 
daovoice: true
daovoice_app_id: ffffffff
```

保存后部署，就可以看到效果了，在  [Daovoice](https://dashboard.daocloud.io) 上可以修改聊天窗口的颜色和位置，可以关联微信小程序，使用微信来接收消息。大家自己摸索。


## 3.22 点击出现小爱心效果



## 3.23 添加站内搜索功能

修改 **主题配置文件** 中的 `local_search` 字段，将 `enable` 的值改为 `true` 。这个是添加本地搜索的功能，网上还有 `algolia_search` 搜索，大家感兴趣的话可以自己学习，不过我感觉本地搜索够用了。

``` text
local_search:
  enable: true
  # if auto, trigger search by changing input
  # if manual, trigger search by pressing enter key or search button
  trigger: auto
  # show top n results per article, show all results by setting to -1
  top_n_per_article: 1
```

## 3.24 去掉文章目录标题的自动编号

修改 **主题配置文件** 中的 `toc` 字段，将 `number` 的值改称 `false`。

``` text
toc:
  enable: true
  # Automatically add list number to toc.
  number: false
```

## 3.25 修改文章底部 # 号标签的图标

去掉文章后面的标签前面的#号。
修改模板/themes/next/layout/_macro/post.swig，搜索 rel="tag">#，将#换成<i class="fa fa-tag"></i>

## 3.26 文章末尾添加“本文结束”标记

在路径 `\themes\next\layout\_macro` 中新建 `passage-end-tag.swig` 文件,并添加以下内容：

``` text
<div>
    {% if not is_index %}
        <div style="text-align:center;color: #636363;font-size:14px;letter-spacing: 10px">本文结束<i class="fa fa-bell"></i>感谢您的阅读</div>
    {% endif %}
</div>
```

接着打开 `\themes\next\layout\_macro\post.swig` 文件，搜索 `END POST BODY` ，在 这之后， 添加以下代码：

``` text
<div>
{% if not is_index %}
{% include 'passage-end-tag.swig' %}
{% endif %}
</div>
```

![](http://melville-images.oss-cn-shanghai.aliyuncs.com/blog/hexo%E7%AB%99%E7%82%B9%E9%85%8D%E7%BD%AE_next%E4%B8%BB%E9%A2%98%E9%85%8D%E7%BD%AE/%E6%96%87%E7%AB%A0%E5%BA%95%E9%83%A8%E5%8A%A0%E7%BB%93%E6%9D%9F%E6%A0%87%E8%AE%B0.png)

打开 **主题配置文件** ，在末尾添加

``` text
passage_end_tag:
  enabled: true
```

## 3.27 修改``代码块自定义样式

打开 `\themes\next\source\css\_custom\custom.styl` 文件，加入

``` html
code {
    color: #ff7600;
    background: #fbf7f8;
    margin: 2px;
}
// 大代码块的自定义样式
.highlight, pre {
    margin: 5px 0;
    padding: 5px;
    border-radius: 3px;
}
.highlight, code, pre {
    border: 1px solid #d6d6d6;
}
```

`color` 可以更改成自己喜欢的颜色。


---
