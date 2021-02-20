# webpack loader原理即手动实现一个html-loader

> Show me your code!

## 1. loader是做什么的

webpack 只能理解 JavaScript 和 JSON 文件。

**loader 让 webpack 能够去处理其他类型的文件，并将它们转换为有效 模块。**

有效模块可以供应用程序使用，以及被添加到依赖图中。

loader 类似于其他构建工具（如：Grunt）中“任务(task)”。

**loader特性：**

---

- loader 支持链式传递,链中的每个 loader 会将转换应用在已处理过的资源上。链中的最后一个 loader，返回 webpack 期望 JavaScript
- 






## 2. 如何使用loader

### 1. 安装loader

```bash
npm install --save-dev css-loader
npm install --save-dev ts-loader
```

### 2. 配置使用规则

**根目录下webpack.config.js:**

```js
module.exports = {
  module: {
    rules: [
      { test: /\.css$/, use: 'css-loader' },
      { test: /\.ts$/, use: 'ts-loader' }
    ]
  }
};
```

在配置文件的rules 中制定loader 来处理对应的文件。

<span style="color: #ff0000; font-size: 16px;">loader 从右到左地取值(evaluate)/执行(execute)</span>。

```js
module.exports = {
  module: {
    rules: [
      {
        test: /\.css$/,
        use: [
          { loader: 'style-loader' },
          {
            loader: 'css-loader',
            options: {
              modules: true
            }
          },
          { loader: 'sass-loader' }
        ]
      }
    ]
  }
};
```

上面loader的执行顺序为：

<code style="color: #708090; background-color: #F5F5F5;">sass-loader</code> -> <code style="color: #708090; background-color: #F5F5F5;">css-loader</code> -> <code style="color: #708090; background-color: #F5F5F5;">style-loader </code>

## 3. 示例:傻傻分不清的样式处理loader

> css的模块化是如此复杂，以至于出现这么多的处理方式。

**先看看样式处理loader有哪些吧：**

- style-loader：把 js 中 import 导入的样式文件代码，打包到 js 文件中，运行 js 文件时，将样式自动插入到\<style\>标签中
- css-loader: 解析JS @import 和 url() 中 import/require() 的css 文件，并正确分解它们为可用模块
- postcss-loader: 使用配置的JS工具来处理css
- sass-loader: Loads a Sass/SCSS file and compiles it to CSS.
- css-loader: Compiles Less to CSS.
- MiniCssExtractPlugin.loader

注意⚠️：

mini-css-extract-plugin不能与style-loader混用；





## 4. 编写一个loader

## 参考

[Webpack4官方文档](https://v4.webpack.docschina.org/concepts/)

[常见 loader 源码简析，以及动手实现一个 md2html-loader](https://github.com/6fedcom/fe-blog/tree/master/webpack/loader)

[编写一个loader](https://v4.webpack.docschina.org/contribute/writing-a-loader)

[Webpack loader 之 file-loader](https://cloud.tencent.com/developer/article/1533438)

[[webpack] devtool里的7种SourceMap模式是什么鬼？](https://juejin.cn/post/6844903450644316174)
