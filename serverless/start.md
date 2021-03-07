# Serverless computing(无服务器计算)

> Serverless 为什么翻译成无服务器？不应该是少服务器吗？

**无服务器运算**（Serverless computing），又被称为功能即服务（Function-as-a-Service，缩写为 FaaS），是云计算的一种**模型**。

- 无服务器做了什么？

  <span style="color: #ff0000; font-size: 16px;">以平台即服务（PaaS）为基础，无服务器运算提供一个微型的架构</span>

- 好处是什么？

  <span style="color: #ff0000; font-size: 16px;">1.终端客户不需要部署、配置或管理服务器服务</span>
  <span style="color: #ff0000; font-size: 16px;">2.服务器服务按流量算钱</span>

- 是如何将终端客户从服务器中解放的？

  <span style="color: #ff0000; font-size: 16px;">代码运行所需要的服务器服务皆由云端平台来提供</span>

- 缺点也显而易见：
  应用严重依赖于特定的云平台、第三方服务

## 1.服务器发展历程：虚拟与隔离

> 开发人员为了保证开发环境的正确（即，这个 Bug 不是环境因素造成的），想出了一系列的隔离方式：虚拟机、容器虚拟化、语言虚拟机、应用容器（如 Java 的 Tomcat）、虚拟环境（如 Python 中的 virtualenv），甚至是独立于语言的 DSL。

![server growth](../_media/server_growth.jpg)

- 我们使用 XEN、KVM 等虚拟化技术，隔离了硬件以及运行在这之上的操作系统
- 我们使用云计算进一步地自动管理这些虚拟化的资源
- 我们使用 Docker 等容器技术，隔离了应用的操作系统与服务器的操作系统
- 有了 Serverless，我们可以隔离操作系统，乃至更底层的技术细节

## 2.云原生

> 云原生是什么？

**FaaS**（Functions as a Service）则是函数即服务，作为无服务器计算的一种形式，当前使用最广泛的当属 AWS 的 Lambada。

究竟该如何理解“函数即服务”的概念？

大致上是开发者先**将函数定义封装在容器中，通过调用函数来实现调用后端存储等服务**。

本质上，<span style="color: #ff0000; font-size: 16px;">FaaS 是一种事件驱动的由消息触发的服务。</span>

在 FaaS 环境中，用户将函数功能代码上传到 FaaS 提供商，其中对的水平扩展是完全自动弹性的。

目前，函数即服务（Function as a Service，FaaS）是当下 Serverless 实现的技术基础。

## 3.Serverless 框架

> Serverless 框架 是云计算模型的具体实现，我们使用 Serverless 框架来使用云计算公司的 Serverless 服务。

各大云计算公司的 Serverless 框架：

- AWS Lambda，最早被大众所认可的 Serverless 实现
- Knative，谷歌开源的 Serverless 架构方案
- Azure Functions，来自微软公有云的 Serverless 实现
- OpenWhisk，Apache 社区的开源 Serverless 框架
- Kubeless，基于 Kubernetes 架构实现的开源 Serverless 框架
- Fission，Platform9 推出的开源 Serverless 框架
- OpenFaaS，以容器技术为核心的开源 Serverless 框架
- Fn，来自 Oracle 的开源 Serverless 框架
- 阿里云函数计算
- 无服务器云函数（Serverless Cloud Function，SCF）是腾讯云推出的函数式计算平台

--- 未完待续... ---

## 参考

[函数计算入门-HelloWorld 应用开发](https://developer.aliyun.com/adc/scenario/20c4bead7e0b4758a6ab857bce793187?spm=5176.137990.J_8779010650.1.24c76320n6OALj&accounttraceid=31e3aa120a3b425db19b35140de87df3xikn)
