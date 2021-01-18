# NodeJs框架

> 框架

## 1. 为什么需要框架

## 2. NodeJs框架的发展与对比

NodeJs框架可谓是百花齐放，目前主流的有：

- Express: 最出名、目前下载量最高的框架
- Koa: Express团队基于async/await的新作
- Egg: 阿里推出的企业级框架
- Nest: 基于TypeScript的

*npm包的下载量对比：*

## 3. 为什么选择 Nest

> Nest是框架，它组织和规范我们的代码结构。Express\Koa是封装了NodeJS HTTP模块 的库，方便我们使用NodeJs的API而已。

- 完美支持TypeScript: 我认为Node项目有必要使用TypeScript
- 架构好：面向AOP编程
- 支持TypeORM: 非常适合非关系型数据库

## 4. NestJs 使用

### 4.1 安装

*脚手架安装：*

```bash
npm i -g @nestjs/cli
nest new my-nest-project
```

*GIT安装：*

```bash
git clone https://github.com/nestjs/typescript-starter.git my-nest-project
cd my-nest-project
npm install
```

*手动安装：*

```bash
npm i --save @nestjs/core @nestjs/common rxjs reflect-metadata
```

### 4.2 启动

*进入项目文件夹并启动项目：*

```bash
cd my-nest-project
npm run start
```

## 5. 深入了解NestJs

## 6. NestJs部署
