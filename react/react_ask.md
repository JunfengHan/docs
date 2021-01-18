# React 问答

> 通过类似问答的形式来总结React中常见的问题。

## 1. setState 是同步还是异步？

> 答： 不一定。

React17目前一共有3种模式：

- legacy 模式：ReactDOM.render(<App />, rootNode)。这是当前 React app 使用的方式，不支持React某些新功能
- blocking 模式： ReactDOM.createBlockingRoot(rootNode).render(<App />)。实验中，作为迁移到 concurrent 模式的第一个步骤，包含部分新特性
- concurrent 模式： ReactDOM.createRoot(rootNode).render(<App />)。实验中，未来稳定之后，打算作为 React 的默认开发模式。这个模式开启了所有的新功能。

legacy 模式是我们目前常用的模式，这种模式下，setState是异步更新。

```js
import React from "react";
import ReactDOM from "react-dom";

class App extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      num: 0
    };

    this.changeNum = this.changeNum.bind(this);
  }

  changeNum() {
    console.log("num1:", this.state.num);
    this.setState({ num: this.state.num + 1 });
    this.setState({ num: this.state.num + 1 });
    this.setState({ num: this.state.num + 1 });
    console.log("num2:", this.state.num);

    setTimeout(() => {
      console.log("num3:", num);
      this.setState({ num: this.state.num + 1 });
      console.log("num4:", num);
    });
  }

  render() {
    return <div onClick={this.changeNum}>{this.state.num}</div>;
  }
}

const rootElement = document.getElementById("root");
ReactDOM.render(
  <React.StrictMode>
    <App />
  </React.StrictMode>,
  rootElement
);

// 第1次点击
num1: 0
num2: 0
num3: 1 // 说明setTimeout 里的state是改变后的
num4: 2 // 说明setTimeout 里的setState是同步执行的
```

**为什么执行3次 this.setState({ num: this.state.num + 1 })，num 没有每次增加3，而是每次加1？**

因为React 有个性能优化，会批量处理同一个事件中多次调用setState为一次，这叫做批处理。源码实现的方法是 batchedUpdates()。

```js
// React v17.0.2
// packages/react-reconciler/src/ReactFiberWorkLoop.old.js
export function batchedUpdates<A, R>(fn: A => R, a: A): R {
  const prevExecutionContext = executionContext;
  executionContext |= BatchedContext;
  try {
    return fn(a);
  } finally {
    executionContext = prevExecutionContext;
    if (executionContext === NoContext) {
      // Flush the immediate callbacks that were scheduled during this batch
      resetRenderTimer();
      flushSyncCallbackQueue();
    }
  }
}
```

React每次更新都会执行 scheduleUpdateOnFiber(fiber,lane,eventTime) 这个函数。

scheduleUpdateOnFiber接收的参数 lane 代表更新方式，legacy 模式下的更新方式是 SyncLane。

因此，scheduleUpdateOnFiber(fiber,lane,eventTime) 函数中会进入判断 if (lane === SyncLane) {} else {};

满足 executionContext === NoContext 时，会进入 if (executionContext === NoContext) {}，此时会同步更新状态的值;

什么情况会满足 executionContext === NoContext 呢？

executionContext 不存在时才会满足，setTimeout里执行的 this.setState(),此时满足executionContext === NoContext。

所以

```ts
// 源码地址：packages/react-reconciler/src/ReactFiberWorkLoop.old.js
export function scheduleUpdateOnFiber(
  fiber: Fiber,
  lane: Lane,
  eventTime: number,
) {
  ...
  if (lane === SyncLane) {
    if (
      (executionContext & LegacyUnbatchedContext) !== NoContext &&
      (executionContext & (RenderContext | CommitContext)) === NoContext
    ) {
      // Register pending interactions on the root to avoid losing traced interaction data.
      schedulePendingInteractions(root, lane);

      // This is a legacy edge case. The initial mount of a ReactDOM.render-ed
      // root inside of batchedUpdates should be synchronous, but layout updates
      // should be deferred until the end of the batch.
      performSyncWorkOnRoot(root);
    } else {
      ensureRootIsScheduled(root, eventTime);
      schedulePendingInteractions(root, lane);
      if (executionContext === NoContext) {
        // Flush the synchronous work now, unless we're already working or inside
        // a batch. This is intentionally inside scheduleUpdateOnFiber instead of
        // scheduleCallbackForFiber to preserve the ability to schedule a callback
        // without immediately flushing it. We only do this for user-initiated
        // updates, to preserve historical behavior of legacy mode.
        resetRenderTimer();
        flushSyncCallbackQueue();
      }
    }
  } else {
    // Schedule a discrete update but only if it's not Sync.
    if (
      (executionContext & DiscreteEventContext) !== NoContext &&
      // Only updates at user-blocking priority or greater are considered
      // discrete, even inside a discrete event.
      (priorityLevel === UserBlockingSchedulerPriority ||
        priorityLevel === ImmediateSchedulerPriority)
    ) {
      // This is the result of a discrete event. Track the lowest priority
      // discrete update per root so we can flush them early, if needed.
      if (rootsWithPendingDiscreteUpdates === null) {
        rootsWithPendingDiscreteUpdates = new Set([root]);
      } else {
        rootsWithPendingDiscreteUpdates.add(root);
      }
    }
    // Schedule other updates after in case the callback is sync.
    ensureRootIsScheduled(root, eventTime);
    schedulePendingInteractions(root, lane);
  }
  ...
}
```

**使用concurrent模式：**

```js
// 使用 concurrent 模式
// 注意，unstable_createRoot只存在于实验版本中，如 v16.9.0-alpha.0
ReactDOM.unstable_createRoot(rootElement).render(<App />);

// 第1次点击
num1: 0
num2: 0
num3: 1 // 说明setTimeout 里的state是改变后的
num4: 1 // 说明setTimeout 里的setState是异步执行的
```

*结论：*

- legacy 模式 setState 是异步的
- 因为是异步更新，所以不能依赖当前state计算下一个state
- 同一个事件中连续多次调用setState，会被批处理成一次
- setTimeout里的setState是同步执行的，且state是组件更新之后的值
- concurrent模式下，所有的setState都是异步执行的

**题外话：**

setState是同步还是异步对我们来说没那么重要，因为如果我们想要在class组件中获取状态更新后的值，可以使用 componentDidMount()和componentDidUpdate()中获取到。

如果想在函数组件中获取，可以使用useEffect()的回调中来获取。

所以，setState是不是异步无所谓。

___

## 2. ？

> 