---
title: iptables和firewalled的区别
date: 2018-10-23 10:15:57
tags: [linux,网络,防火墙]
categories: [Linux系统]
---

# 简述

保证数据的安全性是继可用性之后最为重要的一项工作，防火墙技术作为公网与内网之间的保护屏障，起着至关重要的作用。面对同学们普遍不了解在红帽RHEL7系统中新旧两款防火墙的差异，刘遄老师决定先带领读者正确的认识在红帽RHEL7系统中firewalld防火墙服务与iptables防火墙服务之间的关系，从理论和事实层面剖析真相。
<!-- more -->
本章节内将会分别使用iptables、firewall-cmd、firewall-config和Tcp_wrappers等防火墙策略配置服务来完成数十个根据真实工作需求而设计的防火墙策略配置实验，让同学们不仅能够熟练的对请求数据包流量进行过滤，还能够基于服务程序进行允许和关闭操作，做到保证Linux系统安全万无一失。

# 1. 防火墙管理工具
保证数据的安全性是继可用性之后最为重要的一项工作，众所周知外部公网相比企业内网更加的“罪恶丛生”，因此防火墙技术作为公网与内网之间的保护屏障，虽然有软件或硬件之分，但主要功能都是依据策略对外部请求进行过滤。防火墙技术能够做到监控每一个数据包并判断是否有相应的匹配策略规则，直到匹配到其中一条策略规则或执行默认策略为止，防火墙策略可以基于来源地址、请求动作或协议等信息来定制，最终仅让合法的用户请求流入到内网中，其余的均被丢弃。

<img src="https://melville-images.oss-cn-shanghai.aliyuncs.com/blog/iperf%E5%92%8Cfirewalled%E7%9A%84%E5%8C%BA%E5%88%AB/%E9%98%B2%E7%81%AB%E5%A2%99%E6%8B%93%E6%89%91.png" width=50% height=50%>

在红帽RHEL7系统中Firewalld服务取代了Iptables服务，对于接触Linux系统比较早或学习过红帽RHEL6系统的读者来讲，突然改用Firewalld服务后确实不免会有些抵触心理，或许会觉得Firewalld服务是一次不小的改变。但其实Iptables服务与Firewalld服务都不是真正的防火墙，它们都只是用来定义防火墙策略功能的“防火墙管理工具”而已，iptables服务会把配置好的防火墙策略交由内核层面的netfilter网络过滤器来处理，而firewalld服务则是把配置好的防火墙策略交由内核层面的nftables包过滤框架来处理。换句话说，当前在Linux系统中其实同时有多个防火墙管理工具共同存在，它们的作用都是为了方便运维人员管理Linux系统的防火墙策略，而咱们只要配置妥当其中一个就足够了。虽然各个工具之间各有优劣特色，但对于防火墙策略的配置思路上是保持一致的，同学们甚至可以不用完全掌握本章节内的知识，而是在这诸多个防火墙管理工具中任选一款来学透即可，完全能够满足日常的工作所需。

# 2. iptables

在较早期的Linux系统中想配置防火墙默认使用的都是iptables防火墙管理命令，而新型Firewalld防火墙管理服务已经被投入使用多年，但还记得刘遄老师在第0章0.6小节里谈到过企业不愿意及时升级的原因吧，于是不论出于什么样的原因，目前市场上还有大量的生产环境中在使用着iptables命令来管理着防火墙的规则策略。虽然明知iptables可能有着即将被“淘汰”的命运，但为了让同学们不必在面试时尴尬以及看完手中这本《Linux就该这么学》书籍后能“通吃”各个版本的Linux系统，刘遄老师觉得还是有必要把这一项技术好好卖力气讲一下，更何况各个工具的配置防火墙策略思路上大体一致，具有很高的相同性及借鉴意义。

## 2.1 策略与规则链

防火墙会从上至下来读取规则策略，一旦匹配到了合适的就会去执行并立即结束匹配工作，但也有转了一圈之后发现没有匹配到合适规则的时候，那么就会去执行默认的策略。因此对防火墙策略的设置无非有两种，一种是“通”，一种是“堵”——当防火墙的默认策略是拒绝的，就要设置允许规则，否则谁都进不来了，而如果防火墙的默认策略是允许的，就要设置拒绝规则，否则谁都能进来了，起不到防范的作用。

iptables命令把对数据进行过滤或处理数据包的策略叫做规则，把多条规则又存放到一个规则链中，规则链是依据处理数据包位置的不同而进行的分类，包括有：在进行路由选择前处理数据包（PREROUTING）、处理流入的数据包（INPUT）、处理流出的数据包（OUTPUT）、处理转发的数据包（FORWARD）、在进行路由选择后处理数据包（POSTROUTING）。从内网向外网发送的数据一般都是可控且良性的，因此显而易见咱们使用最多的就是INPUT数据链，这个链中定义的规则起到了保证私网设施不受外网骇客侵犯的作用。

