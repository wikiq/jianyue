> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [kubernetes.io](https://kubernetes.io/zh-cn/docs/concepts/architecture/nodes/)

> Kubernetes 通过将容器放入在节点（Node）上运行的 Pod 中来执行你的工作负载。

Kubernetes 通过将容器放入在节点（Node）上运行的 Pod 中来执行你的[工作负载](https://kubernetes.io/zh-cn/docs/concepts/workloads/)。 节点可以是一个虚拟机或者物理机器，取决于所在的集群配置。 每个节点包含运行 [Pod](https://kubernetes.io/zh-cn/docs/concepts/workloads/pods/) 所需的服务； 这些节点由[控制面](https://kubernetes.io/zh-cn/docs/reference/glossary/?all=true#term-control-plane)负责管理。

通常集群中会有若干个节点；而在一个学习所用或者资源受限的环境中，你的集群中也可能只有一个节点。

节点上的[组件](https://kubernetes.io/zh-cn/docs/concepts/overview/components/#node-components)包括 [kubelet](https://kubernetes.io/docs/reference/generated/kubelet)、 [容器运行时](https://kubernetes.io/zh-cn/docs/setup/production-environment/container-runtimes)以及 [kube-proxy](https://kubernetes.io/zh-cn/docs/reference/command-line-tools-reference/kube-proxy/)。

管理[](#management)
-----------------

向 [API 服务器](https://kubernetes.io/zh-cn/docs/concepts/overview/components/#kube-apiserver)添加节点的方式主要有两种：

1.  节点上的 `kubelet` 向控制面执行自注册；
2.  你（或者别的什么人）手动添加一个 Node 对象。

在你创建了 Node [对象](https://kubernetes.io/zh-cn/docs/concepts/overview/working-with-objects/#kubernetes-objects)或者节点上的 `kubelet` 执行了自注册操作之后，控制面会检查新的 Node 对象是否合法。 例如，如果你尝试使用下面的 JSON 对象来创建 Node 对象：

```
{
  "kind": "Node",
  "apiVersion": "v1",
  "metadata": {
    "name": "10.240.79.157",
    "labels": {
      "name": "my-first-k8s-node"
    }
  }
}


```

Kubernetes 会在内部创建一个 Node 对象作为节点的表示。Kubernetes 检查 `kubelet` 向 API 服务器注册节点时使用的 `metadata.name` 字段是否匹配。 如果节点是健康的（即所有必要的服务都在运行中），则该节点可以用来运行 Pod。 否则，直到该节点变为健康之前，所有的集群活动都会忽略该节点。

**说明：**

Kubernetes 会一直保存着非法节点对应的对象，并持续检查该节点是否已经变得健康。

你，或者某个[控制器](https://kubernetes.io/zh-cn/docs/concepts/architecture/controller/)必须显式地删除该 Node 对象以停止健康检查操作。

Node 对象的名称必须是合法的 [DNS 子域名](https://kubernetes.io/zh-cn/docs/concepts/overview/working-with-objects/names#dns-subdomain-names)。

### 节点名称唯一性[](#node-name-uniqueness)

节点的[名称](https://kubernetes.io/zh-cn/docs/concepts/overview/working-with-objects/names#names)用来标识 Node 对象。 没有两个 Node 可以同时使用相同的名称。 Kubernetes 还假定名字相同的资源是同一个对象。 就 Node 而言，隐式假定使用相同名称的实例会具有相同的状态（例如网络配置、根磁盘内容） 和类似节点标签这类属性。这可能在节点被更改但其名称未变时导致系统状态不一致。 如果某个 Node 需要被替换或者大量变更，需要从 API 服务器移除现有的 Node 对象， 之后再在更新之后重新将其加入。

### 节点自注册[](#self-registration-of-nodes)

当 kubelet 标志 `--register-node` 为 true（默认）时，它会尝试向 API 服务注册自己。 这是首选模式，被绝大多数发行版选用。

对于自注册模式，kubelet 使用下列参数启动：

*   `--kubeconfig` - 用于向 API 服务器执行身份认证所用的凭据的路径。
*   `--cloud-provider` - 与某[云驱动](https://kubernetes.io/zh-cn/docs/reference/glossary/?all=true#term-cloud-provider) 进行通信以读取与自身相关的元数据的方式。
*   `--register-node` - 自动向 API 服务器注册。
*   `--register-with-taints` - 使用所给的[污点](https://kubernetes.io/zh-cn/docs/concepts/scheduling-eviction/taint-and-toleration/)列表 （逗号分隔的 `<key>=<value>:<effect>`）注册节点。当 `register-node` 为 false 时无效。

*   `--node-ip` - 可选的以英文逗号隔开的节点 IP 地址列表。你只能为每个地址簇指定一个地址。 例如在单协议栈 IPv4 集群中，需要将此值设置为 kubelet 应使用的节点 IPv4 地址。 参阅[配置 IPv4/IPv6 双协议栈](https://kubernetes.io/zh-cn/docs/concepts/services-networking/dual-stack/#configure-ipv4-ipv6-dual-stack)了解运行双协议栈集群的详情。
    
    如果你未提供这个参数，kubelet 将使用节点默认的 IPv4 地址（如果有）； 如果节点没有 IPv4 地址，则 kubelet 使用节点的默认 IPv6 地址。
    

*   `--node-labels` - 在集群中注册节点时要添加的[标签](https://kubernetes.io/zh-cn/docs/concepts/overview/working-with-objects/labels/)。 （参见 [NodeRestriction 准入控制插件](https://kubernetes.io/zh-cn/docs/reference/access-authn-authz/admission-controllers/#noderestriction)所实施的标签限制）。
*   `--node-status-update-frequency` - 指定 kubelet 向 API 服务器发送其节点状态的频率。

当 [Node 鉴权模式](https://kubernetes.io/zh-cn/docs/reference/access-authn-authz/node/)和 [NodeRestriction 准入插件](https://kubernetes.io/zh-cn/docs/reference/access-authn-authz/admission-controllers/#noderestriction)被启用后， 仅授权 kubelet 创建 / 修改自己的 Node 资源。

**说明：**

正如[节点名称唯一性](#node-name-uniqueness)一节所述，当 Node 的配置需要被更新时， 一种好的做法是重新向 API 服务器注册该节点。例如，如果 kubelet 重启时其 `--node-labels` 是新的值集，但同一个 Node 名称已经被使用，则所作变更不会起作用， 因为节点标签是在 Node 注册时完成的。

如果在 kubelet 重启期间 Node 配置发生了变化，已经被调度到某 Node 上的 Pod 可能会出现行为不正常或者出现其他问题，例如，已经运行的 Pod 可能通过污点机制设置了与 Node 上新设置的标签相排斥的规则，也有一些其他 Pod， 本来与此 Pod 之间存在不兼容的问题，也会因为新的标签设置而被调到同一节点。 节点重新注册操作可以确保节点上所有 Pod 都被排空并被正确地重新调度。

### 手动节点管理[](#manual-node-administration)

你可以使用 [kubectl](https://kubernetes.io/zh-cn/docs/reference/kubectl/) 来创建和修改 Node 对象。

如果你希望手动创建节点对象时，请设置 kubelet 标志 `--register-node=false`。

你可以修改 Node 对象（忽略 `--register-node` 设置）。 例如，你可以修改节点上的标签或并标记其为不可调度。

你可以结合使用 Node 上的标签和 Pod 上的选择算符来控制调度。 例如，你可以限制某 Pod 只能在符合要求的节点子集上运行。

如果标记节点为不可调度（unschedulable），将阻止新 Pod 调度到该 Node 之上， 但不会影响任何已经在其上的 Pod。 这是重启节点或者执行其他维护操作之前的一个有用的准备步骤。

要标记一个 Node 为不可调度，执行以下命令：

更多细节参考[安全地腾空节点](https://kubernetes.io/zh-cn/docs/tasks/administer-cluster/safely-drain-node/)。

**说明：**

被 [DaemonSet](https://kubernetes.io/zh-cn/docs/concepts/workloads/controllers/daemonset/) 控制器创建的 Pod 能够容忍节点的不可调度属性。 DaemonSet 通常提供节点本地的服务，即使节点上的负载应用已经被腾空， 这些服务也仍需运行在节点之上。

节点状态[](#node-status)
--------------------

一个节点的状态包含以下信息:

*   [地址（Addresses）](https://kubernetes.io/zh-cn/docs/reference/node/node-status/#addresses)
*   [状况（Condition）](https://kubernetes.io/zh-cn/docs/reference/node/node-status/#condition)
*   [容量与可分配（Capacity）](https://kubernetes.io/zh-cn/docs/reference/node/node-status/#capacity)
*   [信息（Info）](https://kubernetes.io/zh-cn/docs/reference/node/node-status/#info)

你可以使用 `kubectl` 来查看节点状态和其他细节信息：

```
kubectl describe node <节点名称>


```

更多细节参见 [Node Status](https://kubernetes.io/zh-cn/docs/reference/node/node-status)。

节点心跳[](#node-heartbeats)
------------------------

Kubernetes 节点发送的心跳帮助你的集群确定每个节点的可用性，并在检测到故障时采取行动。

对于节点，有两种形式的心跳:

*   更新节点的 [`.status`](https://kubernetes.io/zh-cn/docs/reference/node/node-status/)
*   `kube-node-lease` [名字空间](https://kubernetes.io/zh-cn/docs/concepts/overview/working-with-objects/namespaces/)中的 [Lease（租约）](https://kubernetes.io/zh-cn/docs/concepts/architecture/leases/)对象。 每个节点都有一个关联的 Lease 对象。

节点控制器[](#node-controller)
-------------------------

节点[控制器](https://kubernetes.io/zh-cn/docs/concepts/architecture/controller/)是 Kubernetes 控制面组件， 管理节点的方方面面。

节点控制器在节点的生命周期中扮演多个角色。 第一个是当节点注册时为它分配一个 CIDR 区段（如果启用了 CIDR 分配）。

第二个是保持节点控制器内的节点列表与云服务商所提供的可用机器列表同步。 如果在云环境下运行，只要某节点不健康，节点控制器就会询问云服务是否节点的虚拟机仍可用。 如果不可用，节点控制器会将该节点从它的节点列表删除。

第三个是监控节点的健康状况。节点控制器负责：

*   在节点不可达的情况下，在 Node 的 `.status` 中更新 `Ready` 状况。 在这种情况下，节点控制器将 NodeReady 状况更新为 `Unknown`。
*   如果节点仍然无法访问：对于不可达节点上的所有 Pod 触发 [API 发起的逐出](https://kubernetes.io/zh-cn/docs/concepts/scheduling-eviction/api-eviction/)操作。 默认情况下，节点控制器在将节点标记为 `Unknown` 后等待 5 分钟提交第一个驱逐请求。

默认情况下，节点控制器每 5 秒检查一次节点状态，可以使用 `kube-controller-manager` 组件上的 `--node-monitor-period` 参数来配置周期。

### 逐出速率限制[](#rate-limits-on-eviction)

大部分情况下，节点控制器把逐出速率限制在每秒 `--node-eviction-rate` 个（默认为 0.1）。 这表示它每 10 秒钟内至多从一个节点驱逐 Pod。

当一个可用区域（Availability Zone）中的节点变为不健康时，节点的驱逐行为将发生改变。 节点控制器会同时检查可用区域中不健康（`Ready` 状况为 `Unknown` 或 `False`） 的节点的百分比：

*   如果不健康节点的比例超过 `--unhealthy-zone-threshold` （默认为 0.55）， 驱逐速率将会降低。
*   如果集群较小（意即小于等于 `--large-cluster-size-threshold` 个节点 - 默认为 50）， 驱逐操作将会停止。
*   否则驱逐速率将降为每秒 `--secondary-node-eviction-rate` 个（默认为 0.01）。

在逐个可用区域中实施这些策略的原因是， 当一个可用区域可能从控制面脱离时其它可用区域可能仍然保持连接。 如果你的集群没有跨越云服务商的多个可用区域，那（整个集群）就只有一个可用区域。

跨多个可用区域部署你的节点的一个关键原因是当某个可用区域整体出现故障时， 工作负载可以转移到健康的可用区域。 因此，如果一个可用区域中的所有节点都不健康时，节点控制器会以正常的速率 `--node-eviction-rate` 进行驱逐操作。 在所有的可用区域都不健康（也即集群中没有健康节点）的极端情况下， 节点控制器将假设控制面与节点间的连接出了某些问题，它将停止所有驱逐动作 （如果故障后部分节点重新连接，节点控制器会从剩下不健康或者不可达节点中驱逐 Pod）。

节点控制器还负责驱逐运行在拥有 `NoExecute` 污点的节点上的 Pod， 除非这些 Pod 能够容忍此污点。 节点控制器还负责根据节点故障（例如节点不可访问或没有就绪） 为其添加[污点](https://kubernetes.io/zh-cn/docs/concepts/scheduling-eviction/taint-and-toleration/)。 这意味着调度器不会将 Pod 调度到不健康的节点上。

### 资源容量跟踪[](#node-capacity)

Node 对象会跟踪节点上资源的容量（例如可用内存和 CPU 数量）。 通过[自注册](#self-registration-of-nodes)机制生成的 Node 对象会在注册期间报告自身容量。 如果你[手动](#manual-node-administration)添加了 Node， 你就需要在添加节点时手动设置节点容量。

Kubernetes [调度器](https://kubernetes.io/zh-cn/docs/reference/command-line-tools-reference/kube-scheduler/) 保证节点上有足够的资源供其上的所有 Pod 使用。 它会检查节点上所有容器的请求的总和不会超过节点的容量。 总的请求包括由 kubelet 启动的所有容器，但不包括由容器运行时直接启动的容器， 也不包括不受 `kubelet` 控制的其他进程。

节点拓扑[](#node-topology)
----------------------

**特性状态：** `Kubernetes v1.18 [beta]`

如果启用了 `TopologyManager` [特性门控](https://kubernetes.io/zh-cn/docs/reference/command-line-tools-reference/feature-gates/)， `kubelet` 可以在作出资源分配决策时使用拓扑提示。 参考[控制节点上拓扑管理策略](https://kubernetes.io/zh-cn/docs/tasks/administer-cluster/topology-manager/)了解详细信息。

节点体面关闭[](#graceful-node-shutdown)
---------------------------------

**特性状态：** `Kubernetes v1.21 [beta]`

kubelet 会尝试检测节点系统关闭事件并终止在节点上运行的所有 Pod。

在节点终止期间，kubelet 保证 Pod 遵从常规的 [Pod 终止流程](https://kubernetes.io/zh-cn/docs/concepts/workloads/pods/pod-lifecycle/#pod-termination)， 且不接受新的 Pod（即使这些 Pod 已经绑定到该节点）。

节点体面关闭特性依赖于 systemd，因为它要利用 [systemd 抑制器锁](https://www.freedesktop.org/wiki/Software/systemd/inhibit/)机制， 在给定的期限内延迟节点关闭。

节点体面关闭特性受 `GracefulNodeShutdown` [特性门控](https://kubernetes.io/zh-cn/docs/reference/command-line-tools-reference/feature-gates/)控制， 在 1.21 版本中是默认启用的。

注意，默认情况下，下面描述的两个配置选项，`shutdownGracePeriod` 和 `shutdownGracePeriodCriticalPods` 都是被设置为 0 的，因此不会激活节点体面关闭功能。 要激活此功能特性，这两个 kubelet 配置选项要适当配置，并设置为非零值。

一旦 systemd 检测到或通知节点关闭，kubelet 就会在节点上设置一个 `NotReady` 状况，并将 `reason` 设置为 `"node is shutting down"`。 kube-scheduler 会重视此状况，不将 Pod 调度到受影响的节点上； 其他第三方调度程序也应当遵循相同的逻辑。这意味着新的 Pod 不会被调度到该节点上， 因此不会有新 Pod 启动。

如果检测到节点关闭过程正在进行中，kubelet **也会**在 `PodAdmission` 阶段拒绝 Pod，即使是该 Pod 带有 `node.kubernetes.io/not-ready:NoSchedule` 的[容忍度](https://kubernetes.io/zh-cn/docs/concepts/scheduling-eviction/taint-and-toleration/)。

同时，当 kubelet 通过 API 在其 Node 上设置该状况时，kubelet 也开始终止在本地运行的所有 Pod。

在体面关闭节点过程中，kubelet 分两个阶段来终止 Pod：

1.  终止在节点上运行的常规 Pod。
2.  终止在节点上运行的[关键 Pod](https://kubernetes.io/zh-cn/docs/tasks/administer-cluster/guaranteed-scheduling-critical-addon-pods/#marking-pod-as-critical)。

节点体面关闭的特性对应两个 [`KubeletConfiguration`](https://kubernetes.io/zh-cn/docs/tasks/administer-cluster/kubelet-config-file/) 选项：

*   `shutdownGracePeriod`：
    *   指定节点应延迟关闭的总持续时间。此时间是 Pod 体面终止的时间总和，不区分常规 Pod 还是[关键 Pod](https://kubernetes.io/zh-cn/docs/tasks/administer-cluster/guaranteed-scheduling-critical-addon-pods/#marking-pod-as-critical)。
*   `shutdownGracePeriodCriticalPods`：
    *   在节点关闭期间指定用于终止[关键 Pod](https://kubernetes.io/zh-cn/docs/tasks/administer-cluster/guaranteed-scheduling-critical-addon-pods/#marking-pod-as-critical) 的持续时间。该值应小于 `shutdownGracePeriod`。

**说明：**

在某些情况下，节点终止过程会被系统取消（或者可能由管理员手动取消）。 无论哪种情况下，节点都将返回到 `Ready` 状态。然而，已经开始终止进程的 Pod 将不会被 kubelet 恢复，需要被重新调度。

例如，如果设置了 `shutdownGracePeriod=30s` 和 `shutdownGracePeriodCriticalPods=10s`， 则 kubelet 将延迟 30 秒关闭节点。 在关闭期间，将保留前 20（30 - 10）秒用于体面终止常规 Pod， 而保留最后 10 秒用于终止[关键 Pod](https://kubernetes.io/zh-cn/docs/tasks/administer-cluster/guaranteed-scheduling-critical-addon-pods/#marking-pod-as-critical)。

**说明：**

当 Pod 在正常节点关闭期间被驱逐时，它们会被标记为关闭。 运行 `kubectl get pods` 时，被驱逐的 Pod 的状态显示为 `Terminated`。 并且 `kubectl describe pod` 表示 Pod 因节点关闭而被驱逐：

```
Reason:         Terminated
Message:        Pod was terminated in response to imminent node shutdown.


```

### 基于 Pod 优先级的节点体面关闭[](#pod-priority-graceful-node-shutdown)

**特性状态：** `Kubernetes v1.24 [beta]`

为了在节点体面关闭期间提供更多的灵活性，尤其是处理关闭期间的 Pod 排序问题， 节点体面关闭机制能够关注 Pod 的 PriorityClass 设置，前提是你已经在集群中启用了此功能特性。 此功能特性允许集群管理员基于 Pod 的[优先级类（Priority Class）](https://kubernetes.io/zh-cn/docs/concepts/scheduling-eviction/pod-priority-preemption/#priorityclass) 显式地定义节点体面关闭期间 Pod 的处理顺序。

前文所述的[节点体面关闭](#graceful-node-shutdown)特性能够分两个阶段关闭 Pod， 首先关闭的是非关键的 Pod，之后再处理关键 Pod。 如果需要显式地以更细粒度定义关闭期间 Pod 的处理顺序，需要一定的灵活度， 这时可以使用基于 Pod 优先级的体面关闭机制。

当节点体面关闭能够处理 Pod 优先级时，节点体面关闭的处理可以分为多个阶段， 每个阶段关闭特定优先级类的 Pod。kubelet 可以被配置为按确切的阶段处理 Pod， 且每个阶段可以独立设置关闭时间。

假设集群中存在以下自定义的 Pod [优先级类](https://kubernetes.io/zh-cn/docs/concepts/scheduling-eviction/pod-priority-preemption/#priorityclass)。

<table><thead><tr><th>Pod 优先级类名称</th><th>Pod 优先级类数值</th></tr></thead><tbody><tr><td><code>custom-class-a</code></td><td>100000</td></tr><tr><td><code>custom-class-b</code></td><td>10000</td></tr><tr><td><code>custom-class-c</code></td><td>1000</td></tr><tr><td><code>regular/unset</code></td><td>0</td></tr></tbody></table>

在 [kubelet 配置](https://kubernetes.io/zh-cn/docs/reference/config-api/kubelet-config.v1beta1/)中， `shutdownGracePeriodByPodPriority` 可能看起来是这样：

<table><thead><tr><th>Pod 优先级类数值</th><th>关闭期限</th></tr></thead><tbody><tr><td>100000</td><td>10 秒</td></tr><tr><td>10000</td><td>180 秒</td></tr><tr><td>1000</td><td>120 秒</td></tr><tr><td>0</td><td>60 秒</td></tr></tbody></table>

对应的 kubelet 配置 YAML 将会是：

```
shutdownGracePeriodByPodPriority:
  - priority: 100000
    shutdownGracePeriodSeconds: 10
  - priority: 10000
    shutdownGracePeriodSeconds: 180
  - priority: 1000
    shutdownGracePeriodSeconds: 120
  - priority: 0
    shutdownGracePeriodSeconds: 60


```

上面的表格表明，所有 `priority` 值大于等于 100000 的 Pod 会得到 10 秒钟期限停止， 所有 `priority` 值介于 10000 和 100000 之间的 Pod 会得到 180 秒钟期限停止， 所有 `priority` 值介于 1000 和 10000 之间的 Pod 会得到 120 秒钟期限停止， 所有其他 Pod 将获得 60 秒的时间停止。

用户不需要为所有的优先级类都设置数值。例如，你也可以使用下面这种配置：

<table><thead><tr><th>Pod 优先级类数值</th><th>关闭期限</th></tr></thead><tbody><tr><td>100000</td><td>300 秒</td></tr><tr><td>1000</td><td>120 秒</td></tr><tr><td>0</td><td>60 秒</td></tr></tbody></table>

在上面这个场景中，优先级类为 `custom-class-b` 的 Pod 会与优先级类为 `custom-class-c` 的 Pod 在关闭时按相同期限处理。

如果在特定的范围内不存在 Pod，则 kubelet 不会等待对应优先级范围的 Pod。 kubelet 会直接跳到下一个优先级数值范围进行处理。

如果此功能特性被启用，但没有提供配置数据，则不会出现排序操作。

使用此功能特性需要启用 `GracefulNodeShutdownBasedOnPodPriority` [特性门控](https://kubernetes.io/zh-cn/docs/reference/command-line-tools-reference/feature-gates/)， 并将 [kubelet 配置](https://kubernetes.io/zh-cn/docs/reference/config-api/kubelet-config.v1beta1/) 中的 `shutdownGracePeriodByPodPriority` 设置为期望的配置， 其中包含 Pod 的优先级类数值以及对应的关闭期限。

**说明：**

在节点体面关闭期间考虑 Pod 优先级的能力是作为 Kubernetes v1.23 中的 Alpha 功能引入的。 在 Kubernetes 1.28 中该功能是 Beta 版，默认启用。

kubelet 子系统中会生成 `graceful_shutdown_start_time_seconds` 和 `graceful_shutdown_end_time_seconds` 度量指标以便监视节点关闭行为。

处理节点非体面关闭[](#non-graceful-node-shutdown)
----------------------------------------

**特性状态：** `Kubernetes v1.28 [stable]`

节点关闭的操作可能无法被 kubelet 的节点关闭管理器检测到， 是因为该命令不会触发 kubelet 所使用的抑制锁定机制，或者是因为用户错误的原因， 即 ShutdownGracePeriod 和 ShutdownGracePeriodCriticalPod 配置不正确。 请参考以上[节点体面关闭](#graceful-node-shutdown)部分了解更多详细信息。

当某节点关闭但 kubelet 的节点关闭管理器未检测到这一事件时， 在那个已关闭节点上、属于 [StatefulSet](https://kubernetes.io/zh-cn/docs/concepts/workloads/controllers/statefulset/) 的 Pod 将停滞于终止状态，并且不能移动到新的运行节点上。 这是因为已关闭节点上的 kubelet 已不存在，亦无法删除 Pod， 因此 StatefulSet 无法创建同名的新 Pod。 如果 Pod 使用了卷，则 VolumeAttachments 不会从原来的已关闭节点上删除， 因此这些 Pod 所使用的卷也无法挂接到新的运行节点上。 所以，那些以 StatefulSet 形式运行的应用无法正常工作。 如果原来的已关闭节点被恢复，kubelet 将删除 Pod，新的 Pod 将被在不同的运行节点上创建。 如果原来的已关闭节点没有被恢复，那些在已关闭节点上的 Pod 将永远滞留在终止状态。

为了缓解上述情况，用户可以手动将具有 `NoExecute` 或 `NoSchedule` 效果的 `node.kubernetes.io/out-of-service` 污点添加到节点上，标记其无法提供服务。 如果在 [kube-controller-manager](https://kubernetes.io/zh-cn/docs/reference/command-line-tools-reference/kube-controller-manager/) 上启用了 `NodeOutOfServiceVolumeDetach` [特性门控](https://kubernetes.io/zh-cn/docs/reference/command-line-tools-reference/feature-gates/)， 并且节点被通过污点标记为无法提供服务，如果节点 Pod 上没有设置对应的容忍度， 那么这样的 Pod 将被强制删除，并且该在节点上被终止的 Pod 将立即进行卷分离操作。 这样就允许那些在无法提供服务节点上的 Pod 能在其他节点上快速恢复。

在非体面关闭期间，Pod 分两个阶段终止：

1.  强制删除没有匹配的 `out-of-service` 容忍度的 Pod。
2.  立即对此类 Pod 执行分离卷操作。

**说明：**

*   在添加 `node.kubernetes.io/out-of-service` 污点之前， 应该验证节点已经处于关闭或断电状态（而不是在重新启动中）。
*   将 Pod 移动到新节点后，用户需要手动移除停止服务的污点， 并且用户要检查关闭节点是否已恢复，因为该用户是最初添加污点的用户。

交换内存管理[](#swap-memory)
----------------------

**特性状态：** `Kubernetes v1.28 [beta]`

要在节点上启用交换内存，必须启用 kubelet 的 `NodeSwap` 特性门控， 同时使用 `--fail-swap-on` 命令行参数或者将 `failSwapOn` [配置](https://kubernetes.io/zh-cn/docs/reference/config-api/kubelet-config.v1beta1/)设置为 false。

**警告：**

当内存交换功能被启用后，Kubernetes 数据（如写入 tmpfs 的 Secret 对象的内容）可以被交换到磁盘。

用户还可以选择配置 `memorySwap.swapBehavior` 以指定节点使用交换内存的方式。例如:

```
memorySwap:
  swapBehavior: UnlimitedSwap


```

*   `UnlimitedSwap`（默认）：Kubernetes 工作负载可以根据请求使用尽可能多的交换内存， 一直到达到系统限制为止。
*   `LimitedSwap`：Kubernetes 工作负载对交换内存的使用受到限制。 只有具有 Burstable QoS 的 Pod 可以使用交换空间。

如果启用了特性门控但是未指定 `memorySwap` 的配置，默认情况下 kubelet 将使用与 `UnlimitedSwap` 设置相同的行为。

采用 `LimitedSwap` 时，不属于 Burstable QoS 分类的 Pod （即 `BestEffort`/`Guaranteed` QoS Pod） 被禁止使用交换内存。为了保持上述的安全性和节点健康性保证， 在 `LimitedSwap` 生效时，不允许这些 Pod 使用交换内存。

在详细介绍交换限制的计算之前，有必要定义以下术语：

*   `nodeTotalMemory`：节点上可用的物理内存总量。
*   `totalPodsSwapAvailable`：节点上可供 Pod 使用的交换内存总量 （一些交换内存可能被保留由系统使用）。
*   `containerMemoryRequest`：容器的内存请求。

交换限制被配置为 `(containerMemoryRequest / nodeTotalMemory) * totalPodsSwapAvailable` 的值。

需要注意的是，位于 Burstable QoS Pod 中的容器可以通过将内存请求设置为与内存限制相同来选择不使用交换空间。 以这种方式配置的容器将无法访问交换内存。

只有 **cgroup v2** 支持交换空间，cgroup v1 不支持。

如需了解更多信息、协助测试和提交反馈，请参阅关于 [Kubernetes 1.28：NodeSwap 进阶至 Beta1](https://kubernetes.io/zh-cn/blog/2023/08/24/swap-linux-beta/) 的博客文章、 [KEP-2400](https://github.com/kubernetes/enhancements/issues/4128) 及其[设计提案](https://github.com/kubernetes/enhancements/blob/master/keps/sig-node/2400-node-swap/README.md)。

接下来[](#接下来)
-----------

进一步了解以下资料：

*   构成节点的[组件](https://kubernetes.io/zh-cn/docs/concepts/overview/components/#node-components)。
*   [Node 的 API 定义](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.28/#node-v1-core)。
*   架构设计文档中有关 [Node](https://git.k8s.io/design-proposals-archive/architecture/architecture.md#the-kubernetes-node) 的章节。
*   [污点和容忍度](https://kubernetes.io/zh-cn/docs/concepts/scheduling-eviction/taint-and-toleration/)。
*   [节点资源管理器](https://kubernetes.io/zh-cn/docs/concepts/policy/node-resource-managers/)。
*   [Windows 节点的资源管理](https://kubernetes.io/zh-cn/docs/concepts/configuration/windows-resource-management/)。