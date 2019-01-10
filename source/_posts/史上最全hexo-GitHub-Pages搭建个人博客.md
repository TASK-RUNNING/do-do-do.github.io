---
title: 史上最全hexo+GitHubPages搭建个人博客
tags:
  - 博客
  - hexo
  - next
categories:
  - 博客
comments: false
date: 2019-01-08 14:12:57
---

# 前言
反反复复搞了很多天，终于搭建好自己的博客了，因为要配置的个性化东西很多，而且网上查找的结果都是比较零散的，所以我搭建博客时浪费了很多时间，也走了很多的坑。为了节约大家的时间，方便大家搭建自己的博客，特地记录下搭建的过程。如果大家在搭建过程中有什么疑问，**欢迎留言**，同时也请大家收藏网站，防止自己忘记!!!

# 1. GitHub Pages和hexo简介
> GitHub Pages旨在从GitHub存储库托管您的个人，组织或项目页面，是为您和您的项目快速发布漂亮网站的最佳方式。使用GitHub Pages搭建个人网站，可以省去购买服务器、域名等一系列费用，搭建起来非常简单。而且可以上传网页的源代码、更改网页的样式，全部在自己的掌控之中。

> Hexo 是一款基于Node.js的快速、简洁且高效的博客框架。Hexo 使用 Markdown（或其他渲染引擎）解析文章，在几秒内，即可利用靓丽的主题生成静态网页。依赖少易于安装使用，是搭建博客的首选框架。

