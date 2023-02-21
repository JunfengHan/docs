# Babel 的世界

> Use next generation JavaScript, today.

## 1. 浏览器对 JS 的支持

**现状：**

- 在 ES6 之后，它就按照一年一个版本的速度更新。
- TypeScript、JSX 等 JS 的扩展在不断崛起

然而，浏览器/NodeJS 对于 ES 新版本的支持显然不那么友好。

为了解决这个问题，目前最好的解决办法就是，**把新版的 ES 语法解析成 ES5**。

于是，Babel 出现了。

## 2. Babel 是什么

这个问题，官网给出了一个标准[答案](https://babeljs.io/docs/en/).

**Babel is a JavaScript compiler**.

Babel 是一个**工具链**，主要用于在当前和较旧的浏览器或环境中将 <span style="color: #ff0000; font-size: 16px;">ECMAScript 2015+代码转换为 JavaScript 的向后兼容版本</span>。

**以下是 Babel 可以为您做的主要事情：**

- 转换语法
- 提供目标环境缺少的 Polyfill 功能（通过[@babel/polyfill](https://babeljs.io/docs/en/babel-polyfill)）
- 源代码转换

## 3. 如何使用 Babel

<code style="color: #708090; background-color: #F5F5F5; font-size: 18px">Babel</code>适用于不同的框架，且不同框架的配置也有所不同。

请根据自己项目的具体情况来[选择](https://babeljs.io/setup).

本文只讨论**Webpack**构建的项目。

### 3.1 安装依赖

安装 **babel-loader** 和 Babel 核心 **babel-loader**。

```bash
npm install --save-dev babel-loader @babel/core
```

### 3.2 添加 webpack rules

在 webpack 配置文件（如：webpack.config.js）中的 rules 中配置规则，使用 <code style="color: #708090; background-color: #F5F5F5; font-size: 18px">babel-loader</code> 解析 <code style="color: #708090; background-color: #F5F5F5; font-size: 18px">.js</code> 后缀文件.

```js
{
  module: {
    rules: [
      {
        test: /\.m?js$/,
        exclude: /node_modules/,
        use: {
          loader: "babel-loader",
        },
      },
    ];
  }
}
```

### 3.3 添加 Babel 预设

我们在 webpack 配置文件中设置了 Babel 来解析 JS 文件，但是它并没有做任何事情。

我们需要给 Babel 配置预设，预设是一些解析规则，告诉 Babel 该**如何解析**。

_安装配置模块：_

```js
npm install @babel/preset-env --save-dev
```

在项目根目录新建 babel.config.json 文件，

```json
{
  "presets": ["@babel/preset-env"]
}
```

或者，将 Babel 配置信息放在 rules 中的 options 选项。

```js
{
  module: {
    rules: [
      {
        test: /\.m?js$/,
        exclude: /node_modules/,
        use: {
          loader: "babel-loader",
        },
        options: {
          presets: ["@babel/preset-env"],
        },
      },
    ];
  }
}
```

这个 “preset”预设 是什么？

preset 是一个设定好的插件（plugins）集合。

### 3.3 插件

<code style="color: #708090; background-color: #F5F5F5; font-size: 18px">plugins</code> 是小型的 JavaScript 程序，**可指示 Babel 如何进行代码转换。**

如 <code style="color: #708090; background-color: #F5F5F5; font-size: 18px">@babel/plugin-transform-arrow-functions</code> 是个可以将 ES6+ 中的箭头函数转化为 ES5 中的普通函数。

```js
const fn = () => 1;

// converted to

var fn = function fn() {
  return 1;
};
```

### 3.4 配置 Babel options(选项)

Babel [选项](https://babeljs.io/docs/en/options#primary-options)

## 5. Babel 常用插件

> 插件赋予了 Babel 更强大的能力。

## 参考

[Bable 官网](https://babeljs.io/)
