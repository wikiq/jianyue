> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [plantegg.github.io](https://plantegg.github.io/2019/06/09/%E4%B8%80%E6%96%87%E6%90%9E%E6%87%82%E5%9F%9F%E5%90%8D%E8%A7%A3%E6%9E%90%E7%9B%B8%E5%85%B3%E9%97%AE%E9%A2%98/)

> 一文搞懂域名解析 DNS 相关问题 本文希望通过一篇文章解决所有域名解析中相关的问题 最后会通过实际工作中碰到的不同场景下几个 DNS 问题的分析过程来理解 DNS 这几个 Case 描述如下： 一批 ECS n......

就是要你懂 DNS-- 一文搞懂域名解析相关问题
------------------------

发表于 2019-06-09 | 分类于 [DNS](https://plantegg.github.io/categories/DNS/) | 次

> 本文希望通过一篇文章解决所有域名解析中相关的问题
> 
> 最后会通过实际工作中碰到的不同场景下几个 DNS 问题的分析过程来理解 DNS

这几个 Case 描述如下：

1.  [一批 ECS nslookup 域名结果正确，但是 ping 域名 返回 unknown host](https://plantegg.github.io/2019/01/09/nslookup-OK-but-ping-fail/)
2.  [Docker 容器中的域名解析过程和原理](https://plantegg.github.io/2019/01/12/Docker%E4%B8%AD%E7%9A%84DNS%E8%A7%A3%E6%9E%90%E8%BF%87%E7%A8%8B/)
3.  [中间件的 VipClient 服务在 centos7 上域名解析失败](https://plantegg.github.io/2019/01/13/%E4%B8%AD%E9%97%B4%E4%BB%B6%E7%9A%84%E8%87%AA%E5%B7%B1%E7%9A%84DNS%E6%9C%8D%E5%8A%A1%E5%9C%A8alios7%E4%B8%8A%E5%9F%9F%E5%90%8D%E8%A7%A3%E6%9E%90%E5%A4%B1%E8%B4%A5/)
4.  [在公司网下，我的 windows7 笔记本的 wifi 总是报 dns 域名异常无法上网（通过 IP 地址可以上网）](https://plantegg.github.io/2019/01/10/windows7%E7%9A%84wifi%E6%80%BB%E6%98%AF%E6%8A%A5DNS%E5%9F%9F%E5%90%8D%E5%BC%82%E5%B8%B8%E6%97%A0%E6%B3%95%E4%B8%8A%E7%BD%91/)

因为这些问题都不一样，但是都跟 DNS 服务相关所以打算分四篇文章挨个介绍，希望看完后能加深对 DNS 原理的理解并独立解决任何 DNS 问题。

下面我们就先开始介绍下 DNS 解析原理和流程。

[](#Linux下域名解析流程 "Linux下域名解析流程")Linux 下域名解析流程
---------------------------------------------

*   DNS 域名解析的时候先根据 /etc/host.conf、/etc/nsswitch.conf 配置的顺序进行 dns 解析（name service switch），一般是这样配置：hosts: files dns 【files 代表 /etc/hosts ； dns 代表 /etc/resolv.conf】(**ping 是这个流程，但是 nslookup 和 dig 不是**)
*   如果本地有 DNS Client Cache，先走 Cache 查询，所以有时候看不到 DNS 网络包。Linux 下 nscd 可以做这个 cache，Windows 下有 ipconfig /displaydns ipconfig /flushdns
*   如果 /etc/resolv.conf 中配置了多个 nameserver，默认使用第一个，只有第一个失败【如 53 端口不响应、查不到域名后再用后面的 nameserver 顶上】

[![](https://plantegg.github.io/images/oss/b7458f344de1d1b10c2a6f6ee7f1c501.png)](https://plantegg.github.io/images/oss/b7458f344de1d1b10c2a6f6ee7f1c501.png)

上述描述主要是阐述的图中 stub resolver 部分的详细流程。这部分流程出问题才是程序员实际中更多碰到的场景

[所以默认的 nsswitch 流程是](https://zwischenzugs.com/2018/06/08/anatomy-of-a-linux-dns-lookup-part-i/)：

[![](https://plantegg.github.io/images/oss/82489e801d8f7bd455053315d760614b.png)](https://plantegg.github.io/images/oss/82489e801d8f7bd455053315d760614b.png)

以下是一个 /etc/nsswitch.conf

```
# cat /etc/nsswitch.conf |grep -v "^#" |grep -v "^$"

passwd:     files sss

shadow:     files sss

group:      files sss

hosts:      files dns myhostname  <<<<< 重点是这一行三个值的顺序

bootparams: nisplus [NOTFOUND=return] files

ethers:     files

netmasks:   files

networks:   files

protocols:  files

rpc:        files

services:   files sss

netgroup:   nisplus sss

publickey:  nisplus

automount:  files nisplus sss

aliases:    files nisplus
```

这个配置中的解析顺序是：files->dns->myhostname, 这个顺序可以调整和配置。

[](#Linux下域名解析流程需要注意的地方 "Linux下域名解析流程需要注意的地方")Linux 下域名解析流程需要注意的地方
------------------------------------------------------------------

*   如果 /etc/resolv.conf 中配置了 rotate，那么多个 nameserver 轮流使用. [但是因为底层库的原因用了 rotate 会触发 nameserver 排序的时候第二个总是排在第一位](https://access.redhat.com/solutions/1426263)
*   如果被解析的域名不是以 “.” 结尾, 那么解释失败后还会尝试 resolv.conf 中 search 追加到后面，[resolv.conf 最多支持 6 个 search 域](https://access.redhat.com/solutions/58028)
*   ping 调用的是 glibc 的 gethostbyname() 函数流程–背后是 NameServiceSwitch，nslookup 不是. 所以你会经常看到其中一个可以另一个不可以，那么就要按第一部分讲解的流程来排查了。

[](#Linux下域名解析诊断工具 "Linux下域名解析诊断工具")Linux 下域名解析诊断工具
---------------------------------------------------

*   ping
*   nslookup (nslookup domain @dns-server-ip)
*   [dig](https://jvns.ca/blog/2021/12/04/how-to-use-dig/) (dig +trace domain)
*   tcpdump (tcpdump -i eth0 host server-ip and port 53 and udp)
*   strace

[![](https://plantegg.github.io/images/951413iMgBlog/dig.png)](https://plantegg.github.io/images/951413iMgBlog/dig.png)

### [](#案例 "案例")案例

向 /etc/hosts 中添加了一条 test.unknow.host, 无法解析到，但是 test.localhost 可以解析？

```
[ren@vb 08:50 /home/ren]
$head -2 /etc/hosts
127.0.0.1　 test.unknow.host
127.0.0.1   test.localhost

[ren@vb 08:50 /home/ren]
$ping test.unknow.host
ping: unknown host test.unknow.host

[ren@vb 08:50 /home/ren]
$ping -c 1 test.localhost
PING test.localhost (127.0.0.1) 56(84) bytes of data.
64 bytes from test.localhost (127.0.0.1): icmp_seq=1 ttl=64 time=0.016 ms

--- test.localhost ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 0.016/0.016/0.016/0.000 ms


```

为什么 test.unknow.host 没法解析到？  
可能有哪些因素导致这种现象？

尝试 ping -c 1 test.localhost 的目的是做什么？

看完前面的理论我的猜测是两种可能导致这种情况：

*   /etc/hosts 没有启用
*   有本地缓存记录了一个 unknow host 记录

#### [](#验证 "验证")验证

```
strace -e trace=open -f ping -c 1 test.localhost


```

可以通，说明 /etc/hosts 是在起作用的，所以最好验证 /etc/hosts 在起作用的方法是往其中添加一条新纪录，然后验证一下

那接下来只能看本地有没有启动 nscd 这样的缓存了，见后发现也没有，这个时候就可以上 strace 追踪 ping 的流程了

[![](https://plantegg.github.io/images/oss/1560992498945-66445687-3184-4c7d-9fbd-764552025041.png)](https://plantegg.github.io/images/oss/1560992498945-66445687-3184-4c7d-9fbd-764552025041.png)

从上图可以清晰地看到读取了 /etc/host.conf, 然后读了 /etc/hosts, 再然后读取到我们添加的那条记录，似乎没问题，仔细看这应该是 ip 地址后面带的是一个中文字符的空格，这就是问题所在。

到这里可能的情况要追加第三种了：

*   /etc/hosts 中添加的记录没生效 (比如中文符号）

### [](#dhcp "dhcp")dhcp

如果启用了 dhcp，那么 dhclient 会更新在 Network Manager 启动的时候更新 /etc/resolv.conf

### [](#dnsmasq "dnsmasq")dnsmasq

一般会在 127.0.0.1:53 上启动 dns server 服务，配置文件对应在：/run/dnsmasq/resolv.conf。集团内部的 vipclient 就是类似这个原理。

[](#微服务下的域名解析、负载均衡 "微服务下的域名解析、负载均衡")微服务下的域名解析、负载均衡
--------------------------------------------------

微服务中多个服务之间一般都是通过一个 vip 或者域名之类的来做服务发现和负载均衡、弹性伸缩，所以这里也需要域名解析（一个微服务申请一个域名）

### [](#域名解析通过jar、lib包 "域名解析通过jar、lib包")域名解析通过 jar、lib 包

基本与上面的逻辑没什么关系，jar 包会去通过特定的协议联系 server，解析出域名对应的多个 ip、机房、权重等

### [](#域名解析通过dns-server "域名解析通过dns server")域名解析通过 dns server

跟前面介绍逻辑一致，一般是 / etc/resolv.conf 中配置的第一个 nameserver 负责解析微服务的域名，解析不到的（如 baidu.com) 再转发给上一级通用的 dns server，解析到了说明是微服务自定义的域名，就可以返回来了

如果这种情况下 / etc/resolv.conf 中配置的第一个 nameserver 是 127.0.0.1, 意味着本地跑了一个 dns server, 这个服务使用 dns 协议监听本地 udp 53 端口

验证方式： nslookup 域名 @127.0.0.1 看看能否解析到你想要的地址

[](#kubernetes-和-docker中的域名解析 "kubernetes 和 docker中的域名解析")kubernetes 和 docker 中的域名解析
------------------------------------------------------------------------------------

一般是通过 iptables 配置转发规则来实现，这种用 iptables 和 tcpdump 基本都可以看清楚。如果是集群内部的话可以通过 CoreDNS 来实现，通过 K8S 动态向 CoreDNS 增删域名，增删 ip，所以这种域名肯定只能在 k8s 集群内部使用

[](#nginx-中的域名解析 "nginx 中的域名解析")nginx 中的域名解析
--------------------------------------------

nginx 可以自定义 resolver，也可以通过读取 /etc/resolv.conf 转换而来，要注意对 /etc/resolv.conf 中 注释的[兼容](https://serverfault.com/questions/638822/nginx-resolver-address-from-etc-resolv-conf)

[https://github.com/blacklabelops-legacy/nginx/issues/36](https://github.com/blacklabelops-legacy/nginx/issues/36) 可能是 nginx 读取 /etc/resolv.conf 没有处理好 # 注释的问题

[](#进一步的Case学习： "进一步的Case学习：")进一步的 Case 学习：
-------------------------------------------

1.  [一批 ECS nslookup 域名结果正确，但是 ping 域名 返回 unknown host](https://plantegg.github.io/2019/01/09/nslookup-OK-but-ping-fail/)
2.  [Docker 容器中的域名解析过程和原理](https://plantegg.github.io/2019/01/12/Docker%E4%B8%AD%E7%9A%84DNS%E8%A7%A3%E6%9E%90%E8%BF%87%E7%A8%8B/)
3.  [中间件的 VipClient 服务在 centos7 上域名解析失败](https://plantegg.github.io/2019/01/13/%E4%B8%AD%E9%97%B4%E4%BB%B6%E7%9A%84%E8%87%AA%E5%B7%B1%E7%9A%84DNS%E6%9C%8D%E5%8A%A1%E5%9C%A8alios7%E4%B8%8A%E5%9F%9F%E5%90%8D%E8%A7%A3%E6%9E%90%E5%A4%B1%E8%B4%A5/)
4.  [在公司网下，我的 windows7 笔记本的 wifi 总是报 dns 域名异常无法上网（通过 IP 地址可以上网）](https://plantegg.github.io/2019/01/10/windows7%E7%9A%84wifi%E6%80%BB%E6%98%AF%E6%8A%A5DNS%E5%9F%9F%E5%90%8D%E5%BC%82%E5%B8%B8%E6%97%A0%E6%B3%95%E4%B8%8A%E7%BD%91/)

[](#参考文章： "参考文章：")参考文章：
-----------------------

[GO DNS 原理解析](https://blog.bruceding.me/516.html)

[Linux 系统如何处理名称解析](https://blog.arstercz.com/linux-%E7%B3%BB%E7%BB%9F%E5%A6%82%E4%BD%95%E5%A4%84%E7%90%86%E5%90%8D%E7%A7%B0%E8%A7%A3%E6%9E%90)

[Anatomy of a Linux DNS Lookup – Part I](https://zwischenzugs.com/2018/06/08/anatomy-of-a-linux-dns-lookup-part-i/)