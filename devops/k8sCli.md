# Kubernetes 命令

> K8s 的命令行工具是 kubectl，它可以对我们的**集群进行管理**。

老规矩，任何不熟悉的命令行工具，都应该使用 `--help` 查看一下帮助信息：(里面的注释是我添加的，便于理解)

```bash
[root@master ~]# kubectl --help
### 告诉我们 kubectl是干什么的 -- 管理 k8s集群
kubectl controls the Kubernetes cluster manager.

 Find more information at: https://kubernetes.io/docs/reference/kubectl/overview/

Basic Commands (Beginner):
  create        Create a resource from a file or from stdin
  expose        Take a replication controller, service, deployment or pod and expose it as a new Kubernetes service
  run           在集群中运行一个指定的镜像
  set           为 objects 设置一个指定的特征

Basic Commands (Intermediate):
  explain       Get documentation for a resource
  get           显示一个或更多 resources
  edit          在服务器上编辑一个资源
  delete        Delete resources by file names, stdin, resources and names, or by resources and label selector

Deploy Commands:
  rollout       Manage the rollout of a resource
  scale         Set a new size for a deployment, replica set, or replication controller
  autoscale     Auto-scale a deployment, replica set, stateful set, or replication controller

Cluster Management Commands:
  certificate   修改 certificate 资源.
  cluster-info  Display cluster information
  top           Display resource (CPU/memory) usage
  cordon        标记 node 为 unschedulable
  uncordon      标记 node 为 schedulable
  drain         Drain node in preparation for maintenance
  taint         更新一个或者多个 node 上的 taints

Troubleshooting and Debugging Commands:
  describe      显示一个指定 resource 或者 group 的 resources 详情
  logs          输出容器在 pod 中的日志
  attach        Attach 到一个运行中的 container
  exec          在一个 container 中执行一个命令
  port-forward  Forward one or more local ports to a pod
  proxy         运行一个 proxy 到 Kubernetes API server
  cp            Copy files and directories to and from containers
  auth          Inspect authorization
  debug         Create debugging sessions for troubleshooting workloads and nodes

Advanced Commands:
  diff          Diff the live version against a would-be applied version
  apply         Apply a configuration to a resource by file name or stdin
  							### 通过文件名或stdin将配置应用于一个资源
  							### 我们可以使用这个命令，直接将一个 yaml 文件部署到集群
  patch         Update fields of a resource
  replace       Replace a resource by file name or stdin
  wait          Experimental: Wait for a specific condition on one or many resources
  kustomize     Build a kustomization target from a directory or URL.

Settings Commands:
  label         更新在这个资源上的 labels
  annotate      更新一个资源的注解
  completion    Output shell completion code for the specified shell (bash or zsh)

Other Commands:
  api-resources Print the supported API resources on the server
  api-versions  Print the supported API versions on the server, in the form of "group/version"
  config        修改 kubeconfig 文件
  plugin        Provides utilities for interacting with plugins
  version       输出 client 和 server 的版本信息

Usage:
  kubectl [flags] [options]

### 很重要‼️啊，直接查看某个命令的具体信息，不用到处找命令了
Use "kubectl <command> --help" for more information about a given command.
Use "kubectl options" for a list of global command-line options (applies to all commands).
```

## 1. 基础初始化命令集合

> Basic Commands (Beginner)：

- create 创建资源
- expose 将资源作为 k8s 服务导出
- run 在集群中运行一个指定的镜像
- set

## 2. 基础使用命令集合

> Basic Commands (Intermediate):

- explain Get documentation for a resource
- get 显示一个或更多 resources
- edit 在服务器上编辑一个资源
- delete Delete resources

例如：

```shell
# 查看集群所有节点
kubectl get nodes
# 查看某个节点
kubectl get nodes node1

# 查看集群所有pod
kubectl get pods
```

## 2. 查看资源信息--get

```bash
# 获取一个活多个资源的概要信息
kubectl get [TYPE(资源类型如：node/service/pod/deployment等) -l label] [--watch 监听] [--sort-by 排序]
		# 查看*集群*所有节点
		kubectl get nodes
		# 查看*kube-system*命名空间下所有 service
		kubectl get service -n kube-system
		# 查看*kubernetes-dashboard*命名空间下所有 service
		kubectl get service -n kubernetes-dashboard
```

## 3. 删除资源--delete

> 如果某个 pod 安装错了 ❌，可以把它的 deployment、pod 和 service 都删除，然后重新安装。

```bash
# 删除某个deployment
kubectl delete deployment DEPLOYMENT_NAME -n NAMESPACE_NAME
```

## 4. 常用命令

- **节点（node）相关：**

```shell
# 在Master 查看所有节点
kubectl get nodes

# 在Master 查看某个节点的状态
kubectl describe node [NodeName]

# 查看当前主机节点的 kubelet 状态
systemctl status kubelet

# 给node节点添加标签
kubectl label nodes [NODENAME] [LABEL_KEY=VALUE]

```

- **Pod 相关：**

> 查看：

```shell
# 查看默认命名空间下的 pod
kubectl get pods

# 查看所有命名空间下的 pod
kubectl get pods --all-namespace
kubectl get pods -A

# 查看所有命名空间的某个pod
kubectl get pods --all-namespaces -o wide | grep [NODENAME]
```

> 新增：

```shell
# 根据yaml文件创建pod
kubectl apply -f [XXX.yaml]
  # -f 是 --filename=[] 简写

# 如何将pod创建到具体的node，而不是由k8s来随机创建？
# 使用 spec/affinity 配置项，在 yaml文件中设置相应node的label即可。

#
```
