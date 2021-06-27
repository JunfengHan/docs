# React 架构

## 1. React

React17 架构可以分为三层：

- Scheduler（调度器）—— 调度任务的优先级，高优任务优先进入 Reconciler
- Reconciler（协调器）—— 负责找出变化的组件
- Renderer（渲染器）—— 负责将变化的组件渲染到页面上

![react架构工作流](../img/react_architecture.png)

## 2. Scheduler（调度器）-- 调度更新

> 在上一篇文章《React 理念》中我们得到一个结论：React 想要“将同步的更新变为可中断的异步更新”。

**调度器的作用：？**

**调度更新，高优先级的更新会调度给<code style="color: #708090; background-color: #F5F5F5; font-size: 18px">协调器</code>。**

既然我们以浏览器是否有剩余时间作为任务中断的标准，那么我们需要一种机制，当浏览器有剩余时间时通知我们。

浏览器还真有个 API [requestIdleCallback
](https://developer.mozilla.org/zh-CN/docs/Web/API/Window/requestIdleCallback) 可以做这样的事，可惜的是它目前是个实验性质的 API，存在兼容性等诸多问题。

最终 React 放弃使用，原因主要是：

- 浏览器兼容性
- 触发频率不稳定，受很多因素影响

基于以上原因，React 实现了功能更完备的 requestIdleCallback polyfill，这就是 <span style="color: #ff5050">Scheduler</span>。

Scheduler 是一个独立于 React 的库。

## 3. Reconciler（协调器）-- 决定更新什么组件

React15 中 Reconciler 是递归处理虚拟 DOM 的，这样的协调算法我们称为 Stack Reconciler。

React 从 v16 版本开始对 Stack Reconciler 进行的重写，他就是<span style="color: #ff5050">React Fiber</span>,是 v16 版本的核心算法实现。这个就是 Fiber Reconciler，称为 <span style="color: #ff0000; font-size: 16px;">Fiber 架构</span>。

在 React16 中，Reconciler 与 Renderer 不再是交替工作。

当 Scheduler 将任务交给 Reconciler 后，Reconciler 会为变化的虚拟 DOM 打上代表增/删/更新的标记，类似这样：

```js
export const Placement = /*             */ 0b0000000000010;
export const Update = /*                */ 0b0000000000100;
export const PlacementAndUpdate = /*    */ 0b0000000000110;
export const Deletion = /*              */ 0b0000000001000;
```

整个**Scheduler**与**Reconciler**的工作都在内存中进行。

只有当一次更新中所有组件都完成**Reconciler**的工作，才会统一交给**Renderer**。

## 4. Renderer（渲染器）-- 将组件渲染到视图

Renderer 根据 Reconciler 为虚拟 DOM 打的标记，同步执行对应的 DOM 操作。

假如点击按钮，state 会改变：

![react架构工作流](../img/react_flow.png)

- 点击按钮，setState() 导致 state 变化
- 调度器接收到更新，调度更细优先级，将新的 state 给 Reconciler
- Reconciler （Fiber 架构） 接收到更新，计算出虚拟 DOM 变化，给虚拟 DOM 打上标记并交给 Renderer
- Renderer 接收到通知，更新需要更新的 DOM

其中红框中的步骤随时可能由于以下原因被中断：

- 有其他更高优任务需要先更新
- 当前帧没有剩余时间

由于红框中的工作都在内存中进行，不会更新页面上的 DOM，所以即使反复中断，用户也不会看见更新不完全的 DOM。

> 注：本文内容主要来自下方链接，算是一个学习笔记，请知悉。

## 参考

[React 技术揭秘](https://react.iamkasong.com/preparation/newConstructure.html#react16%E6%9E%B6%E6%9E%84)

[How to build your own renderer from scratch?](https://blog.atulr.com/react-custom-renderer-1/)
