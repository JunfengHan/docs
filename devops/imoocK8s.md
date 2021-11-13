# 慕课网k8s学习课程

### 2-1 k8s知识体系

![imooc_k8s2_1](/Users/han/Desktop/code/docs/devops/imooc_k8s2_1.png)

项目整体架构：

- 宿主机：Macbook 8 + 16G
- VirtualBox：用来构建虚拟机
- Master: 主虚拟机
- Node1/Node2：节点虚拟机

### 4-2 位什么需要k8s

K8s解决的问题：

- 按需垂直扩容，随时根据需求增减服务区（node）
- 按需水平扩容，容器实例可以轻松扩容，缩容
- 副本控制器，无需担心副本状态
- 服务发现和路由
- 自动部署回滚