> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [kubernetes.io](https://kubernetes.io/zh-cn/docs/tutorials/security/cluster-level-pss/#clean-up)

> Note 本教程仅适用于新集群。

**Information in this document may be out of date**

This document has an older update date than the original, so the information it contains may be out of date. If you're able to read English, see the English version for the most up-to-date information: [Apply Pod Security Standards at the Cluster Level](https://kubernetes.io/docs/tutorials/security/cluster-level-pss/)

Pod 安全是一个准入控制器，当新的 Pod 被创建时，它会根据 Kubernetes [Pod 安全标准](https://kubernetes.io/zh-cn/docs/concepts/security/pod-security-standards/) 进行检查。这是在 v1.25 中达到正式发布（GA）的功能。 本教程将向你展示如何在集群级别实施 `baseline` Pod 安全标准， 该标准将标准配置应用于集群中的所有名字空间。

要将 Pod 安全标准应用于特定名字空间， 请参阅[在名字空间级别应用 Pod 安全标准](https://kubernetes.io/zh-cn/docs/tutorials/security/ns-level-pss)。

如果你正在运行 v1.28 以外的 Kubernetes 版本， 请查阅该版本的文档。

准备开始[](#准备开始)
-------------

在你的工作站中安装以下内容：

*   [KinD](https://kind.sigs.k8s.io/docs/user/quick-start/#installation)
*   [kubectl](https://kubernetes.io/zh-cn/docs/tasks/tools/)

本教程演示了你可以对完全由你控制的 Kubernetes 集群所配置的内容。 如果你正在学习如何为一个无法配置控制平面的托管集群配置 Pod 安全准入， 请参阅[在名字空间级别应用 Pod 安全标准](https://kubernetes.io/zh-cn/docs/tutorials/security/ns-level-pss)。

正确选择要应用的 Pod 安全标准[](#choose-the-right-pod-security-standard-to-apply)
---------------------------------------------------------------------

[Pod 安全准入](https://kubernetes.io/zh-cn/docs/concepts/security/pod-security-admission/) 允许你使用以下模式应用内置的 [Pod 安全标准](https://kubernetes.io/zh-cn/docs/concepts/security/pod-security-standards/)： `enforce`、`audit` 和 `warn`。

要收集信息以便选择最适合你的配置的 Pod 安全标准，请执行以下操作：

1.  创建一个没有应用 Pod 安全标准的集群：
    
    ```
    kind create cluster --name psa-wo-cluster-pss
    
    
    ```
    
    输出类似于：
    
    ```
    Creating cluster "psa-wo-cluster-pss" ...
    ✓ Ensuring node image (kindest/node:v1.28.2) 🖼
    ✓ Preparing nodes 📦
    ✓ Writing configuration 📜
    ✓ Starting control-plane 🕹️
    ✓ Installing CNI 🔌
    ✓ Installing StorageClass 💾
    Set kubectl context to "kind-psa-wo-cluster-pss"
    You can now use your cluster with:
    
    kubectl cluster-info --context kind-psa-wo-cluster-pss
    
    Thanks for using kind! 😊
    
    
    ```
    

2.  将 kubectl 上下文设置为新集群：
    
    ```
    kubectl cluster-info --context kind-psa-wo-cluster-pss
    
    
    ```
    
    输出类似于：
    
    ```
    Kubernetes control plane is running at https://127.0.0.1:61350
    
    CoreDNS is running at https://127.0.0.1:61350/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy
    
    To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.
    
    
    ```
    

3.  获取集群中的名字空间列表：
    
    输出类似于：
    
    ```
    NAME                 STATUS   AGE
    default              Active   9m30s
    kube-node-lease      Active   9m32s
    kube-public          Active   9m32s
    kube-system          Active   9m32s
    local-path-storage   Active   9m26s
    
    
    ```
    

4.  使用 `--dry-run=server` 来了解应用不同的 Pod 安全标准时会发生什么：
    
    1.  Privileged
        
        ```
        kubectl label --dry-run=server --overwrite ns --all \
        pod-security.kubernetes.io/enforce=privileged
        
        
        ```
        
        输出类似于：
        
        ```
        namespace/default labeled
        namespace/kube-node-lease labeled
        namespace/kube-public labeled
        namespace/kube-system labeled
        namespace/local-path-storage labeled
        
        
        ```
        
    2.  Baseline
        
        ```
        kubectl label --dry-run=server --overwrite ns --all \
        pod-security.kubernetes.io/enforce=baseline
        
        
        ```
        
        输出类似于：
        
        ```
        namespace/default labeled
        namespace/kube-node-lease labeled
        namespace/kube-public labeled
        Warning: existing pods in namespace "kube-system" violate the new PodSecurity enforce level "baseline:latest"
        Warning: etcd-psa-wo-cluster-pss-control-plane (and 3 other pods): host namespaces, hostPath volumes
        Warning: kindnet-vzj42: non-default capabilities, host namespaces, hostPath volumes
        Warning: kube-proxy-m6hwf: host namespaces, hostPath volumes, privileged
        namespace/kube-system labeled
        namespace/local-path-storage labeled
        
        
        ```
        
    3.  Restricted
        
        ```
        kubectl label --dry-run=server --overwrite ns --all \
        pod-security.kubernetes.io/enforce=restricted
        
        
        ```
        
        输出类似于：
        
        ```
        namespace/default labeled
        namespace/kube-node-lease labeled
        namespace/kube-public labeled
        Warning: existing pods in namespace "kube-system" violate the new PodSecurity enforce level "restricted:latest"
        Warning: coredns-7bb9c7b568-hsptc (and 1 other pod): unrestricted capabilities, runAsNonRoot != true, seccompProfile
        Warning: etcd-psa-wo-cluster-pss-control-plane (and 3 other pods): host namespaces, hostPath volumes, allowPrivilegeEscalation != false, unrestricted capabilities, restricted volume types, runAsNonRoot != true
        Warning: kindnet-vzj42: non-default capabilities, host namespaces, hostPath volumes, allowPrivilegeEscalation != false, unrestricted capabilities, restricted volume types, runAsNonRoot != true, seccompProfile
        Warning: kube-proxy-m6hwf: host namespaces, hostPath volumes, privileged, allowPrivilegeEscalation != false, unrestricted capabilities, restricted volume types, runAsNonRoot != true, seccompProfile
        namespace/kube-system labeled
        Warning: existing pods in namespace "local-path-storage" violate the new PodSecurity enforce level "restricted:latest"
        Warning: local-path-provisioner-d6d9f7ffc-lw9lh: allowPrivilegeEscalation != false, unrestricted capabilities, runAsNonRoot != true, seccompProfile
        namespace/local-path-storage labeled
        
        
        ```
        

从前面的输出中，你会注意到应用 `privileged` Pod 安全标准不会显示任何名字空间的警告。 然而，`baseline` 和 `restricted` 标准都有警告，特别是在 `kube-system` 名字空间中。

设置模式、版本和标准[](#set-modes-versions-and-standards)
-----------------------------------------------

在本节中，你将以下 Pod 安全标准应用于最新（`latest`）版本：

*   在 `enforce` 模式下的 `baseline` 标准。
*   `warn` 和 `audit` 模式下的 `restricted` 标准。

`baseline` Pod 安全标准提供了一个方便的中间立场，能够保持豁免列表简短并防止已知的特权升级。

此外，为了防止 `kube-system` 中的 Pod 失败，你将免除该名字空间应用 Pod 安全标准。

在你自己的环境中实施 Pod 安全准入时，请考虑以下事项：

1.  根据应用于集群的风险状况，更严格的 Pod 安全标准（如 `restricted`）可能是更好的选择。
    
2.  对 `kube-system` 名字空间进行赦免会允许 Pod 在其中以 `privileged` 模式运行。 对于实际使用，Kubernetes 项目强烈建议你应用严格的 RBAC 策略来限制对 `kube-system` 的访问， 遵循最小特权原则。
    
3.  创建一个配置文件，Pod 安全准入控制器可以使用该文件来实现这些 Pod 安全标准：
    
    ```
    mkdir -p /tmp/pss
    cat <<EOF > /tmp/pss/cluster-level-pss.yaml
    apiVersion: apiserver.config.k8s.io/v1
    kind: AdmissionConfiguration
    plugins:
    - name: PodSecurity
      configuration:
        apiVersion: pod-security.admission.config.k8s.io/v1
        kind: PodSecurityConfiguration
        defaults:
          enforce: "baseline"
          enforce-version: "latest"
          audit: "restricted"
          audit-version: "latest"
          warn: "restricted"
          warn-version: "latest"
        exemptions:
          usernames: []
          runtimeClasses: []
          namespaces: [kube-system]
    EOF
    
    
    ```
    
    **说明：**
    
    `pod-security.admission.config.k8s.io/v1` 配置需要 v1.25+。 对于 v1.23 和 v1.24，使用 [v1beta1](https://v1-24.docs.kubernetes.io/zh-cn/docs/tasks/configure-pod-container/enforce-standards-admission-controller/)。 对于 v1.22，使用 [v1alpha1](https://v1-22.docs.kubernetes.io/docs/tasks/configure-pod-container/enforce-standards-admission-controller/)。
    

4.  在创建集群时配置 API 服务器使用此文件：
    
    ```
    cat <<EOF > /tmp/pss/cluster-config.yaml
    kind: Cluster
    apiVersion: kind.x-k8s.io/v1alpha4
    nodes:
    - role: control-plane
      kubeadmConfigPatches:
      - |
        kind: ClusterConfiguration
        apiServer:
            extraArgs:
              admission-control-config-file: /etc/config/cluster-level-pss.yaml
            extraVolumes:
              - name: accf
                hostPath: /etc/config
                mountPath: /etc/config
                readOnly: false
                pathType: "DirectoryOrCreate"
      extraMounts:
      - hostPath: /tmp/pss
        containerPath: /etc/config
        # optional: if set, the mount is read-only.
        # default false
        readOnly: false
        # optional: if set, the mount needs SELinux relabeling.
        # default false
        selinuxRelabel: false
        # optional: set propagation mode (None, HostToContainer or Bidirectional)
        # see https://kubernetes.io/docs/concepts/storage/volumes/#mount-propagation
        # default None
        propagation: None
    EOF
    
    
    ```
    
    **说明：**
    
    如果你在 macOS 上使用 Docker Desktop 和 KinD， 你可以在菜单项 **Preferences > Resources > File Sharing** 下添加 `/tmp` 作为共享目录。
    

5.  创建一个使用 Pod 安全准入的集群来应用这些 Pod 安全标准：
    
    ```
    kind create cluster --name psa-with-cluster-pss --config /tmp/pss/cluster-config.yaml
    
    
    ```
    
    输出类似于：
    
    ```
    Creating cluster "psa-with-cluster-pss" ...
     ✓ Ensuring node image (kindest/node:v1.28.2) 🖼
     ✓ Preparing nodes 📦
     ✓ Writing configuration 📜
     ✓ Starting control-plane 🕹️
     ✓ Installing CNI 🔌
     ✓ Installing StorageClass 💾
    Set kubectl context to "kind-psa-with-cluster-pss"
    You can now use your cluster with:
    
    kubectl cluster-info --context kind-psa-with-cluster-pss
    
    Have a question, bug, or feature request? Let us know! https://kind.sigs.k8s.io/#community 🙂
    
    
    ```
    

6.  将 kubectl 指向集群
    
    ```
    kubectl cluster-info --context kind-psa-with-cluster-pss
    
    
    ```
    
    输出类似于：
    
    ```
    Kubernetes control plane is running at https://127.0.0.1:63855
    CoreDNS is running at https://127.0.0.1:63855/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy
    
    To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.
    
    
    ```
    

7.  在 default 名字空间下创建一个 Pod：
    
    ```
    cat <<EOF > /tmp/pss/nginx-pod.yaml
    apiVersion: v1
    kind: Pod
    metadata:
      name: nginx
    spec:
      containers:
        - image: nginx
          name: nginx
          ports:
            - containerPort: 80
    EOF
    
    
    ```
    

8.  在集群中创建 Pod：
    
    ```
    kubectl apply -f https://k8s.io/examples/security/example-baseline-pod.yaml
    
    
    ```
    
    这个 Pod 正常启动，但输出包含警告：
    
    ```
    Warning: would violate PodSecurity "restricted:latest": allowPrivilegeEscalation != false (container "nginx" must set securityContext.allowPrivilegeEscalation=false), unrestricted capabilities (container "nginx" must set securityContext.capabilities.drop=["ALL"]), runAsNonRoot != true (pod or container "nginx" must set securityContext.runAsNonRoot=true), seccompProfile (pod or container "nginx" must set securityContext.seccompProfile.type to "RuntimeDefault" or "Localhost")
    pod/nginx created
    
    
    ```
    

清理[](#clean-up)
---------------

现在通过运行以下命令删除你上面创建的集群：

```
kind delete cluster --name psa-with-cluster-pss


```

```
kind delete cluster --name psa-wo-cluster-pss


```

接下来[](#接下来)
-----------

*   运行一个 [shell 脚本](https://kubernetes.io/zh-cn/examples/security/kind-with-cluster-level-baseline-pod-security.sh) 一次执行前面的所有步骤：
    1.  创建一个基于 Pod 安全标准的集群级别配置
    2.  创建一个文件让 API 服务器消费这个配置
    3.  创建一个集群，用这个配置创建一个 API 服务器
    4.  设置 kubectl 上下文为这个新集群
    5.  创建一个最小的 Pod yaml 文件
    6.  应用这个文件，在新集群中创建一个 Pod
*   [Pod 安全准入](https://kubernetes.io/zh-cn/docs/concepts/security/pod-security-admission/)
*   [Pod 安全标准](https://kubernetes.io/zh-cn/docs/concepts/security/pod-security-standards/)
*   [在名字空间级别应用 Pod 安全标准](https://kubernetes.io/zh-cn/docs/tutorials/security/ns-level-pss/)