比如您所居住的社区物业保安有两条规定——“禁止小商贩进入社区，各种车辆都需要登记”，这两条安保规定很明显应该是作用到了社区的正门（流量必须经过的地方），而不是每家每户的防盗门上。根据前面提到的防火墙策略的匹配顺序规则，咱们可以猜想有多种情况——比如来访人员是小商贩，则会被物业保安直接拒绝在大门外，也无需再对车辆进行登记，而如果来访人员是一辆汽车，那么因为第一条禁止小商贩策略就没有被匹配到，因而按顺序匹配到第二条策略，需要对车辆进行登记，再有如果来访的是社区居民，则既不满足小商贩策略，也不满足车辆登记策略，因此会执行默认的放行策略。

不过只有规则策略还不能保证社区的安全，物业保安还应该知道该怎么样处理这些被匹配到的流量，比如包括有“允许”、“登记”、“拒绝”、“不理他”，这些动作对应到iptables命令术语中是ACCEPT（允许流量通过）、LOG（记录日志信息）、REJECT（拒绝流量通过）、DROP（拒绝流量通过）。允许动作和记录日志工作都比较好理解，着重需要讲解的是这两条拒绝动作的不同点，其中REJECT和DROP的动作操作都是把数据包拒绝，DROP是直接把数据包抛弃不响应，而REJECT会拒绝后再回复一条“您的信息我已收到，但被扔掉了”，让对方清晰的看到数据被拒绝的响应。就好比说您有一天正在家里看电视，突然有人敲门，透过“猫眼”一看是推销商品的，咱们如果不需要的情况下就会直接拒绝他们（REJECT）。但如果透过“猫眼”看到的是债主带了几十个小弟来讨债，这种情况不光要拒绝开门，还要默不作声，伪装成自己不在家的样子（DROP），这就是两种拒绝动作的不同之处。

把Linux系统设置成REJECT拒绝动作策略后，对方会看到本机的端口不可达的响应：

```bash
[root@linuxprobe ~]# ping -c 4 192.168.10.10
PING 192.168.10.10 (192.168.10.10) 56(84) bytes of data.
From 192.168.10.10 icmp_seq=1 Destination Port Unreachable
From 192.168.10.10 icmp_seq=2 Destination Port Unreachable
From 192.168.10.10 icmp_seq=3 Destination Port Unreachable
From 192.168.10.10 icmp_seq=4 Destination Port Unreachable
--- 192.168.10.10 ping statistics ---
4 packets transmitted, 0 received, +4 errors, 100% packet loss, time 3002ms
```

把Linux系统设置成DROP拒绝动作策略后，对方会看到本机响应超时的提醒，无法判断流量是被拒绝，还是对方主机当前不在线：

```bash
[root@linuxprobe ~]# ping -c 4 192.168.10.10
PING 192.168.10.10 (192.168.10.10) 56(84) bytes of data.

--- 192.168.10.10 ping statistics ---
4 packets transmitted, 0 received, 100% packet loss, time 3000ms
```

## 2.2 基本的命令参数

iptables是一款基于命令行的防火墙策略管理工具，由于该命令是基于终端执行且存在有大量参数的，学习起来难度还是较大的，好在对于日常控制防火墙策略来讲，您无需深入的了解诸如“四表五链”的理论概念，只需要掌握常用的参数并做到灵活搭配即可，以便于能够更顺畅的胜任工作所需。iptables命令可以根据数据流量的源地址、目的地址、传输协议、服务类型等等信息项进行匹配，一旦数据包与策略匹配上后，iptables就会根据策略所预设的动作来处理这些数据包流量，另外再来提醒下同学们防火墙策略的匹配顺序规则是从上至下的，因此切记要把较为严格、优先级较高的策略放到靠前位置，否则有可能产生错误。下表中为读者们总结归纳了几乎所有常用的iptables命令参数，刘遄老师遵循《Linux就该这么学》书籍的编写初衷而设计了大量动手实验，让您无需生背硬记这些参数，可以结合下面的实例来逐个参阅即可。

