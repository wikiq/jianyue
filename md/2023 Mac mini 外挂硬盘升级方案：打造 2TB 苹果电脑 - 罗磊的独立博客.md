> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [luolei.org](http://luolei.org/2023-mac-mini-ssd-upgrade-experience/)

> 深入了解如何给 2023 年的 Mac mini 进行 2TB 外挂硬盘升级。包括购买决策过程、硬件选择、安装过程，以及性能测试结果。

今年 618，给老婆升级了一套办公电脑。打造了一台拥有 2TB 硬盘的 Mac。

为什么要升级电脑？
---------

我家目前有几台 Mac 电脑

*   MacBook Pro 14 英寸 (2020 年) M1 16GB + 512GB : 我公司配的工作电脑，也是我的主力机
*   MacBook Pro 15 英寸 (2014 年中)  i7 + 16GB + 500GB: 老婆的主力工作电脑，用了这么多年还挺给力的
*   MacBook Aire 13 英寸 (2018 年) i3 + 8GB + 256GB: 我老婆的便携笔记本

老婆是半个视频创作者，主要工作需要用到 Adobe 和视频剪辑 Final Cut Pro ，尽管在 macOS 上的 Final Cut Pro 已经针对影像处理进行了许多优化，但这台将近 10 年的老机器在系统升级后终于开始显露疲态，开始承受过大的负担。

为什么选择 Mac mini?
---------------

在购买这台 Mac mini 之前，我也有考虑过 MacBook Pro M1 Pro 和 2023 年新款 MacBook Air 15 英寸 的方案，我老婆个人则想要买 iMac。

*   ~Macbook~ :  老婆已有 MacBook，加上确实没有移动办公需求，有点浪费。Apple 笔记本产品线 16G + 512GB 规格组合起售价到 10K 价位以上，不太划算。
*   ~iMac 24 英寸:~ iMac 实际上也是个不错的选择，但是现在的 iMac 依旧只有 M1 芯片，加上只有 24 英寸的小屏幕。放弃。

考虑到家里其他外设，4K 显示器、键盘鼠标也都齐全了，评估了下当前的需求和设备。最终发现了 Mac Mini 配合雷电硬盘盒外接大容量固态硬盘组合。

说来我对这个组合并不陌生。我在 2017 年就曾经写过文章，分享 iMac 通过 USB3.0 外接硬盘提速的方案。

*   [iMac 2015 5K 版外接 USB3.0 硬盘盒 + SSD 系统加速体验](https://luolei.org/imac-5k-external-usb-ssd-update/)

这么多年过去了，苹果的内存和硬盘依旧金贵，最终我还是采用 Mac mini 加外挂硬盘的方案，规格和价格如下，全部购自京东渠道。

![](https://static.is26.com/blog/2023/06/mini/order-1.jpg)

除了 Mac mini 是通过北京消费券优惠满 6000-600，再叠加店铺满 6000-200  稍微麻烦的方案，其他硬件都是 618 优惠价格。

![](https://static.is26.com/blog/2023/06/mini/mac-1.jpg)

这就是今天的配件主角，WERO 的雷电硬盘盒和海力士 Solidigm P44 Pro 。现在 SSD 价格极其优惠，虽然甚至不到 600 就能买到国产的高速固态硬盘，但是由于我是打算给 Mac mini 用作系统盘，所以比较看着稳定性和质量，海力士这块 P44 Pro 算是第一梯队的高档产品，也不便宜。

![](https://static.is26.com/blog/2023/06/mini/mac-2.jpg)

这个就是 WERO 硬盘盒，采用雷电 3 和外挂 USB4 协议的双芯片方案，最高支持 40Gbps 传输速率，我也有看过阿卡西斯 acasis 的 TB405 硬盘盒，但是两个品牌优惠后价格并没有差太多，最终选择了 WERO。如果预算有限，并且只给 Mac 使用，也可以选择单雷电 3 芯片的盒子，价格会便宜不少。

![](https://static.is26.com/blog/2023/06/mini/mac-3.jpg)

很久没有关注硬件市场了，一开始看到 SOLIDIGM 这个牌子我还纳闷这是啥，后来查了下资料发现原来是海力士收购英特尔固态硬盘业务后成立的品牌，也算是第一梯队。我是在京东自营店购买的这款硬盘，其包装盒上标明其制造工厂位于美国特拉华州。

![](https://static.is26.com/blog/2023/06/mini/mac-4.jpg)

盒子插入 WERO 硬盘盒，WERO 盒子有附赠螺丝刀和散热贴。M2 硬盘发热量大，WERO 这种盒子铝合金散热还是有必要的。

![](https://static.is26.com/blog/2023/06/mini/mac-5.jpg)

安装好后，将盒子连接到 Mac mini 背面靠近 HDMI 的雷雳 4 接口，就可以跑满速度了。WERO 这个盒子我看评价说颜值在线，实际观感还可以，我买的雾面银色。

![](https://static.is26.com/blog/2023/06/mini/mac-7.jpg)

安装系统到外接硬盘的步骤也比较简单：

*   格式化硬盘 AFPS 格式 + GUID 分区
*   在 App Store 下载最新 macOS
*   安装 macOS 到外接硬盘即可

![](https://static.is26.com/blog/2023/06/mini/mac-9.jpg)

安装完成后，重启电脑并设定全新的系统环境。此时，系统已经运行在外接硬盘上，测速读写速度可达 2700MB/s。

![](https://static.is26.com/blog/2023/06/mini/mac-8.jpg)

再放一张 mini 内置自带 256GB 和外接 2TB 硬盘的读写速度对比。

总结
--

M2 + 16GB 内存 + 2T 硬盘组合，总价 6.3K，对于我来说，还算是一个性价比不错的方案。![](https://s.w.org/images/core/emoji/14.0.0/svg/1f604.svg)

#### 优点：

*   M2 芯片 + 16GB 的内存，性能足够应付大多数的工作场景。
*   2TB 的固态硬盘: 速度足够快，基本不用再担心空间不够。

#### 缺点：

*   外挂一个硬盘，占了一个雷雳接口，另外一个雷雳接口我目前接了一个雷电拓展坞。
*   肯定没有内置硬盘稳定: 虽然我之前 iMac 外挂 USB 硬盘用了几年也没出过啥问题。

如果你没有现成显示器键盘，个人建议还是买 MacBook Pro M1 16GB + 512GB 的版本。但是如果你的其他条件满足，Mac mini + 外挂硬盘不乏性价比不错的选择。