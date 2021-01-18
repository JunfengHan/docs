# AWS Lambda入门实战

> 云厂商 Serverless 最早的实践就是 AWS 的 Lambda，可能是目前最成熟的方案了。

## 准备

我们需要在自己的电脑安装：

- Node.js 和 NPM，用来管理Lambda函数和跟踪依赖。
- AWS CLI(Command Line Interface,命令行接口)，用来执行部署

配置AWS：

- IAM(Identify and Access Management,身份认证和管理)用户和角色
- S3 存储桶，用来存储视频等资源
- Lambda函数
- Elastic Transcoder管道，用来对视频编码

### 设置IAM用户和CLI



