> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [kubernetes.io](https://kubernetes.io/zh-cn/docs/concepts/scheduling-eviction/kube-scheduler/)

> 在 Kubernetes 中，调度 是指将 Pod 放置到合适的节点上，以便对应节点上的 Kubelet 能够运行这些 Pod。

在 Kubernetes 中，**调度** 是指将 [Pod](https://kubernetes.io/zh-cn/docs/concepts/workloads/pods/) 放置到合适的[节点](https://kubernetes.io/zh-cn/docs/concepts/architecture/nodes/)上，以便对应节点上的 [Kubelet](https://kubernetes.io/docs/reference/generated/kubelet) 能够运行这些 Pod。

调度概览[](#scheduling)
-------------------

调度器通过 Kubernetes 的监测（Watch）机制来发现集群中新创建且尚未被调度到节点上的 Pod。 调度器会将所发现的每一个未调度的 Pod 调度到一个合适的节点上来运行。 调度器会依据下文的调度原则来做出调度选择。

如果你想要理解 Pod 为什么会被调度到特定的节点上， 或者你想要尝试实现一个自定义的调度器，这篇文章将帮助你了解调度。

kube-scheduler[](#kube-scheduler)
---------------------------------

[kube-scheduler](https://kubernetes.io/zh-cn/docs/reference/command-line-tools-reference/kube-scheduler/) 是 Kubernetes 集群的默认调度器，并且是集群 [控制面](https://kubernetes.io/zh-cn/docs/reference/glossary/?all=true#term-control-plane) 的一部分。 如果你真得希望或者有这方面的需求，kube-scheduler 在设计上允许你自己编写一个调度组件并替换原有的 kube-scheduler。

Kube-scheduler 选择一个最佳节点来运行新创建的或尚未调度（unscheduled）的 Pod。 由于 Pod 中的容器和 Pod 本身可能有不同的要求，调度程序会过滤掉任何不满足 Pod 特定调度需求的节点。 或者，API 允许你在创建 Pod 时为它指定一个节点，但这并不常见，并且仅在特殊情况下才会这样做。

在一个集群中，满足一个 Pod 调度请求的所有节点称之为 **可调度节点**。 如果没有任何一个节点能满足 Pod 的资源请求， 那么这个 Pod 将一直停留在未调度状态直到调度器能够找到合适的 Node。

调度器先在集群中找到一个 Pod 的所有可调度节点，然后根据一系列函数对这些可调度节点打分， 选出其中得分最高的节点来运行 Pod。之后，调度器将这个调度决定通知给 kube-apiserver，这个过程叫做 **绑定**。

在做调度决定时需要考虑的因素包括：单独和整体的资源请求、硬件 / 软件 / 策略限制、 亲和以及反亲和要求、数据局部性、负载间的干扰等等。

### kube-scheduler 中的节点选择[](#kube-scheduler-implementation)

kube-scheduler 给一个 Pod 做调度选择时包含两个步骤：

1.  过滤
2.  打分

过滤阶段会将所有满足 Pod 调度需求的节点选出来。 例如，PodFitsResources 过滤函数会检查候选节点的可用资源能否满足 Pod 的资源请求。 在过滤之后，得出一个节点列表，里面包含了所有可调度节点；通常情况下， 这个节点列表包含不止一个节点。如果这个列表是空的，代表这个 Pod 不可调度。

在打分阶段，调度器会为 Pod 从所有可调度节点中选取一个最合适的节点。 根据当前启用的打分规则，调度器会给每一个可调度节点进行打分。

最后，kube-scheduler 会将 Pod 调度到得分最高的节点上。 如果存在多个得分最高的节点，kube-scheduler 会从中随机选取一个。

支持以下两种方式配置调度器的过滤和打分行为：

1.  [调度策略](https://kubernetes.io/zh-cn/docs/reference/scheduling/policies) 允许你配置过滤所用的 **断言（Predicates）** 和打分所用的 **优先级（Priorities）**。
2.  [调度配置](https://kubernetes.io/zh-cn/docs/reference/scheduling/config/#profiles) 允许你配置实现不同调度阶段的插件， 包括：`QueueSort`、`Filter`、`Score`、`Bind`、`Reserve`、`Permit` 等等。 你也可以配置 kube-scheduler 运行不同的配置文件。

接下来[](#接下来)
-----------

*   阅读关于[调度器性能调优](https://kubernetes.io/zh-cn/docs/concepts/scheduling-eviction/scheduler-perf-tuning/)
*   阅读关于 [Pod 拓扑分布约束](https://kubernetes.io/zh-cn/docs/concepts/scheduling-eviction/topology-spread-constraints/)
*   阅读关于 kube-scheduler 的[参考文档](https://kubernetes.io/zh-cn/docs/reference/command-line-tools-reference/kube-scheduler/)
*   阅读 [kube-scheduler 配置参考 (v1beta3)](https://kubernetes.io/zh-cn/docs/reference/config-api/kube-scheduler-config.v1beta3/)
*   了解关于[配置多个调度器](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/configure-multiple-schedulers/) 的方式
*   了解关于[拓扑结构管理策略](https://kubernetes.io/zh-cn/docs/tasks/administer-cluster/topology-manager/)
*   了解关于 [Pod 开销](https://kubernetes.io/zh-cn/docs/concepts/scheduling-eviction/pod-overhead/)
*   了解关于如何在以下情形使用卷来调度 Pod：
    *   [卷拓扑支持](https://kubernetes.io/zh-cn/docs/concepts/storage/storage-classes/#volume-binding-mode)
    *   [存储容量跟踪](https://kubernetes.io/zh-cn/docs/concepts/storage/storage-capacity/)
    *   [特定于节点的卷数限制](https://kubernetes.io/zh-cn/docs/concepts/storage/storage-limits/)