# React 开发实战

> 基于 react-demo 项目的实验。

## 1. 创建一个 React App

最简单的方法是使用 <code style="color: #404090; background-color: #cccccc; font-size: 18px">[Create React App](https://zh-hans.reactjs.org/docs/create-a-new-react-app.html#create-react-app)</code>脚手架来创建。

```shell
npx create-react-app my-app
cd my-app
npm start
```

这样我们就创建了一个名称为 **my-app**的 React 项目。

> 原理解析：

---

// TODO

## 2. 引入 Antd 样式

1. 全局引入

在全局样式 App.css 中引入所有 antd 样式；

```css
/* App.css */
@import "~antd/dist/antd.css";
```

**缺点**：引入了不必要的样式，增大了文件体积。

2. 按需加载

需要哪些工具？

- [react-app-rewired](https://github.com/timarney/react-app-rewired): 对 create-react-app 进行自定义配置
- 配套插件[customize-cra](https://github.com/arackaf/customize-cra)
- [babel-plugin-import](https://github.com/ant-design/babel-plugin-import): 按需加载组件代码和样式的 babel 插件,其[原理](https://3x.ant.design/docs/react/getting-started-cn#%E6%8C%89%E9%9C%80%E5%8A%A0%E8%BD%BD)是什么？

```shell
yarn add react-app-rewired customize-cra babel-plugin-import
```

- 1. 在项目根目录创建一个 <code style="color: #708090; background-color: #F5F5F5; font-size: 18px">config-overrides.js</code> 用于修改<code style="color: #708090; background-color: #F5F5F5; font-size: 18px">create-react-app</code>生成的默认配置.

```js
// config-overrides.js
const { override, fixBabelImports } = require("customize-cra");

module.exports = override(
  fixBabelImports("import", {
    libraryName: "antd",
    libraryDirectory: "es",
    style: "css",
  })
);
```

更多<code style="color: #708090; background-color: #F5F5F5; font-size: 18px">create-react-app</code>中配置 antd，可以看[这里](https://3x.ant.design/docs/react/use-with-create-react-app-cn)

> 原理解析：

---

- **CSS**: 基本原理是往页面插入单独的\<style\>标签
- **JS**: 利用模块化方案，根据*模块引用*情况来进行打包

## 3. 获取子组件（表单）的数据

> 用户注册、后台数据录入等场景都需要我们获取、设置表单数据，这是一个非常常见的需求。

组件结构示意 ⬇️ ：

```js
<Card>
  <Tabs defaultActiveKey="1" onChange={callback}>
    <TabPane tab="Tab 1" key="1">
      <Form1></Form1>
    </TabPane>
    <TabPane tab="Tab 2" key="2">
      <Form2></Form2>
    </TabPane>
    <TabPane tab="Tab 3" key="3">
      <Form3></Form3>
    </TabPane>
  </Tabs>
</Card>
```
