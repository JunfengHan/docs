# Helm 常见命令

## 1. 仓库

> Helm 没有默认仓库，需要我们手动添加。仓库可以用于下载package.

- 添加仓库

  ```shell
  helm repo add [repoName] [url]
  # 添加一个名为 bitnami 的仓库
  helm repo add bitnami https://charts.bitnami.com/bitnami
  ```

- 查看所有仓库

  ```shell
  helm repo list
  ```

- 在仓库中查找 package

  ```shell
  helm search repo [packageName]
  # 在已有仓库中查找 mongodb 的 package
  helm search repo mongodb
  ```

## 2. Chart(图表)

- 搜索图表

  ```shell
  # 在helm hub中搜索
  helm search hub [chartname]
  helm search hub wordpress # 搜索wordpress
  
  # 在已经添加的仓库中搜索
  helm search repo wordpress
  
  # 参数
  --max-col-width=0 显示搜索的完整信息
  ```

- 查看图表

  ```shell
  # 显示图表的元数据
  helm show chart [chartname]
  # 显示图表的自述(README)文件
  helm show readme [chartname]
  # 显示图表的值(values)
  helm show values [chartname]
  # 显示图表元数据、自述和值
  helm show all [chartname]
  
  # 参数
  --version 11.0.5 显示chart某个版本
  ```

## 3. Release

- 创建release

  ```shell
  # 创建图表的release
  helm apply [xxx.yaml]
  
  # 参数
  -f [xxx.yaml] 使用文件覆盖chart默认值
  ```

- 查看release

  ```shell
  # 查看release 列表
  helm list
  	-n [namespacename] 查看某个namespace下的release
  	
  # 查看release详情
  helm get hooks [releasename]
  helm get manifest [releasename]
  helm get notes [releasename]
  helm get values [releasename]
  hlem get all [releasename]
  	-n [namespacename] 查看某个namespace下的release
  ```

  