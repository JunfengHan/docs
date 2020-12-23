# 一文读懂 React Hook

> Hook 为已知的 React 概念提供了更直接的 API：props， state，context，refs 以及生命周期。

**Hook 使你在非 class 的情况下可以使用更多的 React 特性**。

## 1.为什么需要 React Hook

React 官方文档里写了他们开发Hook的[动机](https://zh-hans.reactjs.org/docs/hooks-intro.html#motivation)，动机恰恰正是 Hook要解决的问题：

- 在组件之间复用状态逻辑很难：Hook 使你在无需修改组件结构的情况下复用状态逻辑
- 复杂组件变得难以理解：Hook 将组件中相互关联的部分拆分成更小的函数（比如设置订阅或请求数据）
- 难以理解的 class：Hook 使你在非 class 的情况下可以使用更多的 React 特性

目前写React 组件的两种方式：

- class组件
- 函数组件

我们知道，React的架构遵循 schedule - render - commit 的运行流程，这个流程是React世界最底层的运行规律。

<code style="color: #708090; background-color: #F5F5F5;">ClassComponent</code>  作为React世界的原子，他的生命周期（componentWillXXX/componentDidXXX）是为了介入React的运行流程而实现的更上层抽象，这么做是为了方便框架使用者更容易上手。

函数组件又叫<code style="color: #708090; background-color: #F5F5F5;">“无状态组件”</code>，在它内部是不能使用 state 的，虽然效率更高，但缺点也足够致命。

所以，我们的组件基本都是 <code style="color: #708090; background-color: #F5F5F5;">ClassComponent</code>  组织起来的，如果组件很简单，那就用纯函数写的<code style="color: #708090; background-color: #F5F5F5;">无状态组件</code>作为一个补充。

在React中，我们可以说<code style="color: #708090; background-color: #F5F5F5;">ClassComponent</code> 是一类原子。

那么，Hook 在React 中就是电子，能在更细粒度控制React组件。

Hook 拥抱了函数，是我们在<code style="color: #708090; background-color: #F5F5F5;">函数组件</code>里可以使用几乎所有<code style="color: #708090; background-color: #F5F5F5;">ClassComponent</code>的特性。

## 2.React Hook 是什么

[React Hook](https://zh-hans.reactjs.org/docs/hooks-overview.html#but-what-is-a-hook)是一些可以让你在<code style="color: #708090; background-color: #F5F5F5;">函数组件</code>里“钩入” React state 及生命周期等特性的函数。

我们类分析下React 官方给的这个定义。

**React Hook 是 函数**。

是什么样的函数？

是在函数组件里“钩入” <code style="color: #708090; background-color: #F5F5F5;">React state</code> 及<code style="color: #708090; background-color: #F5F5F5;">生命周期</code>等特性的函数。

那么React 的特性就很好理解了：

- 只能写在函数组件中
- 可以用它来“钩入”React state 及生命周期等特性

## 3.React Hook 分类和使用

React Hook大致可以分为4种：

- State Hook
- Effect Hook
- 自定义 Hook
- 其他 Hook

### State Hook

React 内置了一些像 <code style="color: #708090; background-color: #F5F5F5;">useState</code> 这样的 <code style="color: #708090; background-color: #F5F5F5;">Hook</code>，它是 <code style="color: #708090; background-color: #F5F5F5;">State Hook</code>。

```js
import React, { useState } from 'react';

function Example() {
  // 声明一个叫 “count” 的 state 变量
  // 设置了 “count” 初始化值为 0
  // 改变 “count” 可以使用 setCount 这个函数
  const [count, setCount] = useState(0);

  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>
        Click me
      </button>
    </div>
  );
}

```

### Effect Hook

> 在 React 组件中执行过数据获取、订阅或者手动修改过 DOM。我们统一把这些操作称为<code style="color: #708090; background-color: #F5F5F5;">“副作用</code>”，或者简称为<code style="color: #708090; background-color: #F5F5F5;">“作用(Effect)”</code>。

<code style="color: #708090; background-color: #F5F5F5;">useEffect</code> 就是一个 [Effect Hook](https://zh-hans.reactjs.org/docs/hooks-overview.html#effect-hook)，给函数组件增加了操作副作用的能力。

**当你调用 useEffect 时，就是在告诉 React 在完成对 DOM 的更改后运行你的“副作用”函数**。

useEffect 和 class 组件中的 componentDidMount、componentDidUpdate 和 componentWillUnmount 具有相同的用途，只不过被合并成了一个 API。

例如，下面这个组件在 React 更新 DOM 后会设置一个页面标题：

```js
import React, { useState, useEffect } from 'react';

function Example() {
  const [count, setCount] = useState(0);

  // 相当于 componentDidMount 和 componentDidUpdate:
  useEffect(() => {
    // 使用浏览器的 API 更新页面标题
    document.title = `You clicked ${count} times`;
  });

  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>
        Click me
      </button>
    </div>
  );
}
```

### 自定义 Hook

> 自定义 Hook 更像是一种约定而不是功能。

<code style="color: #708090; background-color: #F5F5F5;">自定义 Hook</code> 可以在组件之间重用一些状态逻辑。

如果函数的名字以 “use” 开头并调用其他 Hook，我们就说这是一个自定义 Hook。

[自定义Hook](https://zh-hans.reactjs.org/docs/hooks-overview.html#building-your-own-hooks)的作用是不增加组件的情况下，像 <code style="color: #708090; background-color: #F5F5F5;">高阶组件</code>和 <code style="color: #708090; background-color: #F5F5F5;">render props</code> 一样，解决组件之间重用一些状态的问题。

### 其他 Hook

还有一些使用频率较低的但是很有用的 Hook,查看[Hook 索引](https://zh-hans.reactjs.org/docs/hooks-reference.html)。

比如，useContext 让你不使用组件嵌套就可以订阅 React 的 Context。

```js
function Example() {
  const locale = useContext(LocaleContext);
  const theme = useContext(ThemeContext);
  // ...
}
```

另外 useReducer 可以让你通过 reducer 来管理组件本地的复杂 state

```js
function Todos() {
  const [todos, dispatch] = useReducer(todosReducer);
  // ...
}
```

[React Hook的使用](https://zh-hans.reactjs.org/docs/hooks-state.html) 还是看官方文档吧，这里没必要复述了。

## 4.React Hook 工作原理解析



## 5.手动实现一个useState 方法

## 参考

[React官方文档](https://zh-hans.reactjs.org/docs/hooks-intro.html)
[React技术揭秘-Hook](https://react.iamkasong.com/hooks/prepare.html)
[一篇看懂 React Hook](https://zhuanlan.zhihu.com/p/50597236)