# React项目中的样式处理

> React 官方文档中是没有任何 css相关的处理方案的，留给大家来思考了。

JavaScript的模块化有多种解决方案，目前和日后主流的应该是 ES6的实现方案，在ES6 模块化出现之前，模块化方案就有<code style="color: #708090; background-color: #F5F5F5;">CommonJS</code>、<code style="color: #708090; background-color: #F5F5F5;">AMD</code>、<code style="color: #708090; background-color: #F5F5F5;">CMD</code>。

其中，<code style="color: #708090; background-color: #F5F5F5;">CommonJS</code>是 <code style="color: #708090; background-color: #F5F5F5;">nodeJs</code>实现的方案，主要用于后端。AMD和CMD主要用于前端，二者之前的区别是先引入和后引入的区别，目前随着兼容性的提高，二者几乎没什么区别了。

然而，我们今天的主角 css，它的模块化现在仍然前途未卜。

## 1. 为什么我们需要 CSS 模块化？

- CSS 作用域是全局的
- 无法模块化

### 1.1 CSS 作用域是全局的

CSS 的规则都是全局的，任何一个组件的样式规则，都对整个页面有效。这样很方便，但会遇到样式冲突（污染）的问题。

为了解决这些问题，我们大概会这样做：

- class命名写长一点，避免重复：之前我参与的项目样式是这样的（.M-dialog-head, "M"代表这是个公共模块的样式，"dialog"是组件名，“head”是指dialog 的头部）

通常我们需要仔细组织自己的样式文件命名，否则可能就会出现样式污染。

所以亟待解决的问题就是 css 局部作用域避免全局样式冲突（污染）的问题。

### 1.2 CSS组件化

JavasScript 的模块化支持算比较完善了，我们写的各个小组件能很好地合作，共同组建成一个大型的项目。这也是React 官方推荐的组件组合方式。

然而，对组件相当重要的CSS目前是不支持组件化的。

社区目前诞生了几种解决方案。

**主流的实现有这么几种：**

- **CSS 命名约定**：规范化 CSS 的模块化解决方案（比如 BEM、OOCSS)
  缺点：
  1. JS CSS 之间依然没有打通变量和选择器等
  2. 复杂的命名
  
- **CSS in JS**：彻底抛弃 CSS，用 JavaScript 写 CSS 规则，并内联样式（如：styled-components）
  缺点：
  1. 无法使用伪类，媒体查询等
  2. 样式代码也会出现大量重复
  3. 不能利用成熟的 CSS 预处理器（或后处理器）
  4. html和样式组合，可读性差

- **使用 JS 来管理样式模块**:使用 JS 编译原生的 CSS 文件，使其具备模块化的能力（如：CSS Modules、PostCSS）
  优点：
  1. 最大化地结合现有 CSS 生态 (预处理器/后处理器等) 和 JS 模块化能力
  2. 几乎零学习成本

## 2. React官方默认的样式处理方式

<code style="color: #708090; background-color: #F5F5F5;">create-react-app</code> 是很多人构建React项目的方式，构建后会出现相应的文件，如 index.js、App.js、App.css等。

*index.js:*

```js
import App from './App';
import App1 from './App1';
import './index.css';

ReactDOM.render(
  <React.StrictMode>
    <App />
    <App1 />
  </React.StrictMode>,
  document.getElementById('root')
);
```

*index.css:*

```css
.bgColor {
  background: red
}
```

*App.js:*

```js
import React, { Component } from 'react';
class App extends Component {
  render() {
    return <div className='bgColor'>组件App</div>
  }
}
export default App;
```

*App1.js:*

```js
import React, { Component } from 'react';
class App extends Component {
  render() {
    return <div className='bgColor'>组件App1</div>
  }
}
export default App1;
```

index.js中引入的index.css 在浏览器中出现在 head 标签下会生成一个 style 标签，因此index.js中使用的组件都可以用 index.css中的样式。

但是，如果我们修改index.css中的样式，App组件和App1组件的样式都会被修改。

显然，我们会遇到css 目前最大的问题，那就是 <code style="color: #708090; background-color: #F5F5F5;">模块化</code>。

## 3. styled-components

> 既然CSS对模块化不友好，干脆用JS来处理CSS不就行了。

<code style="color: #708090; background-color: #F5F5F5;"> styled-components </code> 将样式直接写入到组件中，得到的是一个样式的组件，在调用组件时是带着样式的。

Styled Component是react的一个第三方库，是CSS in JS 的优秀实践和代表，将CSS写在JS中，可以实现常规CSS所不好处理的逻辑复杂、函数方法、复用、避免干扰。

*使用 Styled Component：*

___

1. 安装 Styled Component 包

```bash
npm i Styled Component --save-dev
```

2. 创建 Styled Component 文件

*style.js:*

```js
import { createGlobalStyle } from 'styled-components';

export const Logo = styled.div`
  position: absolute;
  top: 0;
  left: 0;
  display: block;
  width: 160px;
  height: 56px;
`;

export const NavItem = styled.div`
  height: 56px;
  line-height: 56px;
  &.right {
    float: right;
    color: #969696;
  }
  &.navActive {
    background: #ea6f5a;
  }
