# React 高阶应用 -- 组件抽象

> 解决关注点横切，抽取出组件的公共部分，是大型应用的必备工作。

## 1. 关注点横切



## 2. 如何解决关注点横切



## 3.mixin

### 3.1 什么是 mixin

### 3.2 如何使用 mixin

### 3.3 mixin 使用技巧

### 3.4 mixin 的应用

### 3.5 mixin 注意事项

## 4. 高阶组件

<code style="color: #708090; background-color: #F5F5F5; font-size: 18px">高阶组件（HOC）</code>是一种基于 **React 的组合特性**而形成的**设计模式**。

具体而言，<span style="color: #ff0000; font-size: 16px;">高阶组件是参数为组件，返回值为新组件的函数</span>。

可以看出，HOC 的**主要作用是复用组件**。

```js
const EnhancedComponent = higherOrderComponent(WrappedComponent);
```

#### 1.2.1 高阶组件解决横切关注点问题

关注点是是什么 ❓

**关注点**指某个功能（如日志）。

**横切关注点**是指面向对象中某个**关注点在多个模块中使用**（如日志模块）。

如果我们需要在不同的模块中做以下事情：

- 在挂载时，向 DataSource 添加一个更改侦听器（用于获取数据）
- 在侦听器内部，当数据源发生变化时，调用 setState（设置数据）
- 在卸载时，删除侦听器（删除监听器）

_CommentList 和 BlogPost 两个组件都需要获取评论数据：_

```js
const CommentListWithSubscription = withSubscription(
  CommentList,
  (DataSource) => DataSource.getComments()
);

const BlogPostWithSubscription = withSubscription(
  BlogPost,
  (DataSource, props) => DataSource.getBlogPost(props.id)
);
```

第一个参数是**被包装组件**。

第二个参数通过 **DataSource** 和当前的 **props** 返回我们**需要的数据**。

_高价组件示例：_

```js
// 此函数接收一个组件(传入组件)...
function withSubscription(WrappedComponent, selectData) {
  // ...并返回另一个组件（增强组件）...
  return class extends React.Component {
    constructor(props) {
      super(props);
      this.handleChange = this.handleChange.bind(this);
      this.state = {
        data: selectData(DataSource, props),
      };
    }

    componentDidMount() {
      // ...负责订阅相关的操作...
      DataSource.addChangeListener(this.handleChange);
    }

    componentWillUnmount() {
      DataSource.removeChangeListener(this.handleChange);
    }

    handleChange() {
      this.setState({
        data: selectData(DataSource, this.props),
      });
    }

    render() {
      // ... 并使用新数据渲染被包装的组件!
      // 请注意，我们可能还会传递其他属性
      return <WrappedComponent data={this.state.data} {...this.props} />;
    }
  };
}
```

**HOC 特性：**

- HOC 接收一个组件(传入组件)，使用数据将该组件组合成一个新组件，并返回新组件（增强组件）
- HOC 不会修改传入的组件，只是添加了某些属性
- HOC 应该是纯函数，不能有副作用

**HOC 错误示例：❌**

_HOC 中修改组件原型：_

```js
function logProps(InputComponent) {
  InputComponent.prototype.componentDidUpdate = function (prevProps) {
    console.log("Current props: ", this.props);
    console.log("Previous props: ", prevProps);
  };
  // 返回原始的 input 组件，暗示它已经被修改。
  return InputComponent;
}

// 每次调用 logProps 时，生命周期函数componentDidUpdate都会执行，增强组件都会有 log 输出。
const EnhancedComponent = logProps(InputComponent);
```

### 4.1 什么是高阶组件

### 4.2 如何使用高阶组件

### 4.3 高阶组件使用技巧

### 4.4 高阶组件的应用

### 4.5 高阶组件注意事项

## 参考

官方文档，总结全面，但不太容易理解 -- [高阶组件](https://zh-hans.reactjs.org/docs/higher-order-components.html)

生动形象 -- [REACT HIGHER ORDER COMPONENTS IN 3 MINUTES](https://jhey.dev/writing/react-higher-order-components-in-3-minutes/)
