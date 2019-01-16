---
title: 使用workbench编译报错“变量重复定义”
id: a-2019-01-11-17-34-53
tags:
  - bug
  - workbench
  - 调试
categories:
  - bug
keywords: 
  - bug
  - workbench
  - 调试
description: 使用workbench编译报错“变量重复定义”
comments: true
date: 2019-01-11 17:34:53
---

今天在其他同事编写的demo的基础上做修改，使用workbench编译时报了 **main.c** 中变量重复定义的错误，查遍整个工程，只有一个 **config.c** 文件定义了该变量，并没有重复定义的情况。
   
刚开始怀疑过编译器是不是坏了，我把工程全部删掉重新做一遍，还是一样的错误。排查了很久，最后发现在 **main.c** 文件的中间，包含了定义该变量的 **config.c** 文件（`#include "config.c"`），所以变量被重复定义了。

真让人苦恼，搞不懂为什么我的同事要这样做。

<!-- more -->

---
