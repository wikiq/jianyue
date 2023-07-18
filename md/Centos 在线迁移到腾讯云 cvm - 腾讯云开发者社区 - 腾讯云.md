> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [cloud.tencent.com](https://cloud.tencent.com/developer/article/1687584)

> （4）检查 SELinux 是否已打开。如果 SELinux 已打开，请关闭 SELinux

一. 环境检查：
--------

1. 源端环境：

（1）注意选择 centos 的操作系统的源端机器

（2）要有[公网 ip](https://cloud.tencent.com/product/eip?from=20065&from_column=20065) 和公网带宽

（3）检查是否安装了 rsync，可执行`which rsync`命令进行验证

（4）检查 SELinux 是否已打开。如果 SELinux 已打开，请关闭 SELinux

（5）检查和安装 virtio 驱动，详情可参考：

[https://cloud.tencent.com/document/product/213/9929#CheckVirtioForInitramfs](https://cloud.tencent.com/document/product/213/9929?from=20421&from_column=20421#CheckVirtioForInitramfs)

2. 目标环境（腾讯云）：

（1）注意选择 centos 的操作系统的 [CVM](https://cloud.tencent.com/product/cvm?from=20421&from_column=20421)

（2）尽量保证目标端 [CVM](https://cloud.tencent.com/product/cvm?from=20065&from_column=20065) 和源端源端机器在一个地区，会加速迁移

（3）要有公网 ip 和公网带宽

（4）CVM 的容量要大于等于源端机器的容量（包括系统盘和数据盘）

（5）建议尽可能调大两端的带宽，以便更快迁移

（6）安全组需要放开 80 和 443 端口

二. 上传迁移工具至源端机器
--------------

1. 下载[迁移工具](https://cloud.tencent.com/product/msp?from=20065&from_column=20065)到本地，文档链接如下：

https://cloud.tencent.com/document/product/213/38783

![](https://ask.qcloudimg.com/http-save/yehe-4476879/zj0xm26p17.png?imageView2/2/w/1200)

点击即可

2. 在源端机器上安装 lrzsz

yum -y install lrzsz

![](https://ask.qcloudimg.com/http-save/yehe-4476879/9ovk05o9vj.png?imageView2/2/w/1200)

3. 上传迁移工具到源端机器

rz

![](https://ask.qcloudimg.com/http-save/yehe-4476879/9gt9imiw07.png?imageView2/2/w/1200)

三. 在源端修改迁移工具配置文件
----------------

1. 对迁移工具包解压缩

unzip go2tencentcloud.zip

![](https://ask.qcloudimg.com/http-save/yehe-4476879/pp4bb7nm9a.png?imageView2/2/w/1200)

2. 修改 user.json 配置文件

vim user.json

{

"SecretId": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",

"SecretKey": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",

"Region": "ap-beijing",

"InstanceId": "ins-xxxxxxxxx",

"DataDisks": [

{

"Index": 1,

"Size": 40,

"MountPoint": "/root/mnt"

}

]

}

![](https://ask.qcloudimg.com/http-save/yehe-4476879/mq8dwibb45.png?imageView2/2/w/1200)

备注：

（1）SecretId 和 Secretkey 从腾讯云控制台登录后，从访问管理 -> 访问秘钥 ->API 密钥管理获取

![](https://ask.qcloudimg.com/http-save/yehe-4476879/28527qfm33.png?imageView2/2/w/1200)

（2）Region 代表目标端 CVM 实例的所在地域，可从如下链接查找：

[https://cloud.tencent.com/document/product/213/6091](https://cloud.tencent.com/document/product/213/6091?from=20421&from_column=20421)

（3）DataDisks 是非必填项，index 代表第几块数据盘，Size 代表这块盘的大小，MountPoint 代表的是源端 ECS 上数据盘的挂载目录

四. 发起迁移
-------

1.sudo ./go2tencentcloud_x64

然后就一直等待，不要动，此时目的端 CVM 进入迁移流程

![](https://ask.qcloudimg.com/http-save/yehe-4476879/ey130qqb7y.png?imageView2/2/w/1200)

![](https://ask.qcloudimg.com/http-save/yehe-4476879/ow1rb01fib.png?imageView2/2/w/1200)

2. 迁移结束

![](https://ask.qcloudimg.com/http-save/yehe-4476879/m5bw3f7jpz.png?imageView2/2/w/1200)

五. 可自行在目标端 CVM 验证数据完整性和业务情况
---------------------------

点击展开阅读全文

原创声明：本文系作者授权腾讯云开发者社区发表，未经许可，不得转载。

如有侵权，请联系 cloudcommunity@tencent.com 删除。

相关文章

[【腾讯云的 1001 种玩法】如何使用腾讯云 CVM 构建自己的云桌面办公平台](https://cloud.tencent.com/developer/article/1004602)

本文将介绍如何在一台 Windows2008 的 CVM 云服务器上重新安装系统为 Win10，如何在重装后正确的恢复网卡驱动程序到目标系统。

[腾讯云 Ubuntu 下 WordPress 从 Apache 迁移到 Nginx 过程](https://cloud.tencent.com/developer/article/1004644)

需求之前一直都用 Apache 服务器，由于网站访问量比较大，另外加上旧服务器快到期了，准备迁移到新的服务器上，所以决定采用 Nginx 服务器。迁移过程比较心酸，之前一直用 apache，对 nginx 服务器配置不熟悉，踩了很多坑。下面说一下我的网站

[如何使用腾讯云 45 元代金券购买带宽按量计费的云主机？](https://cloud.tencent.com/developer/article/1004741)

对于收到腾讯云 45 元代金券，想要尝试一下云主机使用体验用户。可以阅读本教程，了解如何使用腾讯云 45 元代金券，购买带宽按量计费的云主机。

[【腾讯云的 1001 种玩法】在 CVM 上使用腾讯云 Docker 镜像加速构建](https://cloud.tencent.com/developer/article/1004784)

国内网络访问 docker 官方的仓库速度不快，最近腾讯云提供了 Docker 镜像接入，相比之下下提速显著。下面跟大家分享下如何在 CVM 上使用腾讯云 Docker 镜像加速构建。

[【腾讯云的 1001 种玩法】新手教程：腾讯云 CentOS7 安装 LNMP+wordpress](https://cloud.tencent.com/developer/article/1004838)

这篇文章是大一的时候写的，因为腾讯云对大学生有 1 元云主机的优惠项目，就买了一个，开启了我的云端之旅。搭建博客是技术宅的入门必备技能。所以就从最简单的 wordpress 开始练手吧，整个过程顺利的话只需要十来分钟。

[【腾讯云的 1001 种玩法】腾讯云备份手机照片、办公室文档、手机电脑快速传图](https://cloud.tencent.com/developer/article/1004846)

时时备份我最喜欢 dropbox，所以决定自己搭建备份服务器。本文给大家讲述通过 Syncthing 开源的客户端，在腾讯云部署环境备份手机照片、办公室文档、手机电脑快速传图。

[【在线分享】腾讯云高性能云硬盘入门与实战](https://cloud.tencent.com/developer/article/1004988)

腾讯云技术社区特别邀请到了负责云硬盘 CBS 产品经理和研发工程师进行在线分享，为大家全面地介绍云硬盘服务的具体情况，同时分享一些云硬盘使用的最佳实践。

[如何在腾讯云主机上快速部署 F-Stack HTTP 服务](https://cloud.tencent.com/developer/article/1005180)

F-Stack 是一个全用户态的高性能的网络接入开发包，本文介绍如何在腾讯云主机上使用 F-Stack 快速部署 HTTP 服务器。

[姚俊军：如何设计数据迁移方案](https://cloud.tencent.com/developer/article/1009120)

好的迁移方案设计不仅能够节省迁移成本，还能帮助用户拥有更加完备的异地部署和灾备能力。腾讯云技术专家姚俊军在现场讲解了如何设计数据迁移方案，还和大家分享了两个数据迁移的实际案例。

[腾讯云 CentOS 下 nodejs 的安装方法](https://cloud.tencent.com/developer/article/1055280)

访问链接 https://nodejs.org/en/download/ 找到最新的文件。如图所示是最新的安装文件

[无所谓](https://cloud.tencent.com/developer/article/1057092)

VPS 有很多种玩法，在墙上打洞是最常见的玩法之一。打洞方法多种多样，其中以 PPTP 最为常见，也是配置起来最为简便的方式之一。 本脚本只需执行一次即可将 PPTP 服务安装完毕，然后在你的电脑里设置好 VPN 即可。当然了，要保证你的 VPS 是在外面的自由世界中，而且 VPS 是基于 Xen 或 KVM 的。

[腾讯云 API：用 Python 使用腾讯云 API（cvm 实例）](https://cloud.tencent.com/developer/article/1139698)

腾讯云 API 地址：https://cloud.tencent.com/document/api

[Hexo 博客部署到腾讯云教程](https://cloud.tencent.com/developer/article/1140005)

本文首发于我的个人博客：『不羁阁』文章链接：传送门 本篇内容用来讲述如何将 hexo 博客部署到腾讯云的服务器上。 只要通过三步即可成功部署： 云服务器端 git 的配置 Nginx 的配置 本地端 hexo 的设置更改 前言 最近趁着腾讯云在做活动，买了 3 年的服务器。正好自己的博客之前是搭建在 coding 上的，现在也可以顺便部署到腾讯云上了。其实过程蛮简单的，即使，你是个对后台一窍不通的小白，也能很容易部署成功。顺便安利下腾讯云的活动。通过以下两步，即可 360 块钱买到 40 个月 (三年半) 的云服务

[腾讯云 CentOS 部分公有镜像系统下线的通知](https://cloud.tencent.com/developer/article/1140748)

随着 CentOS 的快速发展，目前 CentOS 官方支持的主要版本是 6.9 和 7.4，腾讯云服务器 CentOS 对官方不再维护的版本做下线处理。将于 2018 年 4 月 20 日正式下线以下公有镜像，届时将无法使用以下公有镜像购买新的 CVM 实例和重装 CVM 实例。 【注】其他镜像、自定义镜像、服务市场镜像以及导入的镜像使用不受影响。 下线的公有镜像列表如下： CentOS 6.2 64 位 CentOS 6.3 32/64 位 CentOS 6.4 32/64 位 CentOS 6.5 3

[腾讯云服务器 CVM(免费 30 天使用) 申请过程](https://cloud.tencent.com/developer/article/1140944)

随着国内 VPS 空间的激烈竞争，各大商家也定期推出各种活动，比如腾讯云就推出了免费体验馆，可以享受多款腾讯云产品免费体验服务。基于腾讯云的安全可靠高性能，今天魏艾斯博客来申请一下腾讯云提供的 30 天云主机，具体配置是：1 核 CPU，1GB 内存，1Mbps 带宽，30GB 数据盘。定位是入门级云服务器 CVM，这篇文章就记录一下申请过程。

[腾讯云 centos 安装 python3.6 和 pip](https://cloud.tencent.com/developer/article/1145483)

不知道腾讯云的 centos 和阿里云的 centos 一不一样，反正两个云平台的 Ubuntu 系统是不一样的，照着同样的教程敲，往往掉坑里。 安装一些 centos 依赖库： 这一步很关键，很多报错往往都因为少了这一步 yum install -y gcc zlib* openssl-devel openssl 安装 pip wget https://bootstrap.pypa.io/get-pip.py --no-check-certificate sudo python get-pip.py 安装 python3.

[腾讯云 centos7 安装 MySQL](https://cloud.tencent.com/developer/article/1145484)

centos 就 centos 呗，为什么要加个腾讯云呢？有这种疑问的兄 dei，一定是没被不同云的系统坑过啊，阿里云的 Ubuntu 和腾讯云的 Ubuntu 不一样，centos 好像也有差别，各个云平台，同样的系统，不同的标准，如果没找准，非掉坑去不可…… 安装 mysql wget http://dev.mysql.com/get/mysql-community-release-el7-5.noarch.rpm rpm -ivh mysql-community-release-el7-5.noarch.rpm yum