# K8s 踩坑集锦

1. kubectl 验证错误

```shell
➜  ~ kubectl get nodes
Unable to connect to the server: x509: certificate signed by unknown authority (possibly because of "crypto/rsa: verification error" while trying to verify candidate authority certificate "kubernetes")
```

原因：取配置文件错误导致

解决方法：指定 KUBECONFIG 默认文件路径

```shell
export KUBECONFIG=/etc/kubernetes/kubelet.conf
```

2. 更新 zsh 后导致 KUBECONFIG 变量被覆盖

> 有时更新 zsh 会出错，导致 .zshrc 配置的变量失效，如：导致 KUBECONFIG 失效，进而出现一系列问题。

```shell
➜  ~ kubectl -n kube-system get configmap
Error from server (Forbidden): configmaps is forbidden: User "system:node:master" cannot list resource "configmaps" in API group "" in the namespace "kube-system": No Object name found
```

包错提示我们，当前的 User 是"system:node:master"，没有权限访问我们要查看的资源。

验证用户是否有权限创建 deployment 资源。

```shell
➜  ~ kubectl auth can-i create deploy
no
```

修改 KUBECONFIG 后再次尝试：

```shell
➜  ~ KUBECONFIG=/etc/kubernetes/admin.conf kubectl auth can-i create deploy
yes
```

成功了。

说明我们在 .zshrc 配置的 KUBECONFIG 失效了。

解决办法：重新加载一下 .zshrc 的配置。

```shell
➜  ~ source .zshrc
```

3. 不要胡乱修改 ECS 的主机名称

- 修改主机名称后，会导致节点无法获取信息;

- 修改主机名后要重启主机