|参数         |作用                                         |
|:------------|:-------------------------------------------|
|-P           |设置默认策略:iptables -P INPUT (DROP&#124;ACCEPT) | 
|-F           |清空规则链                                   |
|-L           |查看规则链                                   |
|-A           |在规则链的末尾加入新规则                      |
|-I num       |在规则链的头部加入新规则                      |
|-D num       |删除某一条规则                               |
|-s           |匹配来源地址IP/MASK，加叹号"!"表示除这个IP外。  |
|-d           |匹配目标地址                                 |
|-i 网卡名称   | 匹配从这块网卡流入的数据                     |
|-o 网卡名称   |匹配从这块网卡流出的数据                      |
|-p           |匹配协议,如tcp,udp,icmp                      |
|--dport num  |匹配目标端口号                               |
|--sport num  |匹配来源端口号                               |
|-j           |指定动作类型                                 |

使用iptables命令-L参数查看已有的防火墙策略：

```bash
[root@linuxprobe ~]# iptables -L
Chain INPUT (policy ACCEPT)
target prot opt source destination 
ACCEPT all -- anywhere anywhere ctstate RELATED,ESTABLISHED
ACCEPT all -- anywhere anywhere 
INPUT_direct all -- anywhere anywhere 
INPUT_ZONES_SOURCE all -- anywhere anywhere 
INPUT_ZONES all -- anywhere anywhere 
ACCEPT icmp -- anywhere anywhere 
REJECT all -- anywhere anywhere reject-with icmp-host-prohibited
………………省略部分输出信息………………
```

使用iptables命令-F参数清空已有的防火墙策略：

```bash
[root@linuxprobe ~]# iptables -F
[root@linuxprobe ~]# iptables -L
Chain INPUT (policy ACCEPT)
target prot opt source destination 
………………省略部分输出信息………………
```

把INPUT链的默认策略设置为拒绝：

如前面所提到的防火墙策略设置无非有两种方式，一种是“通”，一种是“堵”，当把INPUT链设置为默认拒绝后，就要往里面写入允许策略了，否则所有流入的数据包都会被默认拒绝掉，同学们需要留意规则链的默认策略拒绝动作只能是DROP，而不能是REJECT。

```bash
[root@linuxprobe ~]# iptables -P INPUT DROP
[root@linuxprobe ~]# iptables -L
Chain INPUT (policy DROP)
target prot opt source destination 
…………省略部分输出信息………………
```

向INPUT链中添加允许icmp数据包流入的允许策略：

在日常运维工作中经常会使用到ping命令来检查对方主机是否在线，而向防火墙INPUT链中添加一条允许icmp协议数据包流入的策略就是默认允许了这种ping命令检测行为。

```bash
[root@linuxprobe ~]# ping -c 4 192.168.10.10
PING 192.168.10.10 (192.168.10.10) 56(84) bytes of data.

--- 192.168.10.10 ping statistics ---
4 packets transmitted, 0 received, 100% packet loss, time 3000ms
[root@linuxprobe ~]# iptables -I INPUT -p icmp -j ACCEPT
[root@linuxprobe ~]# ping -c 4 192.168.10.10
PING 192.168.10.10 (192.168.10.10) 56(84) bytes of data.
64 bytes from 192.168.10.10: icmp_seq=1 ttl=64 time=0.156 ms
64 bytes from 192.168.10.10: icmp_seq=2 ttl=64 time=0.117 ms
64 bytes from 192.168.10.10: icmp_seq=3 ttl=64 time=0.099 ms
64 bytes from 192.168.10.10: icmp_seq=4 ttl=64 time=0.090 ms
--- 192.168.10.10 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 2999ms
rtt min/avg/max/mdev = 0.090/0.115/0.156/0.027 ms
```

删除INPUT链中的那条策略，并把默认策略还原为允许：

```bash
[root@linuxprobe ~]# iptables -D INPUT 1
[root@linuxprobe ~]# iptables -P INPUT ACCEPT
[root@linuxprobe ~]# iptables -L
Chain INPUT (policy ACCEPT)
target prot opt source destination
………………省略部分输出信息………………
```

设置INPUT链只允许指定网段访问本机的22端口，拒绝其他所有主机的数据请求流量：

防火墙策略是按照从上至下顺序匹配的，因此请一定要记得把允许动作放到拒绝动作上面，否则所有的流量就先被拒绝掉了，任何人都获取不到咱们的业务。文中提到的22端口是下面第9章节讲的ssh服务做占用的资源，刘遄老师在这里挖个小坑~等读者们稍后学完再回来验证这个实验效果吧~

> #屏蔽单个IP的命令是 
iptables -I INPUT -s 123.45.6.7 -j DROP  

> #封整个段即从123.0.0.1到123.255.255.254的命令  
iptables -I INPUT -s 123.0.0.0/8 -j DROP 

> #封IP段即从123.45.0.1到123.45.255.254的命令  
iptables -I INPUT -s 124.45.0.0/16 -j DROP  

> #封IP段即从123.45.6.1到123.45.6.254的命令是 
iptables -I INPUT -s 123.45.6.0/24 -j DROP

> #指定几个ip123.45.6.0，123.45.6.1,能用ssh连接，其他均不行。
iptables -I INPUT -s 123.45.6.0/123.45.6.2 -p tcp -j ACCEPT

```bash
 iptables -A INPUT -p tcp --dport 22 -j REJECT
```

```bash
[root@linuxprobe ~]# iptables -I INPUT -s 192.168.10.0/24 -p tcp --dport 22 -j ACCEPT
[root@linuxprobe ~]# iptables -A INPUT -p tcp --dport 22 -j REJECT
[root@linuxprobe ~]# iptables -L
Chain INPUT (policy ACCEPT)
target prot opt source destination 
ACCEPT tcp -- 192.168.10.0/24 anywhere tcp dpt:ssh
REJECT tcp -- anywhere anywhere tcp dpt:ssh reject-with icmp-port-unreachable
………………省略部分输出信息………………
```

使用IP地址在192.168.10.0/24网段内的主机访问服务器的22端口：

```bash
[root@Client A ~]# ssh 192.168.10.10
The authenticity of host '192.168.10.10 (192.168.10.10)' can't be established.
ECDSA key fingerprint is 70:3b:5d:37:96:7b:2e:a5:28:0d:7e:dc:47:6a:fe:5c.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '192.168.10.10' (ECDSA) to the list of known hosts.
root@192.168.10.10's password: 
Last login: Sun Feb 12 01:50:25 2017
[root@Client A ~]#
```

使用IP地址在192.168.20.0/24网段外的主机访问服务器的22端口：

```bash
[root@Client B ~]# ssh 192.168.10.10
Connecting to 192.168.10.10:22...
Could not connect to '192.168.10.10' (port 22): Connection failed.
```

向INPUT链中添加拒绝所有人访问本机12345端口的防火墙策略：

```bash
[root@linuxprobe ~]# iptables -I INPUT -p tcp --dport 12345 -j REJECT
[root@linuxprobe ~]# iptables -I INPUT -p udp --dport 12345 -j REJECT
[root@linuxprobe ~]# iptables -L
Chain INPUT (policy ACCEPT)
target prot opt source destination 
REJECT udp -- anywhere anywhere udp dpt:italk reject-with icmp-port-unreachable
REJECT tcp -- anywhere anywhere tcp dpt:italk reject-with icmp-port-unreachable
ACCEPT tcp -- 192.168.10.0/24 anywhere tcp dpt:ssh
REJECT tcp -- anywhere anywhere tcp dpt:ssh reject-with icmp-port-unreachable
………………省略部分输出信息………………
```

向INPUT链中添加拒绝来自于指定192.168.10.5主机访问本机80端口（web服务）的防火墙策略：

```bash
[root@linuxprobe ~]# iptables -I INPUT -p tcp -s 192.168.10.5 --dport 80 -j REJECT
[root@linuxprobe ~]# iptables -L
Chain INPUT (policy ACCEPT)
target prot opt source destination 
REJECT tcp -- 192.168.10.5 anywhere tcp dpt:http reject-with icmp-port-unreachable
REJECT udp -- anywhere anywhere udp dpt:italk reject-with icmp-port-unreachable
REJECT tcp -- anywhere anywhere tcp dpt:italk reject-with icmp-port-unreachable
ACCEPT tcp -- 192.168.10.0/24 anywhere tcp dpt:ssh
REJECT tcp -- anywhere anywhere tcp dpt:ssh reject-with icmp-port-unreachable
………………省略部分输出信息………………
```

向INPUT链中添加拒绝所有主机不能访问本机1000至1024端口的防火墙策略：

```bash
[root@linuxprobe ~]# iptables -A INPUT -p tcp --dport 1000:1024 -j REJECT
[root@linuxprobe ~]# iptables -A INPUT -p udp --dport 1000:1024 -j REJECT
[root@linuxprobe ~]# iptables -L
Chain INPUT (policy ACCEPT)
target prot opt source destination 
REJECT tcp -- 192.168.10.5 anywhere tcp dpt:http reject-with icmp-port-unreachable
REJECT udp -- anywhere anywhere udp dpt:italk reject-with icmp-port-unreachable
REJECT tcp -- anywhere anywhere tcp dpt:italk reject-with icmp-port-unreachable
ACCEPT tcp -- 192.168.10.0/24 anywhere tcp dpt:ssh
REJECT tcp -- anywhere anywhere tcp dpt:ssh reject-with icmp-port-unreachable
REJECT tcp -- anywhere anywhere tcp dpts:cadlock2:1024 reject-with icmp-port-unreachable
REJECT udp -- anywhere anywhere udp dpts:cadlock2:1024 reject-with icmp-port-unreachable
………………省略部分输出信息………………
```

是不是还意犹未尽？但对于iptables防火墙管理命令的学习到此就可以结束了，考虑到以后防火墙的发展趋势，同学们只要能把上面的实例看懂看熟就可以完全搞定日常的iptables防火墙配置工作了。但请特别留意下，iptables命令配置的防火墙规则默认会在下一次重启时失效，所以如果您想让配置的防火墙策略永久的生效下去，还要执行一下保存命令：

```bash
[root@linuxprobe ~]# service iptables save
iptables: Saving firewall rules to /etc/sysconfig/iptables: [ OK ]
```

# 3. firewalled

RHEL7是一个集合多款防火墙管理工具并存的系统，Firewalld动态防火墙管理器服务（Dynamic Firewall Manager of Linux systems）是目前默认的防火墙管理工具，同时拥有命令行终端和图形化界面的配置工具，即使是对Linux命令并不熟悉的同学也能快速入门。相比于传统的防火墙管理工具还支持了动态更新技术并加入了“zone区域”的概念，简单来说就是为用户预先准备了几套防火墙策略集合（策略模板），然后可以根据生产场景的不同而选择合适的策略集合，实现了防火墙策略之间的快速切换。例如咱们有一台笔记本电脑每天都要在办公室、咖啡厅和家里使用，按常理推断最安全的应该是家里的内网，其次是公司办公室，最后是咖啡厅，如果需要在办公室内允许文件共享服务的请求流量、回到家中需要允许所有的服务，而在咖啡店则是除了上网外不允许任何其他请求，这样的需求应该是很常见的，在以前只能频繁的进行手动设置，而现在只需要预设好zone区域集合，然后轻轻点击一下就可以切换过去了上百条策略了，极大的提高了防火墙策略的应用效率，常见的zone区域名称及应用可见下表（默认为public）：


|区域      | 默认规则策略   |
|:---------|:-------------|
|trusted   |允许所有的数据包。|
|home      |拒绝流入的数据包，除非与输出流量数据包相关或是ssh,mdns,ipp-client,samba-client与dhcpv6-client服务则允许。|
|internal  |等同于home区域|
|work      |拒绝流入的数据包，除非与输出流量数据包相关或是ssh,ipp-client与dhcpv6-client服务则允许。|
|public    |拒绝流入的数据包，除非与输出流量数据包相关或是ssh,dhcpv6-client服务则允许。|
|external  |拒绝流入的数据包，除非与输出流量数据包相关或是ssh服务则允许。|
|dmz       |拒绝流入的数据包，除非与输出流量数据包相关或是ssh服务则允许。|
|block     |拒绝流入的数据包，除非与输出流量数据包相关。|
|drop      |拒绝流入的数据包，除非与输出流量数据包相关。|

## 3.1 终端管理工具

前面第2章学习Linux命令时刘遄老师提到过的，命令行终端是一种极富效率的工作方式，firewall-cmd命令是Firewalld动态防火墙管理器服务的命令行终端。它的参数一般都是以“长格式”来执行的，但同学们也不用太过于担心，因为红帽RHEL7系统非常酷的支持了部分命令的参数补齐，也正好包括了这条命令，也就是说现在除了能够用Tab键来补齐命令或文件名等等内容，还可以用Tab键来补齐下列长格式参数啦（这点特别的棒）。
               
|参数|  作用|
|:--|:--|
|--get-default-zone                     |查询默认的区域名称。|
|--set-default-zone=<区域名称>           |设置默认的区域，永久生效。|
|--get-zones                            |显示可用的区域。|
|--get-services                         |显示预先定义的服务。|
|--get-active-zones                     |显示当前正在使用的区域与网卡名称。|
|--add-source=                          |将来源于此IP或子网的流量导向指定的区域。|
|--remove-source=                       |不再将此IP或子网的流量导向某个指定区域。|
|--add-interface=<网卡名称>              |将来自于该网卡的所有流量都导向某个指定区域。|
|--change-interface=<网卡名称>           |将某个网卡与区域做关联。|
|--list-all                             |显示当前区域的网卡配置参数，资源，端口以及服务等信息。|
|--list-all-zones                       |显示所有区域的网卡配置参数，资源，端口以及服务等信息。|
|--add-service=<服务名>                  |设置默认区域允许该服务的流量。|
|--add-port=<端口号/协议>                |允许默认区域允许该端口的流量。|
|--remove-service=<服务名>               |设置默认区域不再允许该服务的流量。|
|--remove-port=<端口号/协议>             |允许默认区域不再允许该端口的流量。|
|--reload                               |让“永久生效”的配置规则立即生效，覆盖当前的。|
|--panic-on                             |开启应急状况模式。|
|--panic-off                            |关闭应急状况模式。|

与Linux系统中其他的防火墙策略配置工具一样，使用firewalld配置的防火墙策略默认为运行时（Runtime）模式，又称为当前生效模式，而且随着系统的重启会失效。如果想让配置策略一直存在，就需要使用永久（Permanent）模式了，方法就是在用firewall-cmd命令正常设置防火墙策略时添加--permanent参数，这样配置的防火墙策略就可以永久生效了。但是，永久生效模式有一个“不近人情”的特点，就是使用它设置的策略只有在系统重启之后才能自动生效。如果想让配置的策略立即生效，需要手动执行firewall-cmd --reload命令。

接下来的实验都很简单，但是提醒大家一定要仔细查看刘遄老师使用的是Runtime模式还是Permanent模式。如果不关注这个细节，就算是正确配置了防火墙策略，也可能无法达到预期的效果。

查看firewalld服务当前所使用的区域：

```bash
[root@linuxprobe ~]# firewall-cmd --get-default-zone
public
```

查询eno16777728网卡在firewalld服务中的区域：

```bash
[root@linuxprobe ~]# firewall-cmd --get-zone-of-interface=eno16777728
public
```

把firewalld服务中eno16777728网卡的默认区域修改为external，并在系统重启后生效。分别查看当前与永久模式下的区域名称：

```bash
[root@linuxprobe ~]# firewall-cmd --permanent --zone=external --change-interface=eno16777728
success
[root@linuxprobe ~]# firewall-cmd --get-zone-of-interface=eno16777728
public
[root@linuxprobe ~]# firewall-cmd --permanent --get-zone-of-interface=eno16777728
external
```

把firewalld服务的当前默认区域设置为public：

```bash
[root@linuxprobe ~]# firewall-cmd --set-default-zone=public
success
[root@linuxprobe ~]# firewall-cmd --get-default-zone 
public
```

启动/关闭firewalld防火墙服务的应急状况模式，阻断一切网络连接（当远程控制服务器时请慎用）：

```bash
[root@linuxprobe ~]# firewall-cmd --panic-on
success
[root@linuxprobe ~]# firewall-cmd --panic-off
success
```

查询public区域是否允许请求SSH和HTTPS协议的流量：

```bash
[root@linuxprobe ~]# firewall-cmd --zone=public --query-service=ssh
yes
[root@linuxprobe ~]# firewall-cmd --zone=public --query-service=https
no
```

把firewalld服务中请求HTTPS协议的流量设置为永久允许，并立即生效：

```bash
[root@linuxprobe ~]# firewall-cmd --zone=public --add-service=https
success
[root@linuxprobe ~]# firewall-cmd --permanent --zone=public --add-service=https
success
[root@linuxprobe ~]# firewall-cmd --reload
success
```

把firewalld服务中请求HTTP协议的流量设置为永久拒绝，并立即生效：

```bash
[root@linuxprobe ~]# firewall-cmd --permanent --zone=public --remove-service=http 
success
[root@linuxprobe ~]# firewall-cmd --reload 
success
```

把在firewalld服务中访问8080和8081端口的流量策略设置为允许，但仅限当前生效：

```bash
[root@linuxprobe ~]# firewall-cmd --zone=public --add-port=8080-8081/tcp
success
[root@linuxprobe ~]# firewall-cmd --zone=public --list-ports 
8080-8081/tcp
```

把原本访问本机888端口的流量转发到22端口，要且求当前和长期均有效：
> 流量转发命令格式为firewall-cmd --permanent --zone=<区域> --add-forward-port=port=<源端口号>:proto=<协议>:toport=<目标端口号>:toaddr=<目标IP地址>

```bash
[root@linuxprobe ~]# firewall-cmd --permanent --zone=public --add-forward-port=port=888:proto=tcp:toport=22:toaddr=192.168.10.10
success
[root@linuxprobe ~]# firewall-cmd --reload
success
```

在客户端使用ssh命令尝试访问192.168.10.10主机的888端口：

```bash
[root@client A ~]# ssh -p 888 192.168.10.10
The authenticity of host '[192.168.10.10]:888 ([192.168.10.10]:888)' can't be established.
ECDSA key fingerprint is b8:25:88:89:5c:05:b6:dd:ef:76:63:ff:1a:54:02:1a.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '[192.168.10.10]:888' (ECDSA) to the list of known hosts.
root@192.168.10.10's password:此处输入远程root管理员的密码
Last login: Sun Jul 19 21:43:48 2017 from 192.168.10.10
```

firewalld中的富规则表示更细致、更详细的防火墙策略配置，它可以针对系统服务、端口号、源地址和目标地址等诸多信息进行更有针对性的策略配置。它的优先级在所有的防火墙策略中也是最高的。比如，我们可以在firewalld服务中配置一条富规则，使其拒绝192.168.10.0/24网段的所有用户访问本机的ssh服务（22端口）：

```bash
[root@linuxprobe ~]# firewall-cmd --permanent --zone=public --add-rich-rule="rule family="ipv4" source address="192.168.10.0/24" service name="ssh" reject"
success
[root@linuxprobe ~]# firewall-cmd --reload
success
```

在客户端使用ssh命令尝试访问192.168.10.10主机的ssh服务（22端口）：

```bash
[root@client A ~]# ssh 192.168.10.10
Connecting to 192.168.10.10:22...
Could not connect to '192.168.10.10' (port 22): Connection failed.
```

## 3.2 图形管理工具

在各种版本的Linux系统中，几乎没有能让刘遄老师欣慰并推荐的图形化工具，但是firewall-config做到了。它是firewalld防火墙配置管理工具的GUI（图形用户界面）版本，几乎可以实现所有以命令行来执行的操作。毫不夸张的说，即使读者没有扎实的Linux命令基础，也完全可以通过它来妥善配置RHEL 7中的防火墙策略。firewall-config的界面如图8-2所示，其功能具体如下。

> 1：选择运行时（Runtime）模式或永久（Permanent）模式的配置。
> 2：可选的策略集合区域列表。
> 3：常用的系统服务列表。
> 4：当前正在使用的区域。
> 5：管理当前被选中区域中的服务。
> 6：管理当前被选中区域中的端口。
> 7：开启或关闭SNAT（源地址转换协议）技术。
> 8：设置端口转发策略。
> 9：控制请求icmp服务的流量。
> 10：管理防火墙的富规则。
> 11：管理网卡设备。
> 12：被选中区域的服务，若勾选了相应服务前面的复选框，则表示允许与之相关的流量。
> 13：firewall-config工具的运行状态。

![firewall-config的界面](https://melville-images.oss-cn-shanghai.aliyuncs.com/blog/iperf%E5%92%8Cfirewalled%E7%9A%84%E5%8C%BA%E5%88%AB/firewall-config%E7%95%8C%E9%9D%A2.png)

刘遄老师再啰嗦几句。在使用firewall-config工具配置完防火墙策略之后，无须进行二次确认，因为只要有修改内容，它就自动进行保存。下面进行动手实践环节。

我们先将当前区域中请求http服务的流量设置为允许，但仅限当前生效。具体配置如图8-3所示。

![放行请求http服务的流量](https://melville-images.oss-cn-shanghai.aliyuncs.com/blog/iperf%E5%92%8Cfirewalled%E7%9A%84%E5%8C%BA%E5%88%AB/%E5%85%81%E8%AE%B8%E5%85%B6%E4%BB%96%E4%B8%BB%E6%9C%BA%E8%AE%BF%E9%97%AEhttp%E6%9C%8D%E5%8A%A1.png)

尝试添加一条防火墙策略规则，使其放行访问8080～8088端口（TCP协议）的流量，并将其设置为永久生效，以达到系统重启后防火墙策略依然生效的目的。在按照图8-4所示的界面配置完毕之后，还需要在Options菜单中单击Reload Firewalld命令，让配置的防火墙策略立即生效（见图8-5）。这与在命令行中执行--reload参数的效果一样。

![放行访问8080～8088端口的流量](https://melville-images.oss-cn-shanghai.aliyuncs.com/blog/iperf%E5%92%8Cfirewalled%E7%9A%84%E5%8C%BA%E5%88%AB/%E5%85%81%E8%AE%B8%E5%85%B6%E4%BB%96%E4%B8%BB%E6%9C%BA%E8%AE%BF%E9%97%AE8080-8088%E7%AB%AF%E5%8F%A3.png)

![让配置的防火墙策略规则立即生效](https://melville-images.oss-cn-shanghai.aliyuncs.com/blog/iperf%E5%92%8Cfirewalled%E7%9A%84%E5%8C%BA%E5%88%AB/%E9%87%8D%E5%90%AF%E5%90%8E%E4%BE%9D%E7%84%B6%E6%9C%89%E6%95%88.png)

前面在讲解firewall-config工具的功能时，曾经提到了SNAT（Source Network Address Translation，源网络地址转换）技术。SNAT是一种为了解决IP地址匮乏而设计的技术，它可以使得多个内网中的用户通过同一个外网IP接入Internet。该技术的应用非常广泛，甚至可以说我们每天都在使用，只不过没有察觉到罢了。比如，当我们通过家中的网关设备（比如无线路由器）访问本书配套站点www.linuxprobe.com时，就用到了SNAT技术。

大家可以看一下在网络中不使用SNAT技术（见图8-6）和使用SNAT技术（见图8-7）时的情况。在图8-6所示的局域网中有多台PC，如果网关服务器没有应用SNAT技术，则互联网中的网站服务器在收到PC的请求数据包，并回送响应数据包时，将无法在网络中找到这个私有网络的IP地址，所以PC也就收不到响应数据包了。在图8-7所示的局域网中，由于网关服务器应用了SNAT技术，所以互联网中的网站服务器会将响应数据包发给网关服务器，再由后者转发给局域网中的PC。

![没有使用SNAT技术的网络](https://melville-images.oss-cn-shanghai.aliyuncs.com/blog/iperf%E5%92%8Cfirewalled%E7%9A%84%E5%8C%BA%E5%88%AB/%E6%9C%AA%E7%94%A8SNAT1.png)

![使用SNAT技术处理过的网络](https://melville-images.oss-cn-shanghai.aliyuncs.com/blog/iperf%E5%92%8Cfirewalled%E7%9A%84%E5%8C%BA%E5%88%AB/%E4%BD%BF%E7%94%A8SNAT1.png)


使用iptables命令实现SNAT技术是一件很麻烦的事情，但是在firewall-config中却是小菜一碟了。用户只需按照图8-8进行配置，并选中Masquerade zone复选框，就自动开启了SNAT技术。

![开启防火墙的SNAT技术](https://melville-images.oss-cn-shanghai.aliyuncs.com/blog/iperf%E5%92%8Cfirewalled%E7%9A%84%E5%8C%BA%E5%88%AB/%E5%BC%80%E5%90%AF%E4%BC%AA%E8%A3%85%E5%8A%9F%E8%83%BD.png)

为了让大家直观查看不同工具在实现相同功能的区别，这里使用firewall-config工具重新演示了前面使用firewall-cmd来配置防火墙策略规则，将本机888端口的流量转发到22端口，且要求当前和长期均有效，具体如图8-9和图8-10所示。

![配置本地的端口转发](https://melville-images.oss-cn-shanghai.aliyuncs.com/blog/iperf%E5%92%8Cfirewalled%E7%9A%84%E5%8C%BA%E5%88%AB/%E5%B0%86%E5%90%91%E6%9C%AC%E6%9C%BA888%E7%AB%AF%E5%8F%A3%E7%9A%84%E8%AF%B7%E6%B1%82%E8%BD%AC%E5%8F%91%E8%87%B3%E6%9C%AC%E6%9C%BA%E7%9A%8422%E7%AB%AF%E5%8F%A3.png)

![让防火墙效策略规则立即生效](https://melville-images.oss-cn-shanghai.aliyuncs.com/blog/iperf%E5%92%8Cfirewalled%E7%9A%84%E5%8C%BA%E5%88%AB/reload%E7%AB%8B%E5%8D%B3%E7%94%9F%E6%95%88.png)

配置富规则，让192.168.10.20主机访问到本机的1234端口号，如图8-11所示。

![配置防火墙富规则策略](https://melville-images.oss-cn-shanghai.aliyuncs.com/blog/iperf%E5%92%8Cfirewalled%E7%9A%84%E5%8C%BA%E5%88%AB/%E4%BB%85%E5%85%81%E8%AE%B8192.168.10.20%E4%B8%BB%E6%9C%BA%E8%AE%BF%E9%97%AE%E6%9C%AC%E6%9C%BA%E7%9A%841234%E7%AB%AF%E5%8F%A3.png)

如果生产环境中的服务器有多块网卡在同时提供服务（这种情况很常见），则对内网和对外网提供服务的网卡要选择的防火墙策略区域也是不一样的。也就是说，可以把网卡与防火墙策略区域进行绑定（见图8-12），这样就可以使用不同的防火墙区域策略，对源自不同网卡的流量进行针对性的监控，效果会更好。

最后，刘遄老师想说的是，firewall-config工具真的非常实用，很多原本复杂的长命令被用图形化按钮替代，设置规则也简单明了，足以应对日常工作。所以再次向大家强调配置防火墙策略的原则—只要能实现所需的功能，用什么工具请随君便。

![把网卡与防火墙策略区域进行绑定](https://melville-images.oss-cn-shanghai.aliyuncs.com/blog/iperf%E5%92%8Cfirewalled%E7%9A%84%E5%8C%BA%E5%88%AB/%E6%9F%A5%E7%9C%8B%E7%BD%91%E5%8D%A1%E8%AE%BE%E5%A4%87%E4%BF%A1%E6%81%AF.png)

# 4. 服务的访问控制列表

TCP Wrappers是RHEL 7系统中默认启用的一款流量监控程序，它能够根据来访主机的地址与本机的目标服务程序作出允许或拒绝的操作。换句话说，Linux系统中其实有两个层面的防火墙，第一种是前面讲到的基于TCP/IP协议的流量过滤工具，而TCP Wrappers服务则是能允许或禁止Linux系统提供服务的防火墙，从而在更高层面保护了Linux系统的安全运行。

TCP Wrappers服务的防火墙策略由两个控制列表文件所控制，用户可以编辑允许控制列表文件来放行对服务的请求流量，也可以编辑拒绝控制列表文件来阻止对服务的请求流量。控制列表文件修改后会立即生效，系统将会先检查允许控制列表文件（/etc/hosts.allow），如果匹配到相应的允许策略则放行流量；如果没有匹配，则去进一步匹配拒绝控制列表文件（/etc/hosts.deny），若找到匹配项则拒绝该流量。如果这两个文件全都没有匹配到，则默认放行流量。

TCP Wrappers服务的控制列表文件配置起来并不复杂，常用的参数如表4所示。

表4        TCP Wrappers服务的控制列表文件中常用的参数

|客户端类型   |示例  |满足示例的客户端列表
|:-----------|:--|:--|
|单一主机          |192.168.10.10                   |IP地址为192.168.10.10的主机|
|指定网段          |192.168.10.                     |IP段为192.168.10.0/24的主机|
|指定网段          |192.168.10.0/255.255.255.0      |IP段为192.168.10.0/24的主机|
|指定DNS后缀       |.linuxprobe.com                 |所有DNS后缀为.linuxprobe.com的主机|
|指定主机名称      |www.linuxprobe.com              |主机名称为www.linuxprobe.com的主机|
|指定所有客户端    |ALL                             |所有主机全部包括在内|

在配置TCP Wrappers服务时需要遵循两个原则：

1. 编写拒绝策略规则时，填写的是服务名称，而非协议名称；
2. 建议先编写拒绝策略规则，再编写允许策略规则，以便直观地看到相应的效果。

下面编写拒绝策略规则文件，禁止访问本机sshd服务的所有流量（无须/etc/hosts.deny文件中修改原有的注释信息）：

```bash
[root@linuxprobe ~]# vim /etc/hosts.deny
#
# hosts.deny This file contains access rules which are used to
# deny connections to network services that either use
# the tcp_wrappers library or that have been
# started through a tcp_wrappers-enabled xinetd.
#
# The rules in this file can also be set up in
# /etc/hosts.allow with a 'deny' option instead.
#
# See 'man 5 hosts_options' and 'man 5 hosts_access'
# for information on rule syntax.
# See 'man tcpd' for information on tcp_wrappers
sshd:*
[root@linuxprobe ~]# ssh 192.168.10.10
ssh_exchange_identification: read: Connection reset by peer
```

接下来，在允许策略规则文件中添加一条规则，使其放行源自192.168.10.0/24网段，访问本机sshd服务的所有流量。可以看到，服务器立刻就放行了访问sshd服务的流量，效果非常直观：

```bash
[root@linuxprobe ~]# vim /etc/hosts.allow
#
# hosts.allow This file contains access rules which are used to
# allow or deny connections to network services that
# either use the tcp_wrappers library or that have been
# started through a tcp_wrappers-enabled xinetd.
#
# See 'man 5 hosts_options' and 'man 5 hosts_access'
# for information on rule syntax.
# See 'man tcpd' for information on tcp_wrappers
sshd:192.168.10.

[root@linuxprobe ~]# ssh 192.168.10.10
The authenticity of host '192.168.10.10 (192.168.10.10)' can't be established.
ECDSA key fingerprint is 70:3b:5d:37:96:7b:2e:a5:28:0d:7e:dc:47:6a:fe:5c.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '192.168.10.10' (ECDSA) to the list of known hosts.
root@192.168.10.10's password: 
Last login: Wed May 4 07:56:29 2017
[root@linuxprobe ~]# 
```





---
