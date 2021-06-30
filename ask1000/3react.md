# React 篇

> React 问题快问快答。

## 1. Hook 相关

问 ❓：为什么需要 Hooks?或者说 Hooks 解决了什么问题？

> 答：Hooks 的设计初衷是**改进 React 组件开发模式**，使**函数组件**也具备操作*state*等特性的权利；
>
> Hook 的动机：
>
> <1> **组件之间难以复用状态逻辑**：(例如只能使用高阶组件做登陆状态判断)，Hook 使用**自定义 Hook**可以解决*状态复用*。
>
> <2> **复杂的组件变得难以理解**：(如：componentDidMount 集成了很多互不相关的副作用)，**useState**可以解决。
>
> <3> JS 的 Class 和 this 不好理解，特别是对于非前端开发者；Hook 更符合**函数式编程**。

问 ❓：[React Hooks 是什么](https://zh-hans.reactjs.org/docs/hooks-overview.html#but-what-is-a-hook)？

> 每个 Hook 是一个特殊的函数，可以在函数组件中使用，它可以让你“钩入” **React state** 及**生命周期**等特性。
>
> 我们不需要特殊的 API 来读取 Hook —— 它已经保存在函数作用域中。
>
> Hook 使用了 JavaScript 的**闭包机制**。

问 ❓：Hook 的工作机制是怎样的？何时执行？

>

问 ❓：使用 Hook 有哪些限制？如何避免？

> 答：只能在**函数组件最外层**调用 Hook。不要在循环、条件判断或者子函数中调用。
>
> why：Hooks 的设计是基于*链表*实现。在调用时按顺序加入链表中，使用循环、条件或嵌套函数很有可能导致链表取值错位，执行错误的 Hook。
>
> 使用 Lint 工具库， “eslint-plugin-react-hooks” 完成自动检查。

问 ❓：生命周期方法要如何对应到 Hook？(ps:这个 react 官方文档的问题)（腾讯一面原题）

> useEffect 可以看作是 componentDidMount，componentDidUpdate 和 componentWillUnmount 这三个函数的组合。
>
> React.memo 等效于 PureComponent, 但它只比较 props，不比较 state.

如：

```js
const Button = React.memo((props) => {
  // 你的组件
});
```

问 ❓：setState 是同步还是异步？

> 答：React 控制的 setState 调用是异步更新，非 React 控制的调用是同步，如：setTimeout 的回调中。

问 ❓：如何使用 useEffect，让它只更新一次？（腾讯一面原题）

> 答：useEffect 默认**第一次渲染之后**和**每次更新之后**都会执行；
>
> 可以给它传递第二个参数“[]”，控制它只在**第一次挂载时**执行；
>
> 可以给他传递第二个参数“\[stateA, prop1\]”，则 stateA 或 prop1 发生变化后执行；

问 ❓：[useEffect 做了什么？有什么用？](https://zh-hans.reactjs.org/docs/hooks-effect.html#example-using-hooks)

> 答：告诉 React 组件需要在渲染后执行某些操作。
>
> 同一个函数组件中可以多次调用 useEffect，实现**关注点分离**。

问 ❓：React 何时清除 Effect？

> React 会在组件卸载的时候执行清除操作。
>
> Effect 的清除阶段在每次重新渲染时都会执行，防止因为更新导致 bug。

问 ❓：如何手动清除 Effect ?

> 答：让 useEffect 的回调函数返回一个**清除函数**。

如：

```js
function Fn(props) {
  useEffect(() => {
    function handleStatusChange(status) {
      setIsOnline(status.isOnline);
    }
    ChatAPI.subscribeToFriendStatus(props.friend.id, handleStatusChange);
    // Effect清除函数，React 将会在执行清除操作时调用它
    // 这样，移除订阅和上面的添加订阅逻辑放在一起了
    return function cleanup() {
      ChatAPI.unsubscribeFromFriendStatus(props.friend.id, handleStatusChange);
    };
  });
}
```

问 ❓：使用 useEffect 要注意什么？（腾讯一面原题）

> 答：

## 2. React Router

问 ❓：React Router 原理是什么？

> 把 Route 内的组件当作自己的子组件进行管理，通过监听 hashChange 和 popstate 事件来改变组件的显示隐藏。

## 3. Redux

问 ❓：Redux 的原理是什么？
