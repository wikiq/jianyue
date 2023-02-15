> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [bbs.huaweicloud.com](https://bbs.huaweicloud.com/blogs/263494)

> KYON（Keep Your Own Network）是华为云推出的企业级云网络解决方案，帮助用户 “带着原来的网络” 极简、敏捷、快速上云，是企业上云的不二...

KYON（Keep Your Own Network）是华为云推出的企业级云网络解决方案，帮助用户 “带着原来的网络” 极简、敏捷、快速上云，是企业上云的不二之选。解决方案咨询，请参考 [KYON 企业级云网络解决方案](https://www.huaweicloud.com/solution/kyon.html?utm_source=huaweiyun&utm_medium=ruanwen&utm_campaign=kyonsolution&utm_content=yunxiaoke&utm_term=CIS-076)。  
**什么是 KYON 呢？** 简单来说 KYON 能让用户直接将 IDC 组网搬到云上，网段零修改，简单又好用。  
具体来说就是针对用户在业务上云不同阶段的关键诉求，KYON 提供了私网 NAT、二层连接网关（L2CG）、混合负载均衡和 VPC 终端节点（VPC Endpoint）等服务，帮助用户极简规划网络、敏捷迁移业务、无缝融合地使用 IDC 和云上资源。  
![](https://bbs-img.huaweicloud.com/blogs/img/1619577273438065388.png)

场景一：网络规划阶段 - 网段免修改上云
--------------------

**业务背景**  
某公司的 2 个子公司网段独立规划，存在**子网网段重叠**的情况。客户希望保留原网段上云，且上云后仍能相互访问。  
图 1 IDC 网络模型示例  
![](https://bbs-img.huaweicloud.com/blogs/img/1619577397211034451.png)  
可以通过在华为云上创建 2 个虚拟私有云（VPC）并划分子网，实现 2 个子公司的网段迁移上云。但是 2 个有重叠子网的 VPC 之间通常无法直接相互访问，也无法通过 VPC 对等连接服务打通 VPC 相互访问。VPC 对等链接简介和约束，请单击[这里](https://support.huaweicloud.com/usermanual-vpc/zh-cn_topic_0046655036.html?utm_source=huaweiyun&utm_medium=ruanwen&utm_campaign=usermanual-vpc-ddlj&utm_content=yunxiaoke&utm_term=CIS-076)。  
不修改网段直接迁移上云，让有重叠子网的 2 个 VPC 相互访问，是用户网络迁移上云过程中一个令人头痛的问题。

**方案实现**  
华为云**私网 NAT** 服务，可以完美解决 VPC 间重叠子网相互访问的诉求。如图 2，可以创建一个中转 VPC，然后使用私网 NAT 服务将部门 A 的 192.168.0.1 转化为 10.0.0.33、将部门 B 的 192.168.0.1 转化为 10.0.0.22，通过转化后的 IP 地址相互访问。  
图 2 私网 NAT 服务示意  
![](https://bbs-img.huaweicloud.com/blogs/img/1619577468118019823.png)  
私网 NAT 更多功能和配置方法，请参考[华为云 KYON 之私网 NAT 网关](https://bbs.huaweicloud.com/blogs/254168?utm_source=huaweiyun&utm_medium=ruanwen&utm_campaign=kyon-nat&utm_content=yunxiaoke&utm_term=CIS-076)。

场景二：上云迁移阶段 - IDC 主机 IP 地址配置不变访问云上主机
-----------------------------------

**业务背景**  
某公司已经使用云专线 / VPN 打通与华为云的网络。客户希望迁移部分主机上云，迁移后不修改 IDC 主机配置就能与云上主机相互访问。  
云专线 / VPN 服务可以实现 IDC 和云上网络的**三层互通**，但是无法实现 IDC 主机不修改 IP 地址配置直接访问云上主机。原因是主机迁移到云上之后，IDC 和云上就是隔离的环境，必须通过网关设备才能相互访问。  
**如何不修改 IDC 主机的 IP 地址配置就能访问云上主机呢？** 需要云上子网与 IDC 子网间**二层网络互通**。

**方案实现**  
华为云**二层连接网关（L2CG）服务**，能够实现 IDC 与云上 VPC 之间的二层网络互通。如图 3，利用二层连接网关和线下 VxLAN 交换机构建二层隧道，在云专线 / VPN 的三层网络的基础上构建大二层网络。IDC 和云上 VPC 的主机在一个二层域内，完美实现 IDC 主机 IP 地址配置不变访问云上主机。并且可以实现迁移过程中不中断业务，将部门 A 中的 192.168.0.3 主机直接迁移到云上 VPC 内。  
图 3 使用 L2CG 实现服务器二层迁移  
![](https://bbs-img.huaweicloud.com/blogs/img/1619577542374063305.png)  
L2CG 更多功能和配置方法，请参考[华为云 KYON 之 L2CG](https://bbs.huaweicloud.com/blogs/259851?utm_source=huaweiyun&utm_medium=ruanwen&utm_campaign=kyon-l2cg&utm_content=yunxiaoke&utm_term=CIS-076) 。

场景三：IDC 和云上融合阶段 - IDC 和云上服务器负载分担
--------------------------------

**业务背景**  
某公司的部门 A 对用户提供服务，客户希望云上主机作为 IDC 主机的扩展，云上云下主机组成业务集群，集群内负载分担。并且在业务高峰期能够使用云上资源快速扩容，适配高峰业务诉求。  
图 4 IDC 负载均衡访问后端服务器  
![](https://bbs-img.huaweicloud.com/blogs/img/1619577610572029819.png)  
IDC 主机可以使用云专线 / VPN 服务与云上主机相互访问，但是 IDC 的负载均衡器无法绑定云上主机做负载分担。  
如何要实现云上和 IDC 主机负载分担呢？需要能同时绑定云上和 IDC 内的主机做负载分担的负载均衡器。

**方案实现**  
华为云弹性负载均衡服务的混合负载均衡功能，支持绑定云上和 IDC 内的主机，实现负载分担。结合弹性伸缩（AS）服务，还能基于业务情况自动申请 / 释放云上主机资源。  
如图 5，独享型负载均衡实例绑定云上 10.0.0.5 主机和 IDC 内的 192.168.0.1、192.168.0.5 主机作为负载分担的后端服务器组，实现负载分担。并且关联弹性伸缩服务，根据业务需要自动在云上扩展主机到业务集群内。  
图 5 使用混合负载均衡功能实现 IDC 和云上主机负载分担  
![](https://bbs-img.huaweicloud.com/blogs/img/1619577648748094226.png)  
ELB 混合负载均衡更多功能和配置方法，请参考[华为云 KYON 之 ELB 混合负载均衡](https://bbs.huaweicloud.com/blogs/264117?utm_source=huaweiyun&utm_medium=ruanwen&utm_campaign=kyon-elb&utm_content=yunxiaoke&utm_term=CIS-076)。

场景四：IDC 和云上融合阶段 - IDC 应用使用云上服务
------------------------------

**业务背景**  
随着云上的服务越来越丰富，尤其是高阶服务（例如 EI 企业智能服务、数据库服务）能力越来越强大。用户希望 IDC 应用能够使用高阶服务，帮助业务创新变革。  
但是本地部署高阶云服务的部署复杂度和后期维护成本是用户头痛的问题。

**方案实现**  
华为云 VPC 终端节点（VPC Endpoint）服务，结合云专线（DC）/ 虚拟专用网络（VPN），实现 IDC 内的应用访问云上的服务。  
如图 6，IDC 的应用通过云专线 / VPN 访问云上的 VPC 终端节点，就能够使用华为云上已经发布的云服务，例如数据库服务、EI 企业智能服务等。  
图 6 使用 VPC 终端节点服务实现 IDC 应用使用云上服务  
![](https://bbs-img.huaweicloud.com/blogs/img/1619577700762038013.png)  
VPCEP 更多功能和配置方法，请参考[华为云 KYON 之 VPCEP](https://bbs.huaweicloud.com/blogs/278349?utm_source=huaweiyun&utm_medium=ruanwen&utm_campaign=kyon-vpcep&utm_content=yunxiaoke&utm_term=CIS-076)。

了解更多 KYON 介绍和操作，看下面：

*   KYON 解决方案介绍：[KYON 企业级云网络解决方案](https://www.huaweicloud.com/solution/kyon.html?utm_source=huaweiyun&utm_medium=ruanwen&utm_campaign=kyonsolution&utm_content=yunxiaoke&utm_term=CIS-076)
*   私网 NAT 更多功能和配置方法：[华为云 KYON 之私网 NAT 网关](https://bbs.huaweicloud.com/blogs/254168?utm_source=huaweiyun&utm_medium=ruanwen&utm_campaign=kyon-nat&utm_content=yunxiaoke&utm_term=CIS-076)
*   L2CG 更多功能和配置方法：[华为云 KYON 之 L2CG](https://bbs.huaweicloud.com/blogs/259851?utm_source=huaweiyun&utm_medium=ruanwen&utm_campaign=kyon-l2cg&utm_content=yunxiaoke&utm_term=CIS-076)
*   混合负载均衡更多功能和配置方法：[华为云 KYON 之 ELB 混合负载均衡](https://bbs.huaweicloud.com/blogs/264117?utm_source=huaweiyun&utm_medium=ruanwen&utm_campaign=kyon-elb&utm_content=yunxiaoke&utm_term=CIS-076)
*   VPCEP 更多功能和配置方法，请参考[华为云 KYON 之 VPCEP](https://bbs.huaweicloud.com/blogs/278349?utm_source=huaweiyun&utm_medium=ruanwen&utm_campaign=kyon-vpcep&utm_content=yunxiaoke&utm_term=CIS-076)

【版权声明】本文为华为云社区用户原创内容，转载时必须标注文章的来源（华为云社区），文章链接，文章作者等基本信息，否则作者和本社区有权追究责任。如果您发现本社区中有涉嫌抄袭的内容，欢迎发送邮件至：[cloudbbs@huaweicloud.com](mailto:cloudbbs@huaweicloud.com) 进行举报，并提供相关证据，一经查实，本社区将立刻删除涉嫌侵权内容。