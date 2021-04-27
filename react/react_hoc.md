# 深入 React 高阶组件

## 1. 高阶组件是什么？

<code style="color: #708090; background-color: #F5F5F5; font-size: 18px">高阶组件（HOC）</code>是一种基于 React 的组合特性而形成的**设计模式**。

HOC 这种设计模式是怎么设计的 ❓

```js
const EnhancedComponent = higherOrderComponent(WrappedComponent);
```

可以看出：**高阶组件这种设计模式是**<span style="color: #ff0000; font-size: 16px;">一个高阶组件(higherOrderComponent)接收一个包裹组件(WrappedComponent)为参数，返回一个增强组件(EnhancedComponent)</span>。

- WrappedComponent: 需要被增强某些特定功能的一般组件
- higherOrderComponent: 给 WrappedComponent 做特殊的处理，增强其功能
- EnhancedComponent: 增加特定功能后的组件

**高阶组件**在大型前端项目和库（如：Redux、Relay）中应用广泛，是他们不可缺少的组成部分。

类似的，一个函数接收函数作为**输入**，或**输出**一个函数，我们叫做<code style="color: #708090; background-color: #F5F5F5; font-size: 18px">高阶函数</code>。

高阶函数在 JS 种的应用也很广泛，如：

- Array.map()
- Array.reducer()
- Array.sort()

其实，它的本质是**函数式编程**的一种应用。

## 2. 高阶组件有何用？

定义中已经说了，**高阶组件是为了解决 React 组件的组合问题**。

高阶组件主要<span style="color: #ff0000; font-size: 16px;">解决关注点横切的问题</span>。

什么是**关注点横切**❓

通俗的说就是，某些**任务**（关注点）被**多个模块重复使用**（横切）。

常见的关注点横切有：

- 日志功能：多个模块可能都会用到
- 获取用户信息

一句话终结就是：**HOC 可以帮助我们抽象出组件的公共部分**。

## 3. 高阶组件和普通组件的区别

**高阶组件和普通组件的区别是**：

**普通组件**是<span style="color: #ff0000; font-size: 16px;">将 props/state 转换为 UI</span>，而**高阶组件**是<span style="color: #ff0000; font-size: 16px;">将组件转换为另一个组件</span>。

