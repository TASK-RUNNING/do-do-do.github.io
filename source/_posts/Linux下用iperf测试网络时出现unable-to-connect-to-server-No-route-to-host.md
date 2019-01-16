---
title: 'Linux下用iperf测试网络时出现unable to connect to server: No route to host'
id: a-2018-10-23-09-27-01
date: 2018-10-23 09:27:01
tags: [linux,网络,iperf]
categories: [工具]
keywords: [linux,网络,iperf]
description: Linux下用iperf测试网络时出现unable to connect to server "No route to host"
---

# Problem

为了验证虚拟网卡驱动的性能，需要两台主机之间互相收发数据，我使用iperf工具进行测试，报错“unable to connect to server: No route to host”，但是可以ping通另一台主机。

# Solvent

Google了一下，发现是防火墙的问题，需要将防火墙关掉，大部分搜索到的结果都是：

```bash
service iptables stop
iptables -F
```

<!-- more -->

执行此操作后并没有解决问题（我用的是中标麒麟V5.0系统），我就把系统防火墙firewalld全部关闭，问题解决。指令如下：
```bash
systemctl stop firewalld
systemctl disable firewalld
systemctl stop firewalld.service
systemctl disable firewalld.service
```

想知道iptables和firewalled有什么区别？请参照[《iptables和firewalled的区别》](https://melville.club/posts/a-2018-10-23-10-15-57.html)

---
