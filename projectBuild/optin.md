###  一个webpack项目的优化

## 1. 从package.json 开始

### 1.1 npm install 查找问题

- 查看安装时输出的问题，及时更新已经废弃的包
- npm audit fix --force 自动修改相关问题

## 2.文件大小

### 2.1 代码切割

@loadable/component + webpack

### 2.2 antd样式动态引入

