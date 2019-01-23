---
title: Linux查看端口号
id: a-20190123-102022
tags: 
  - 端口号
categories: 
  - Linux
comments: true
copyright: true
date: 2019-01-23 10:20:22
keywords: 
  - Linux
  - 端口号
  - port
description: Linux查看端口号
---

<script type="text/javascript" src="/js/src/baidu.js"></script>

查看某一端口的占用情况
``` shell
lsof -i:端口号
```

显示系统端口使用情况

``` shell
netstat -anp
```

---