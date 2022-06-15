# 深入理解 react-create-app

> 如何创建一个 React 应用，答：脚手架。

## 1. 如何创建 React 应用

想要使用 React 的方法有两种：

- 1. 在你已有的项目中引入 React

  请看 React 官网文章[在网站中添加 React](https://zh-hans.reactjs.org/docs/add-react-to-a-website.html)

- 2. 创建一个新的基于 React 的项目

  请看 React 官网文章[创建新的 React 应用](https://zh-hans.reactjs.org/docs/create-a-new-react-app.html)

  总结一下就是：

  - 你可以手动[从 0 到 1 打造一个自己的 React 应用](https://blog.usejournal.com/creating-a-react-app-from-scratch-f3c693b84658)

  - 如果你嫌麻烦的化，使用 React 应用**构建工具**可以让你轻松构建一个功能完善的 React 应用，如<code style="color: #708090; background-color: #F5F5F5; font-size: 18px">Gatsby</code>、<code style="color: #708090; background-color: #F5F5F5; font-size: 18px">Next.js</code>、以及我们今天的主角<code style="color: #708090; background-color: #F5F5F5; font-size: 18px">Create React App</code>等。

## 2. 认识 react-create-app

> react-create-app 是 Dan 开发的一个 NPM 包，可以让我们使用命令行的方式轻松构建 React 应用。

### 2.1 Create React App 是什么

<code style="color: #708090; background-color: #F5F5F5; font-size: 18px">Create React App</code>包含两个包：

- <code style="color: #708090; background-color: #F5F5F5; font-size: 18px">create-react-app/code> 是一个**全局命令行实用程序**，可用于创建新项目
- <code style="color: #708090; background-color: #F5F5F5; font-size: 18px">react-scripts/code> 是生成的项目中的一个开发依赖项

### 2.2 Create React App 做了什么

- <code style="color: #708090; background-color: #F5F5F5; font-size: 18px">Create React App</code>只是创建一个前端构建流水线（build pipeline），所以你可以使用它来配合任何你想使用的后端

- CLI 默认使用 Yarn 安装管理依赖

- 在**生产模式**下会打包 React 并优化构建以获得最佳性能

## 3. react-create-app 如何使用

### 3.1 创建 react 应用

官网文章写的够详细，传送门[Getting Started](https://create-react-app.dev/docs/getting-started).

首先，你无需安装它：

这要感谢 <code style="color: #708090; background-color: #F5F5F5; font-size: 18px">npx</code>，它让我们无需安装 NPM 的一些 CLI 包，就能直接使用他们。看看 NPM CLI 团队成员的介绍：[Introducing npx: an npm package runner](https://medium.com/@maybekatz/introducing-npx-an-npm-package-runner-55f7d4bd282b).

新建一个 React 应用*my-app*：

```js
npx create-react-app my-app
```

使用**模板**创建一个 React 应用*my-app*：

```js
npx create-react-app my-app --template [template-name]
```

新建一个基于 TypeScript 的 React 应用*my-app*：

```js
npx create-react-app my-app --template typescript
```

### 3.2 创建结果

默认情况创建的*my-app*目录：

```js
my-app
├── README.md
├── node_modules
├── package.json
├── .gitignore
├── public
│   ├── favicon.ico
│   ├── index.html
│   ├── logo192.png
│   ├── logo512.png
│   ├── manifest.json
│   └── robots.txt
└── src
    ├── App.css
    ├── App.js
    ├── App.test.js
    ├── index.css
    ├── index.js
    ├── logo.svg
    ├── serviceWorker.js
    └── setupTests.js
```

**说明**：

- 项目构建（build）时，以下文件必须存在

  - <code style="color: #708090; background-color: #F5F5F5; font-size: 18px">public/index.html</code> 应用主页模板
  - <code style="color: #708090; background-color: #F5F5F5; font-size: 18px">src/index.js</code> JS 入口文件

- <code style="color: #708090; background-color: #F5F5F5; font-size: 18px">src</code> 是我们的工作目录

- <code style="color: #708090; background-color: #F5F5F5; font-size: 18px">public</code>目录下的文件（如某些插件）才能用在 <code style="color: #708090; background-color: #F5F5F5; font-size: 18px">public/index.html</code>里

## 4. 自定义 development 配置

> 配置一个合适的开发环境可以大大提高开发效率，减少错误率。

### 4.1 语法高亮

配合我们常用的 IDE，使用<code style="color: #708090; background-color: #F5F5F5; font-size: 18px">ESLint</code>可以提示我们开发者遇到的语法错误等。

我们可以通过拓展*eslintConfig*配置实现特定的检查规则，例如下面是关于**typeScript**的配置：

```js
{
  "eslintConfig": {
    "extends": ["react-app", "shared-config"],
    "rules": {
      "additional-rule": "warn"
    },
    "overrides": [
      {
        "files": ["**/*.ts?(x)"],
        "rules": {
          "additional-typescript-only-rule": "warn"
        }
      }
    ]
  }
}
```

### 4.2 在 VS Code 中调试代码

在 VS Code 中调试代码需要做两件事：

1. 配置<code style="color: #708090; background-color: #F5F5F5; font-size: 18px">launch.json</code>文件
2. VS Code 安装插件 <code style="color: #708090; background-color: #F5F5F5; font-size: 18px">Chrome Debugger</code>

然后通过<code style="color: #708090; background-color: #F5F5F5; font-size: 18px">npm start</code>命令启动项目，在 VS Code 中启动 Debug(快捷键 F5)。

### 4.3 自动格式化代码

为了在代码**commit**时进行代码的自动格式化，需要安装以下依赖：

```js
npm install --save husky lint-staged prettier
```

- <code style="color: #708090; background-color: #F5F5F5; font-size: 18px">husky</code> 可以像使用 npm 脚本一样使用 githooks
- <code style="color: #708090; background-color: #F5F5F5; font-size: 18px">lint-staged</code> 允许我们在代码的暂存文件上运行脚本
- <code style="color: #708090; background-color: #F5F5F5; font-size: 18px">prettier</code> 是我们在 commit 时执行的 JS 格式化程序

当然，别忘了在*package.json*中进行配置：

```js
"husky": {
  "hooks": {
    "pre-commit": "lint-staged"
  }
},
"lint-staged": {
  "src/**/*.{js,jsx,ts,tsx,json,css,scss,md}": [
    "prettier --write"
  ]
},
```

这样，我们的代码会在每次 commit 时进行格式化。

你也可以随时手动格式化：

```shell
./node_modules/.bin/prettier --write "src/**/*.{js,jsx,ts,tsx,json,css,scss,md}"
```

或者在 VS Code 中进行配置，每次保存时进行格式化。

## 5. 自定义 production 配置

## 4. 如何修改 react-create-app 默认配置

## 5. react-create-app 源码解析

## 6. react-create-app 哲学

## 参考

官方网站，写的很详细，推荐 👍 [Create React App](https://create-react-app.dev/)

源码地址，去 start 一下 🌟 [github 源码](https://github.com/facebook/create-react-app)