上面这句话来自[React-高阶组件官方文档](https://zh-hans.reactjs.org/docs/higher-order-components.html#convention-wrap-the-display-name-for-easy-debugging)，它具有纲领意义。

如果我们需要处理东西和 UI 相关，那么，他就是**组件的组合问题**。

如果我们处理的东西是多个组件共同所有的，想要把**共有的处理办法提取出来**，那么就需要使用 HOC。

## 4. 如何实现高阶组件

> 高阶组件就是要操作 WrappedComponent，得到一个增强的新组件。

如何操作呢 ❓

- **属性代理（props proxy）**：高阶组件<span style="color: #ff0000; font-size: 16px;">通过被包裹的 React 组件来操作 props</span>
- **反向继承(inheritance inversion)**：高阶组件<span style="color: #ff0000; font-size: 16px;">继承于被包裹的组件</span>

### 3.1 属性代理

**Props Proxy 的简单实现**：

```js
function HOC(WrappedComponent) {
  return class EnhancedComponent extends React.Component {
    render() {
      return <WrappedComponent {...this.props} />;
    }
  };
}
```

需要说明的是，EnhancedComponent 在这里是<code style="color: #708090; background-color: #F5F5F5; font-size: 18px">容器组件</code>，它包裹着**包裹组件(WrappedComponent)**，🌟 就是它**来处理具体增强逻辑**。

HOC 做了什么？

**通过给 WrappedComponent 传递 this.props 得到一个新的组件**。

<code style="color: #708090; background-color: #F5F5F5; font-size: 18px">this.props</code>是**WrappedComponent**本身具有的属性，使用**EnhancedComponent**时可以直接使用它们。

_示例 1：_

```js
import { Component } from "react";
// 定义被包裹组件
const Mouse = (props) => {
  return (
    <span className="mouse" role={props.role}>
      🐭
    </span>
  );
};

// 定义 HOC
const withDrag = (Wrapped) => {
  class WithDrag extends Component {
    render = () => {
      // this.props是要透传给 Wrapped 的属性
      return <Wrapped {...this.props} />;
    };
  }
  return WithDrag;
};

// 生成增强组件 MouseDraggable
const MouseDraggable = withDrag(Mouse);

// 使用增强组件
ReactDOM.render(
  <MouseDraggable role="mouse" />,
  document.getElementById("root")
);
```

这是个基础示例，并没有增强**Mouse 组件**的任何功能。

我们一起看看**属性代理**可以做什么：

- 操作 props
- 通过 Refs 访问到组件实例
- 提取 state
- 用其他元素包裹 WrappedComponent

#### 3.1.1 操作 props

**可以读取、添加、编辑、删除传给 WrappedComponent 的 props**。

_改进例 1 中的 HOC:_

```js
const withDrag = (Wrapped) => {
  class WithDrag extends Component {
    // 增加新的 props
    const newProps = {
      title: "draggable"
    }
    render = () => {
      return <Wrapped {...this.props} {...newProps} />;
    };
  }
  return WithDrag;
};
```

#### 3.1.2 通过 Refs 访问到组件实例

<code style="color: #708090; background-color: #F5F5F5; font-size: 18px">ref</code>像<code style="color: #708090; background-color: #F5F5F5; font-size: 18px">key</code>一样，它**不是 prop 属性**，会被 React 进行特殊处理。

如果对 HOC 添加 ref，该 <span style="color: #ff0000; font-size: 16px;">ref 将引用最外层的容器组件，而不是被包裹的组件</span>。

```js
...
const withDrag = (Wrapped) => {
  class WithDrag extends Component {
    render = () => {
      return <Wrapped {...this.props} />;
    };
  }
  return WithDrag;
};

const MouseDraggable = withDrag(Mouse);
// 创建ref
const ref = React.createRef();

ReactDOM.render(
  <MouseDraggable role="mouse" ref={ref} />,
  document.getElementById("root")
);
```

这里的 ref 指向 **HOC 的容器组件** -- WithDrag。

<code style="color: #708090; background-color: #F5F5F5; font-size: 18px">React.forwardRef</code> 就是一个高阶组件，它能帮我们转发 ref 到增强后的组件。

```js
import { Component } from "react";
const Mouse = (props) => {
  return (
    <span className="mouse" role={props.role}>
      🐭
    </span>
  );
};

const withDrag = (Wrapped) => {
  class WithDrag extends Component {
    render = () => {
      return <Wrapped {...this.props} />;
    };
  }
  return WithDrag;
};

const MouseDraggable = withDrag(Mouse);
const ref = React.createRef();

ReactDOM.render(
  <MouseDraggable role="mouse" ref={ref} />,
  document.getElementById("root")
);
```

这里的 ref 指向 **MouseDraggable**。

### 3.2 反向继承

## 4. 约定

既然高阶组件是一种**设计模式**，我们应该准守它的设计约定。

## 5. 注意事项

## 参考

**Blog:**

浅显易懂，动画辅助 -- [REACT HIGHER ORDER COMPONENTS IN 3 MINUTES](https://jhey.dev/writing/react-higher-order-components-in-3-minutes/)

深入讲解，值得研究 -- [React Higher Order Components in depth](https://medium.com/@franleplant/react-higher-order-components-in-depth-cf9032ee6c3e#.g1lnzrw5d)

**文档:**

[高阶组件](https://zh-hans.reactjs.org/docs/higher-order-components.html)

[暂时看看，翻译](https://zhuanlan.zhihu.com/p/24776678)
