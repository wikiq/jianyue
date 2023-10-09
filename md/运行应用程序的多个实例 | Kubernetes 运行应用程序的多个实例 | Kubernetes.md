> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [kubernetes.io](https://kubernetes.io/zh-cn/docs/tutorials/kubernetes-basics/scale/scale-intro/)

> 目标 用 kubectl 扩缩应用程序 扩缩应用程序 在之前的模块中，我们创建了一个 Deployment，然后通过 Service 让其可以开放访问。

1.  [Kubernetes 文档](https://kubernetes.io/zh-cn/docs/)
2.  [教程](https://kubernetes.io/zh-cn/docs/tutorials/)
3.  [学习 Kubernetes 基础知识](https://kubernetes.io/zh-cn/docs/tutorials/kubernetes-basics/)
4.  [扩缩你的应用](https://kubernetes.io/zh-cn/docs/tutorials/kubernetes-basics/scale/)
5.  [运行应用程序的多个实例](https://kubernetes.io/zh-cn/docs/tutorials/kubernetes-basics/scale/scale-intro/)

**Information in this document may be out of date**

This document has an older update date than the original, so the information it contains may be out of date. If you're able to read English, see the English version for the most up-to-date information: [Running Multiple Instances of Your App](https://kubernetes.io/docs/tutorials/kubernetes-basics/scale/scale-intro/)

### 扩缩应用程序

在之前的模块中，我们创建了一个 [Deployment](https://kubernetes.io/zh-cn/docs/concepts/workloads/controllers/deployment/)，然后通过 [Service](https://kubernetes.io/zh-cn/docs/concepts/services-networking/service/) 让其可以开放访问。Deployment 仅为跑这个应用程序创建了一个 Pod。 当流量增加时，我们需要扩容应用程序满足用户需求。

**扩缩** 是通过改变 Deployment 中的副本数量来实现的。

_在运行 kubectl run 命令时，你可以通过设置 --replicas 参数来设置 Deployment 的副本数。_

  

扩展 Deployment 将创建新的 Pods，并将资源调度请求分配到有可用资源的节点上，收缩 会将 Pods 数量减少至所需的状态。Kubernetes 还支持 Pods 的[自动缩放](https://kubernetes.io/zh-cn/docs/tasks/run-application/horizontal-pod-autoscale/)，但这并不在本教程的讨论范围内。将 Pods 数量收缩到 0 也是可以的，但这会终止 Deployment 上所有已经部署的 Pods。

运行应用程序的多个实例需要在它们之间分配流量。服务 (Service) 有一种负载均衡器类型，可以将网络流量均衡分配到外部可访问的 Pods 上。服务将会一直通过端点来监视 Pods 的运行，保证流量只分配到可用的 Pods 上。

_扩缩是通过改变 Deployment 中的副本数量来实现的。_

  

一旦有了多个应用实例，就可以没有宕机地滚动更新。我们将会在下面的模块中介绍这些。现在让我们使用在线终端来体验一下应用程序的扩缩过程。