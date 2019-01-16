---
title: Windows10企业版激活方法
id: a-2019-01-07-17-52-13
date: 2019-01-07 17:52:13
tags: [Windows10,激活]
categories: [Windows]
keywords: [Windows10,激活]
description: Windows10企业版激活方法
---

# 1. 效果图
![](http://melville-images.oss-cn-shanghai.aliyuncs.com/blog/Windows10%E4%BC%81%E4%B8%9A%E7%89%88%E6%BF%80%E6%B4%BB%E6%96%B9%E6%B3%95/Windows10%E6%BF%80%E6%B4%BB.png)

# 2. 激活方法
以管理员身份运行`命令提示符`，依次输入以下命令：

<!-- more -->

**a. 卸载产品秘钥** 
```text
slmgr.vbs /upk
```

**b. 安装产品秘钥**
```text
slmgr /ipk NPPR9-FWDCX-D2C8J-H872K-2YT43
```

**c. 设置计算机名称**
```text
slmgr /skms zh.us.to
```

**d. 激活**
```text
slmgr /ato
```

# 3. 查看
上述步骤完成后，如果提示成功激活，应该可以从`控制面板->系统和安全->系统`中看到激活状态，如果还是未激活，关机重启后再查看。

![](http://melville-images.oss-cn-shanghai.aliyuncs.com/blog/Windows10%E4%BC%81%E4%B8%9A%E7%89%88%E6%BF%80%E6%B4%BB%E6%96%B9%E6%B3%95/Windows%E6%BF%80%E6%B4%BB%E7%8A%B6%E6%80%81.png)

---