`;
```

3. 使用样式文件

```js
// 引入样式文件导出的模块
import React, { Component } from 'react';
import { Logo, NavItem } from './style';

class App extends Component {
  render() {
    return (
      <div>
        // 使用组件
        <Logo></Logo>
        <NavItem></NavItem>
      </div>
    ) 
  }
}

export default App;

```

___

具体的 styled-components 使用请看[这里](https://styled-components.com/)。

显然，styled-components 的优点是基本能实现CSS的模块化；

缺点是**要像写JS一样来写CSS**，且<span style="color: #ff0000; font-size: 16px;">易读性比较差</span>。

为什么说易读性差呢？

```js
<Logo></Logo>
<NavItem></NavItem>
```

**看到这两个组件，你能看到什么？甚至它是什么元素都不知道，个人感觉和React 声明式的理念不太符合，JSX的让写JS像HTML一样，但 styled-components 却破坏了它，所以我尝试一下，实在不喜欢这中方式，最终放弃了。**

## 4. CSS Modules

> CSS 依然是CSS，只不过加入了局部作用域和模块依赖。

CSS Module 就是所有的类名都只有局部作用域的 CSS 文件。

CSS Modules 是一种 <code style="color: #708090; background-color: #F5F5F5;">CSS模块化</code>解决方案。

CSS Modules 文件会被编译成[ICSS](https://github.com/css-modules/icss)，但写法上和普通CSS文件一样。

CSS Modules 既不是官方标准，也不是浏览器的特性，而是在构建步骤（例如使用 Webpack 或 Browserify）中对 CSS 类名选择器限定作用域的一种方式（通过 hash 实现类似于命名空间的方法）。

原理是：**在使用 CSS Module 时，类名是动态生成的，唯一的，并准确对应到源文件中的各个类的样式。**

当从JS模块导入CSS模块时，它会导出一个对象，其中包含所有从本地名到全局名的映射。

```js
import styles from "./style.css";
// import { className } from "./style.css";

element.innerHTML = '<div class="' + styles.className + '">';
```

### 4.1 使用CSS Modules

我们可以在不同的构建模式下使用 CSS Modules，Webpack、postcss、Theming、browserify等，本文讨论 Webpack 作为打包工具的情况，其他情况请看[官网案例](https://github.com/css-modules/css-modules#examples)。

**在webpack配置文件启用 CSS Modules:**

项目的 webpack.config.js:

```js
module.exports = {
  entry: "./src/index.js",
  mode: "development",
  module: {
    rules: [
      {
        test: /\.css$/,
        exclude: /node_modules/,
        use: [
          "style-loader",
          {
            loader: "css-loader",
            options: {
              // 指定启用css modules
              modules: true,
              // 允许你配置在css-loader被应用到@import资源之前有多少个加载器
              importLoaders: 2,
              localIdentName: '[name]__[local]--[hash:base64:5]'
            }
          }
        ]
      }
    ]
  }
}
```

CSS Modules的具体使用方法建议直接查看下面参考中的官方地址，和相关学习链接。

## 5. PostCSS

**PostCSS 是一个允许使用 JS 插件转换样式的工具。**

PostCSS 接收一个 CSS 文件并提供了一个 API 来分析、修改它的规则（通过把 CSS 规则转换成一个抽象语法树的方式）。在这之后，这个 API 便可被许多插件利用来做有用的事情，比如寻错或自动添加 CSS vendor 前缀。

PostCss插件能做的就多了，如官网介绍的4大功能：

- Autoprefixer：在CSS规则中添加供应商前缀
- PostCSS Preset Env：使用CSS新特性
- CSS Modules: 解决CSS全局样式污染问题
- stylelint： CSS错误检查
...

### 5.1 使用PostCSS

根目录新建 postcss.config.js 文件：

```js
module.exports = {
  // parser: 'sugarss',
  plugins: {
    // 'postcss-import': {},
    // 'postcss-cssnext': {},
    // 'cssnano': {},
    'autoprefixer': {}
  }
}
```

**配置 webpack.config.js:**

```js
module: {
  rules: [
    {
      test: /\.css$/,
      exclude: /node_modules/,
      use: [
        "style-loader",
        {
          loader: "css-loader",
          options: {
            modules: true,
            localIdentName: '[name]__[local]--[hash:base64:5]'
          }
        },
        // 添加 postcss-loader 处理css文件
        'postcss-loader'
      ]
    }
  ]
}
```

## 参考：

[CSS Modules 官方文档](https://github.com/css-modules/css-modules)

[CSS Modules 用法教程-阮一峰](http://www.ruanyifeng.com/blog/2016/06/css_modules.html)

[PostCss 官方文档](https://postcss.org/)

[PostCss 中文文档](https://github.com/postcss/postcss/blob/main/docs/README-cn.md)

[CSS Modules入门及React中的实践](http://www.alloyteam.com/2017/03/getting-started-with-css-modules-and-react-in-practice/#prettyPhoto)

[css模块化及CSS Modules使用详解](https://cloud.tencent.com/developer/article/1036799)

[Webpack打包配置解读-CSS 样式篇](https://juejin.cn/post/6868538920796340237#heading-1)