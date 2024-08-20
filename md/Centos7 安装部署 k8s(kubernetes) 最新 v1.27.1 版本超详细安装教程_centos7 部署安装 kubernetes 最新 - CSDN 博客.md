> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [blog.csdn.net](https://blog.csdn.net/weixin_44084452/article/details/130797232)

k8s 安装
------

### centos7.9 最小安装版本

#### 从零开始的 k8s 安装

##### 硬件配置要求

1.  cpu >= 2 核
2.  硬盘 >= 20G
3.  内存 >= 2G
4.  节点数量建议为奇数（3, 5, 7, 9 等）（1 台好像也能搭，没试过）

以下命令出除特殊要求外，其余都建议在 master 主机执行

##### 本教程配置如下

<table><thead><tr><th>主机名</th><th>IP</th><th>配置</th></tr></thead><tbody><tr><td>master</td><td>192.168.42.150</td><td>2 核 + 2G+20G</td></tr><tr><td>node1</td><td>192.168.42.151</td><td>2 核 + 2G+20G</td></tr><tr><td>node2</td><td>192.168.42.152</td><td>2 核 + 2G+20G</td></tr></tbody></table>

##### 一. 安装（所有机器都要执行）

1.  执行以下命令安装必备插件
    
    ```
    # yum 更新
    sudo yum update -y
    # tab 命令补全
    sudo yum install -y bash-completion
    # wget
    sudo yum install -y wget
    # vim 编辑器
    sudo yum install -y vim-enhanced
    # 网络工具
    sudo yum install -y net-tools
    # gcc 编译器
    sudo yum install -y gcc
    ```
    
2.  将主机名指向[本机 IP](https://so.csdn.net/so/search?q=%E6%9C%AC%E6%9C%BAIP&spm=1001.2101.3001.7020)，**主机名只能包含：字母、数字、-（横杠）、.（点）**
    
    获取主机名
    
    ```
    hostname
    ```
    
    设置主机名
    
    ```
    hostnamectl set-hostname  主机名
    ```
    
3.  将节点加入到 hosts 中
    
    ```
    cat >> /etc/hosts << EOF
    192.168.42.150 master
    192.168.42.151 node1
    192.168.42.152 node2
    EOF
    ```
    
4.  设置[时间同步](https://so.csdn.net/so/search?q=%E6%97%B6%E9%97%B4%E5%90%8C%E6%AD%A5&spm=1001.2101.3001.7020)
    
    ```
    sudo yum -y install ntpdate
    sudo ntpdate ntp1.aliyun.com
    sudo systemctl status ntpdate
    sudo systemctl start ntpdate
    sudo systemctl status ntpdate
    sudo systemctl enable ntpdate
    ```
    
5.  [关闭防火墙](https://so.csdn.net/so/search?q=%E5%85%B3%E9%97%AD%E9%98%B2%E7%81%AB%E5%A2%99&spm=1001.2101.3001.7020)或者开通指定端口（这里使用关闭防火墙）
    
    ```
    sudo systemctl stop firewalld.service 
    sudo systemctl disable firewalld.service
    ```
    
6.  关闭 swap 交换空间
    
    ```
    free -h
    sudo swapoff -a
    sudo sed -i 's/.*swap.*/#&/' /etc/fstab
    free -h
    ```
    
7.  关闭 selinux
    
    ```
    getenforce
    cat /etc/selinux/config
    sudo setenforce 0
    sudo sed -i 's/^SELINUX=enforcing$/SELINUX=permissive/' /etc/selinux/config
    cat /etc/selinux/config
    ```
    
8.  安装 docker , Containerd
    
    ```
    # 删除 docker(如果有的话)
    sudo yum remove docker docker-client docker-client-latest docker-common docker-latest docker-latest-logrotate docker-logrotate docker-engine
    # 安装必备工具
    sudo yum install -y yum-utils device-mapper-persistent-data lvm2
    # 加入 docker 源
    sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo 
    
    # 安装 docker
    sudo yum install -y docker-ce docker-ce-cli containerd.io docker-compose-plugin
    # 安装 containerd
    sudo yum install -y containerd
    
    # 停止 containerd
    sudo systemctl stop containerd.service
    
    # 生成并修改配置文件
    sudo cp /etc/containerd/config.toml /etc/containerd/config.toml.bak
    sudo containerd config default > $HOME/config.toml
    sudo cp $HOME/config.toml /etc/containerd/config.toml
    
    sudo sed -i "s#registry.k8s.io/pause#registry.cn-hangzhou.aliyuncs.com/google_containers/pause#g" /etc/containerd/config.toml
    
    sudo sed -i "s#SystemdCgroup = false#SystemdCgroup = true#g" /etc/containerd/config.toml
    
    # 将 containerd 加入开机自启
    sudo systemctl enable --now containerd.service
    
    # 启动 docker
    sudo systemctl start docker.service
    # 将 docker 加入开机自启
    sudo systemctl enable docker.service
    sudo systemctl enable docker.socket
    sudo systemctl list-unit-files | grep docker
    
    # 设置 docker 镜像加速
    sudo mkdir -p /etc/docker
    # 镜像地址换成你自己的阿里云镜像地址
    sudo tee /etc/docker/daemon.json <<-'EOF'
    {
      "registry-mirrors": ["https://z5d2yy4c.mirror.aliyuncs.com"],
      "exec-opts": ["native.cgroupdriver=systemd"]
    }
    EOF
    
    sudo systemctl daemon-reload
    sudo systemctl restart docker
    sudo docker info
    
    sudo systemctl status docker.service
    sudo systemctl status containerd.service
    ```
    
9.  添加阿里云 k8s 镜像仓库
    
    ```
    cat <<EOF > /etc/yum.repos.d/kubernetes.repo
    [kubernetes]
    name=Kubernetes
    baseurl=https://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-x86_64/
    # 是否开启本仓库
    enabled=1
    # 是否检查 gpg 签名文件
    gpgcheck=0
    # 是否检查 gpg 签名文件
    repo_gpgcheck=0
    gpgkey=https://mirrors.aliyun.com/kubernetes/yum/doc/yum-key.gpg https://mirrors.aliyun.com/kubernetes/yum/doc/rpm-package-key.gpg
    
    EOF
    ```
    
10.  转发 IPv4 并让 iptables 看到桥接流量
    
    ```
    cat >/etc/modules-load.d/k8s.conf <<EOF
    overlay
    br_netfilter
    EOF
    modprobe overlay
    modprobe br_netfilter
    
    cat >/etc/sysctl.d/k8s.conf <<EOF
    net.bridge.bridge-nf-call-iptables=1
    net.bridge.bridge-nf-call-ip6tables=1
    net.ipv4.ip_forward=1
    EOF
    sysctl --system
    
    # 通过运行以下指令确认 br_netfilter 和 overlay 模块被加载
    lsmod | egrep 'overlay|br_netfilter'
    # 通过运行以下指令确认 net.bridge.bridge-nf-call-iptables、net.bridge.bridge-nf-call-ip6tables 系统变量在你的 sysctl 配置中被设置为 1
    sysctl net.bridge.bridge-nf-call-iptables net.bridge.bridge-nf-call-ip6tables net.ipv4.ip_forward
    ```
    
11.  安装 k8s
    
    ```
    # 安装 1.27.1 版本
    sudo yum install -y kubelet-1.27.1-0 kubeadm-1.27.1-0 kubectl-1.27.1-0 --disableexcludes=kubernetes --nogpgcheck
    
    # 安装最新版本（生产环境不建议）
    # sudo yum install -y kubelet kubeadm kubectl --disableexcludes=kubernetes --nogpgcheck
    
    systemctl daemon-reload
    sudo systemctl restart kubelet
    sudo systemctl enable kubelet
    ```
    

##### 二. 启动

1.  master 初始化（仅在 master 节点主机上执行）
    
    ```
    kubeadm init --image-repository=registry.aliyuncs.com/google_containers --apiserver-advertise-address=192.168.42.150 --kubernetes-version=v1.27.1
    
    # --image-repository 					镜像加速地址，一般不动
    # --apiserver-advertise-address  master 节点IP地址，自己改
    # --kubernetes-version 					kubernetes 版本，自己选择的什么版本就改成什么版本
    
    
    # 初始化失败可以使用 kubeadm reset 重置
    # 失败原因多半是因为网络问题，可以换个网络试试
    ```
    
2.  初始化成功后执行（仅在 master 节点主机上执行）
    
    ```
    mkdir -p $HOME/.kube
    sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
    sudo chown $(id -u):$(id -g) $HOME/.kube/config
    ```
    
3.  将 node 加入集群（仅在 node 节点主句执行）**不要直接复制，每个人不一样**
    
    ```
    # 执行成功后，会出现类似下列内容：
    # kubeadm join 192.168.80.60:6443 --token f9lvrz.59mykzssqw6vjh32 \
    # --discovery-token-ca-cert-hash sha256:4e23156e2f71c5df52dfd2b9b198cce5db27c47707564684ea74986836900107
    # 将控制台打印的这句复制到 node 节点主机上执行就行
    
    # 如果忘记或者过期可以使用以下命令重新生成
    # kubeadm token create --print-join-command
    ```
    
4.  查看集群状态（master 节点执行）
    
    ```
    kubectl get nodes
    ```
    
    输出：
    
    ```
    NAME     STATUS   ROLES           AGE     VERSION
    master   NotReady    control-plane   7h21m   v1.27.1
    node1    NotReady    <none>          7h20m   v1.27.1
    node2    NotReady    <none>          7h20m   v1.27.1
    ```
    
    可以看到所有节点都是 NotReady ，这是因为还没有配置网络
    
5.  配置网络（仅在 master 节点执行）
    
    k8s 与 Calico 版本对应
    
    <table><thead><tr><th><strong>Kubernetes 版本</strong></th><th><strong>Calico 版本</strong></th><th><strong>Calico 文档</strong></th><th></th></tr></thead><tbody><tr><td>1.18、1.19、1.20</td><td>3.18</td><td>https://projectcalico.docs.tigera.io/archive/v3.18/getting-started/kubernetes/requirements</td><td>https://projectcalico.docs.tigera.io/archive/v3.18/manifests/calico.yaml</td></tr><tr><td>1.19、1.20、1.21</td><td>3.19</td><td>https://projectcalico.docs.tigera.io/archive/v3.19/getting-started/kubernetes/requirements</td><td>https://projectcalico.docs.tigera.io/archive/v3.19/manifests/calico.yaml</td></tr><tr><td>1.19、1.20、1.21</td><td>3.20</td><td>https://projectcalico.docs.tigera.io/archive/v3.20/getting-started/kubernetes/requirements</td><td>https://projectcalico.docs.tigera.io/archive/v3.20/manifests/calico.yaml</td></tr><tr><td>1.20、1.21、1.22</td><td>3.21</td><td>https://projectcalico.docs.tigera.io/archive/v3.21/getting-started/kubernetes/requirements</td><td>https://projectcalico.docs.tigera.io/archive/v3.21/manifests/calico.yaml</td></tr><tr><td>1.21、1.22、1.23</td><td>3.22</td><td>https://projectcalico.docs.tigera.io/archive/v3.22/getting-started/kubernetes/requirements</td><td>https://projectcalico.docs.tigera.io/archive/v3.22/manifests/calico.yaml</td></tr><tr><td>1.21、1.22、1.23</td><td>3.23</td><td>https://projectcalico.docs.tigera.io/archive/v3.23/getting-started/kubernetes/requirements</td><td>https://projectcalico.docs.tigera.io/archive/v3.23/manifests/calico.yaml</td></tr><tr><td>1.22、1.23、1.24</td><td>3.24</td><td>https://projectcalico.docs.tigera.io/archive/v3.24/getting-started/kubernetes/requirements</td><td>https://projectcalico.docs.tigera.io/archive/v3.24/manifests/calico.yaml</td></tr><tr><td>1.23、1.24、1.25、1.26、1.27</td><td>3.25</td><td>https://projectcalico.docs.tigera.io/archive/v3.25/getting-started/kubernetes/requirements</td><td>https://projectcalico.docs.tigera.io/archive/v3.25/manifests/calico.yaml</td></tr></tbody></table>
    
    或者自己去官网查看版本对应关系
    
    https://docs.tigera.io/calico/latest/getting-started/kubernetes/requirements
    
    下载（网址换成自己版本对应的即可）
    
    ```
    wget --no-check-certificate https://projectcalico.docs.tigera.io/archive/v3.25/manifests/calico.yaml
    ```
    
    下载不了的可以把后面网址复制到浏览器下载下来后在传到虚拟机
    
    修改 calico.yaml 文件
    
    ```
    vim calico.yaml
    ```
    
    ```
    # 在 - name: CLUSTER_TYPE 下方添加如下内容
    - name: CLUSTER_TYPE
      value: "k8s,bgp"
      # 下方为新增内容
    - name: IP_AUTODETECTION_METHOD
      value: "interface=master节点主机的网卡名称"
    ```
    
    配置网络
    
    ```
    kubectl apply -f calico.yaml
    ```
    
6.  再次查看节点信息
    
    查看 node 状态
    
    ```
    kubectl get nodes
    ```
    
    输出：
    
    ```
    NAME     STATUS   ROLES           AGE     VERSION
    master   NotReady    control-plane   21m   v1.27.1
    node1    NotReady    <none>          20m   v1.27.1
    node2    NotReady    <none>          20m   v1.27.1
    ```
    
    查看 pod 状态
    
    ```
    kubectl get pods --all-namespaces -o wide
    ```
    
    输出：
    
    ```
    [root@master ~]# kubectl get pods --all-namespaces -o wide
    NAMESPACE     NAME                                      READY   STATUS     RESTARTS   AGE     IP              NODE            NOMINATED NODE   READINESS GATES
    kube-system   calico-kube-controllers-f79f7749d-rkqgw   0/1     Pending    0          11s     <none>          <none>          <none>           <none>
    kube-system   calico-node-7698p                         0/1     Init:0/3   0          11s     192.168.80.60   k8s             <none>           <none>
    kube-system   calico-node-tvhnb                         0/1     Init:0/3   0          11s     192.168.0.18    centos-7-9-16   <none>           <none>
    kube-system   coredns-c676cc86f-4lncg                   0/1     Pending    0          8m14s   <none>          <none>          <none>           <none>
    kube-system   coredns-c676cc86f-7n9wv                   0/1     Pending    0          8m14s   <none>          <none>          <none>           <none>
    kube-system   etcd-k8s                                  1/1     Running    0          8m21s   192.168.80.60   k8s             <none>           <none>
    kube-system   kube-apiserver-k8s                        1/1     Running    0          8m18s   192.168.80.60   k8s             <none>           <none>
    kube-system   kube-controller-manager-k8s               1/1     Running    0          8m18s   192.168.80.60   k8s             <none>           <none>
    kube-system   kube-proxy-87lx5                          1/1     Running    0          6m16s   192.168.0.18    centos-7-9-16   <none>           <none>
    kube-system   kube-proxy-rctn6                          1/1     Running    0          8m14s   192.168.80.60   k8s             <none>           <none>
    kube-system   kube-scheduler-k8s                        1/1     Running    0          8m18s   192.168.80.60   k8s             <none>           <none>
    [root@k8s ~]#
    ```
    
    可以看到正在初始化，现在稍等一段时间（多久看网络情况）
    
    初始化失败大部分情况也是因为网络原因，建议换个网络试试
    
7.  初始化成功
    
    查看 node 状态
    
    ```
    kubectl get nodes
    ```
    
    输出：
    
    ```
    NAME     STATUS   ROLES           AGE     VERSION
    master   Ready    control-plane   7h21m   v1.27.1
    node1    Ready    <none>          7h20m   v1.27.1
    node2    Ready    <none>          7h20m   v1.27.1
    ```
    
    全部 Ready
    
    查看 pod 状态
    
    ```
    kubectl get pods --all-namespaces -o wide
    ```
    
    输出：
    
    ```
    NAMESPACE     NAME                                       READY   STATUS    RESTARTS      AGE     IP               NODE     NOMINATED NODE   READINESS GATES
    kube-system   calico-kube-controllers-6c99c8747f-92ctj   1/1     Running   1 (64m ago)   31m   172.16.219.69    master   <none>           <none>
    kube-system   calico-node-72n28                          1/1     Running   2 (64m ago)   31m   192.168.42.150   master   <none>           <none>
    kube-system   calico-node-jb2n8                          1/1     Running   1 (64m ago)   31m   192.168.42.152   node2    <none>           <none>
    kube-system   calico-node-m6ndl                          1/1     Running   1 (64m ago)   31m   192.168.42.151   node1    <none>           <none>
    kube-system   coredns-7bdc4cb885-6l9dk                   1/1     Running   1 (64m ago)   33m   172.16.219.70    master   <none>           <none>
    kube-system   coredns-7bdc4cb885-j7qlm                   1/1     Running   1 (64m ago)   33m   172.16.219.68    master   <none>           <none>
    kube-system   etcd-master                                1/1     Running   1 (64m ago)   33m   192.168.42.150   master   <none>           <none>
    kube-system   kube-apiserver-master                      1/1     Running   1 (64m ago)   33m   192.168.42.150   master   <none>           <none>
    kube-system   kube-controller-manager-master             1/1     Running   1 (64m ago)   33m   192.168.42.150   master   <none>           <none>
    kube-system   kube-proxy-558cb                           1/1     Running   1 (64m ago)   33m   192.168.42.150   master   <none>           <none>
    kube-system   kube-proxy-fpk62                           1/1     Running   1 (64m ago)   32m   192.168.42.152   node2    <none>           <none>
    kube-system   kube-proxy-sm4ph                           1/1     Running   1 (64m ago)   32m   192.168.42.151   node1    <none>           <none>
    kube-system   kube-scheduler-master                      1/1     Running   1 (64m ago)   33m   192.168.42.150   master   <none>           <none>
    ```
    
    全部 Ready
    
8.  k8s 命令补全
    
    ```
    ! grep -q kubectl "$HOME/.bashrc" && echo "source /usr/share/bash-completion/bash_completion" >>"$HOME/.bashrc"
    ! grep -q kubectl "$HOME/.bashrc" && echo "source <(kubectl completion bash)" >>"$HOME/.bashrc"
    ! grep -q kubeadm "$HOME/.bashrc" && echo "source <(kubeadm completion bash)" >>"$HOME/.bashrc"
    ! grep -q crictl "$HOME/.bashrc" && echo "source <(crictl completion bash)" >>"$HOME/.bashrc"
    source "$HOME/.bashrc"
    ```
    
9.  常用命令
    
    ```
    # 获取节点
    kubectl get nodes -o wide
    # 实时查询nodes状态
    watch kubectl get nodes -o wide
    # 获取pod
    kubectl get pods --all-namespaces -o wide
    # 查看镜像列表
    kubeadm config images list
    # 节点加入集群
    kubeadm token create --print-join-command
    # 描述node
    kubectl describe node k8s-master
    # 描述pod
    kubectl describe pod kube-flannel-ds-hs8bq --namespace=kube-system
    ```
    

##### 三. 测试

1.  创建一个 nginx 来测试
    
    ```
    kubectl create deployment nginx --image=nginx
    ```
    
2.  查看状态
    
    ```
    kubectl get pods -o wide
    ```
    
    输出：
    
    ```
    NAME                     READY   STATUS    RESTARTS   AGE
    nginx-77b4fdf86c-pfklq   1/1     ContainerCreating   0          72m
    ```
    
    等待一段时间，等 ContainerCreating 变成 Running 时进行下一步
    
3.  暴露端口
    
    ```
    kubectl expose deployment nginx --port=80 --type=NodePort
    ```
    
4.  查看 pos 及服务信息
    
    ```
    kubectl get pod,svc
    ```
    
    输出：
    
    ```
    NAME                         READY   STATUS    RESTARTS   AGE
    pod/nginx-77b4fdf86c-pfklq   1/1     Running   0          76m
    
    NAME                 TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
    service/kubernetes   ClusterIP   10.96.0.1       <none>        443/TCP        7h47m
    service/nginx        NodePort    10.98.106.212   <none>        80:32403/TCP   74m
    ```
    
5.  在浏览器中访问
    
    ```
    http://192.168.42.150:32403
    ```
    
    地址根据你自己的主机变化，端口上面输出的信息中 PORT(S) 这一栏会有
    
    访问成功就说明 k8s 安装部署成功！