# 2. 创建GitHub Pages
## 2.1 注册GitHub
如果你看到这篇文章，90%的可能性已经是GitHub会员了，这里就不介绍注册步骤了，**[GitHub入口](https://github.com/)。**

## 2.2 创建仓库
进入GitHub个人页面，点击右上角的 **+** 号新建仓库。
![](http://melville-images.oss-cn-shanghai.aliyuncs.com/blog/%E5%8F%B2%E4%B8%8A%E6%9C%80%E5%85%A8hexo-GitHubPages%E6%90%AD%E5%BB%BA%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2/%E5%88%9B%E5%BB%BA%E4%BB%93%E5%BA%93.png)

在新建仓库页面，输入仓库名字，选择pulic和初始化README，点击 `Create repository` 新建仓库，如下图所示。
<font color=#FF0000>注意：仓库名字一定要和自己的用户名一样，规则就是 `用户名.github.io`。不这样命名会出错。</font>
<img src="http://melville-images.oss-cn-shanghai.aliyuncs.com/blog/%E5%8F%B2%E4%B8%8A%E6%9C%80%E5%85%A8hexo-GitHubPages%E6%90%AD%E5%BB%BA%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2/%E5%88%9B%E5%BB%BA%E4%BB%93%E5%BA%93%E5%90%8D%E5%AD%97.png" width=60% height=60%>

创建完成后，在个人仓库页面会出现刚才创建的仓库，点击仓库名称进入仓库。
<img src="http://melville-images.oss-cn-shanghai.aliyuncs.com/blog/%E5%8F%B2%E4%B8%8A%E6%9C%80%E5%85%A8hexo-GitHubPages%E6%90%AD%E5%BB%BA%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2/%E5%88%9B%E5%BB%BA%E6%88%90%E5%8A%9F.png" width=60% height=60%>

进入仓库后，点击Settings，往下翻，查看仓库的GitHub Pages，已经分配了一个网址，这个网址就是你的博客地址，可以访问。
<img src="http://melville-images.oss-cn-shanghai.aliyuncs.com/blog/%E5%8F%B2%E4%B8%8A%E6%9C%80%E5%85%A8hexo-GitHubPages%E6%90%AD%E5%BB%BA%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2/%E6%9F%A5%E7%9C%8B%E4%BB%93%E5%BA%93%E8%AE%BE%E7%BD%AE.png" width=60% height=60%>
<img src="http://melville-images.oss-cn-shanghai.aliyuncs.com/blog/%E5%8F%B2%E4%B8%8A%E6%9C%80%E5%85%A8hexo-GitHubPages%E6%90%AD%E5%BB%BA%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2/%E6%9F%A5%E7%9C%8BGitHubpages%E7%BD%91%E5%9D%80.png" width=60% height=60%>

# 3. 安装工具并设置
## 3.1 安装Git，关联自己的远程仓库
Git是开源的分布式版本控制系统，用于敏捷高效地处理项目。我们网站在本地搭建好了，需要使用Git同步到GitHub上。Git是一个工具，GitHub是用于托管项目的网站，不要搞混淆了。 **[Git下载地址](https://git-scm.com/download/win)**
下载并安装完成后，鼠标右键点击桌面（或文件系统的其它地方），会出现 `Git GUI Here`, `Git Bash Here` 两个选项。
![](http://melville-images.oss-cn-shanghai.aliyuncs.com/blog/%E5%8F%B2%E4%B8%8A%E6%9C%80%E5%85%A8hexo-GitHubPages%E6%90%AD%E5%BB%BA%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2/Git%E5%AE%89%E8%A3%85%E6%88%90%E5%8A%9F.png)

点击 `Git Bash Here`，打开命令窗口。输入以下指令设置Git用户信息。
```text
git config --global user.name "你的GitHub用户名"
git config --global user.email "你的GitHub注册邮箱"
```

生成ssh密钥文件
```text
ssh-keygen -t rsa -C "你的GitHub注册邮箱"
```

输入这个命令时会提示一些信息，直接三次回车即可，然后在 `C:\Users\用户名\.ssh` 目录下会生成两个文件 `id_rsa` 和 `id_rsa.pub`。 `id_rsa`是私钥文件，`id_rsa.pub`是公钥文件。将 `id_rsa.pub`用记事本打开，拷贝文件里面的所有内容。 然后打开GitHub个人主页，点击右上角的头像，选择Settings
![](http://melville-images.oss-cn-shanghai.aliyuncs.com/blog/%E5%8F%B2%E4%B8%8A%E6%9C%80%E5%85%A8hexo-GitHubPages%E6%90%AD%E5%BB%BA%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2/%E8%B4%A6%E6%88%B7%E8%AE%BE%E7%BD%AE.png)
找到 `SSH and GPG keys`，点击 `New SSH key`。
<img src="http://melville-images.oss-cn-shanghai.aliyuncs.com/blog/%E5%8F%B2%E4%B8%8A%E6%9C%80%E5%85%A8hexo-GitHubPages%E6%90%AD%E5%BB%BA%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2/GIT%E6%96%B0%E5%BB%BASSH%20key.png" width=70% height=70%>
输入标题和key的内容，点击 **Add SSH key**
<img src="http://melville-images.oss-cn-shanghai.aliyuncs.com/blog/%E5%8F%B2%E4%B8%8A%E6%9C%80%E5%85%A8hexo-GitHubPages%E6%90%AD%E5%BB%BA%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2/%E8%BE%93%E5%85%A5SSHkey.png" width=70% height=70%>
在Git Bash中检测GitHub公钥设置是否成功，输入
```text
ssh git@github.com
```
结果如下图所示，则说明设置成功
![](http://melville-images.oss-cn-shanghai.aliyuncs.com/blog/%E5%8F%B2%E4%B8%8A%E6%9C%80%E5%85%A8hexo-GitHubPages%E6%90%AD%E5%BB%BA%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2/sshkey%E6%B7%BB%E5%8A%A0%E6%88%90%E5%8A%9F.png)
> **GitHub添加SSH key的目的：**通过非对称加密的公钥与私钥来完成加密，公钥放置在GitHub上，私钥放置在自己的电脑里。GitHub要求每次推送代码都是合法用户，所以每次推送都需要输入账号密码验证推送用户是否是合法用户，为了省去每次输入密码的步骤，采用了SSH，当你推送的时候，Git就会匹配你的私钥跟GitHub上面的公钥是否是配对的，若是匹配就认为你是合法用户，则允许推送。这样可以保证每次的推送都是正确合法的。

## 3.2 clone远程仓库到本地
不克隆仓库也可以正常部署博客，这一步的目的是为了方便提交本地的配置文件到GitHub上，在 **5.2** 章节会提到。
在本地新建一个空文件夹，克隆刚才新建的仓库
```text
git clone 仓库的URL
```
其中，仓库的URL可从仓库主页获取，如下图：
![](http://melville-images.oss-cn-shanghai.aliyuncs.com/blog/%E5%8F%B2%E4%B8%8A%E6%9C%80%E5%85%A8hexo-GitHubPages%E6%90%AD%E5%BB%BA%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2/%E4%BB%93%E5%BA%93URL.png)

## 3.3 安装node.js
[Node.js下载](https://nodejs.org/en/download/)，注意安装Node.js会包含环境变量及npm的安装，安装后，检测Node.js和npm是否安装成功，在命令行中输入
```text
node -v   #显示版本号则说明安装成功
npm -v    #显示版本号则说明安装成功
```

## 3.4 安装hexo
进入我们在 **3.2** 章节中创建的文件夹中，按住shift键，右击鼠标点击命令行（或者是powershell，都是一样的）。输入
```text
npm install -g hexo-cli
```
开始安装hexo，<font color=#FF0000>注意，安装hexo以及下面对hexo初始化的操作，一定要在刚才创建的文件夹中进行。</font>

# 4. 初始化博客以及常用命令
hexo安装完成后，开始对其进行初始化
```text
hexo init
```
初始化的过程稍微有点长，需要等待一会儿。初始化完成之后，文件夹下面应该有这些文件
![](http://melville-images.oss-cn-shanghai.aliyuncs.com/blog/%E5%8F%B2%E4%B8%8A%E6%9C%80%E5%85%A8hexo-GitHubPages%E6%90%AD%E5%BB%BA%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2/hexo%E5%88%9D%E5%A7%8B%E5%8C%96%E5%90%8E%E7%9A%84%E7%9B%AE%E5%BD%95%E7%BB%93%E6%9E%84.png)
测试一下，依次输入
``` text
hexo g
hexo s
```
打开浏览器，输入 `localhost:4000`，回车，出现
![](http://melville-images.oss-cn-shanghai.aliyuncs.com/blog/%E5%8F%B2%E4%B8%8A%E6%9C%80%E5%85%A8hexo-GitHubPages%E6%90%AD%E5%BB%BA%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2/hexo%E5%88%9D%E5%A7%8B%E5%8C%96%E7%95%8C%E9%9D%A2.png)
OK，博客搭建已经完成一半了，接下来我先介绍几个常用的命令

命令|简写|描述|备注
:---:|:---:|:---|:---
hexo init [folder]|-|在指定目录初始化博客|如果不加[folder]参数，则默认在当前目录初始化
hexo clean|-|清除缓存文件 (db.json) 和已生成的静态文件 (public)|
hexo generate|hexo g|生成静态文件|-
hexo deploy|hexo d|部署网站|可以和hexo g合并成 hexo g -d，生成静态网页并部署网站，用的比较多
hexo version|hexo -v|查看hexo版本|-
hexo new [layout] [filename]|-|新建一篇文章|如果没有设置 layout 的话，默认使用 _config.yml 中的 default_layout 参数代替。如果标题包含空格的话，请使用引号括起来。
hexo publish [layout] [filename]|-|发表草稿|-
hexo server|hexo s|启动服务器。默认情况下，访问网址为： http://localhost:4000/|加参数-p，可以指定端口号
hexo list [type]|-|列出网站指定类型的资料|type类型可以是 page, post, route, tag, category等

# 5. GitHub Pages和hexo关联
## 5.1 更改hexo站点配置文件
打开文件夹下面的_config.yml文件
![](http://melville-images.oss-cn-shanghai.aliyuncs.com/blog/%E5%8F%B2%E4%B8%8A%E6%9C%80%E5%85%A8hexo-GitHubPages%E6%90%AD%E5%BB%BA%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2/config%E6%96%87%E4%BB%B6.png)
翻到最后，填写配置
<font color=#FF0000>注意`:`号后面有一个空格</font>。如下图所示
![](http://melville-images.oss-cn-shanghai.aliyuncs.com/blog/%E5%8F%B2%E4%B8%8A%E6%9C%80%E5%85%A8hexo-GitHubPages%E6%90%AD%E5%BB%BA%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2/config%E6%96%87%E4%BB%B6%E9%85%8D%E7%BD%AEdeploy.png)
其中,repo可以在你新建的仓库中获取到
![](http://melville-images.oss-cn-shanghai.aliyuncs.com/blog/%E5%8F%B2%E4%B8%8A%E6%9C%80%E5%85%A8hexo-GitHubPages%E6%90%AD%E5%BB%BA%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2/preo%E5%9C%B0%E5%9D%80.png)

## 5.2 给仓库创建分支
这里的branch填的是master，这个master是你建立的github.io仓库的主干，用于存放生成的静态网页，你从自己博客网站上看到的东西都是在master这个主干上，而我们本地存放的不仅有博客文章，还有网站的配置文件，这就引发了一个问题，如果我们的电脑硬盘坏了，这些配置文件就丢了，我们再想更改网站的配置，就只能从头再来。所以我们要及时对这些文件做好备份，最好的方法当然是备份到GitHub上，我们可以在master的基础上创建一个分支，比如叫hexo，把网站的配置文件都放在hexo分支上，如果我们电脑硬盘坏了或者想换电脑了，直接从hexo分支上pull到本地即可。下面是创建的方法：

直接在此处输入hexo，如果没有这个分支，会提示创建，直接回车即可创建hexo分支。如下图所示
![](http://melville-images.oss-cn-shanghai.aliyuncs.com/blog/%E5%8F%B2%E4%B8%8A%E6%9C%80%E5%85%A8hexo-GitHubPages%E6%90%AD%E5%BB%BA%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2/GitHub%E5%88%9B%E5%BB%BA%E5%88%86%E6%94%AF.png)

点击 **branches**，将GitHub的默认分支改成hexo
![](http://melville-images.oss-cn-shanghai.aliyuncs.com/blog/%E5%8F%B2%E4%B8%8A%E6%9C%80%E5%85%A8hexo-GitHubPages%E6%90%AD%E5%BB%BA%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2/%E7%82%B9%E5%87%BB%E5%88%86%E6%94%AF.png)
![](http://melville-images.oss-cn-shanghai.aliyuncs.com/blog/%E5%8F%B2%E4%B8%8A%E6%9C%80%E5%85%A8hexo-GitHubPages%E6%90%AD%E5%BB%BA%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2/%E6%94%B9%E5%8F%98%E5%88%86%E6%94%AF.png)
创建好以后，就可以将我们本地的文件提交到hexo上了。关于git的命令，大家自己百度学习。
```git
git add .               # 添加所有文件
git commit -m "描述"    # 提交
git push origin hexo    # push到hexo分支上
```

# 6. 新建一篇博客，并部署
接下来就可以写博客啦，我们先新建一个文章，部署上去看看效果。
```text
hexo new myTestFile
hexo g -d
```
部署完成后，打开你的博客网址：https://用户名.github.io，在主页上就能看到你刚才的文章啦。

# 7. 搭建主题

# 8. 主题配置

# 9. 自定义域名





---
