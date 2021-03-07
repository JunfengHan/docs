# React理念
> 本文会根据官方文档的介绍顺序来尝试读源码。先分析react的理念显然是有必要的，如果不知道理念的话读别人写的代码会很累。不知道原作者的理念的话读起来一切都是在猜。

## 1.React是做什么的？
“用于构建用户界面的 JavaScript 库”，官方文档是这样给React定义的。

"React 是用 JavaScript 构建**快速响应**的大型 Web 应用程序的首选方式",官方文档React哲学里这样说。

React所做的努力与尝试都是为了**快速响应**。

## 2.React的特征是什么？
- 声明式：与声明式相对应的是“命令式”，声明式只用声明自己想要的UI，至于插入渲染等交给React。命令式就像原生JS编程，每一步详细的操作命令都要清晰地表达出来。

- 组件化：组件化解决复用组件的问题。一个个各有状态的组件可以组件出复杂的应用，同时有利于代码管理。

- 一次学习，随处编写：我们用JSX构建React组件，JSX 可以生成 React “元素”。React “元素”可以用react-dom渲染出web应用，可以用React Native 渲染出移动端应用，还可以使用 Node 进行服务器渲染

```javascript
//  ReactDOM 渲染web应用，<App />是个组件，使用了JSX语法，返回一个React"元素"
import React from "react";
import ReactDOM from "react-dom";
import GlobalStyle from './globalStyle.js';
import App from "./App.js";

ReactDOM.render(
  <App />
  document.getElementById("root")
);
```

## 3.React是如何做到快速响应的？
想知道如何快速响应，就要先知道我们访问Web网站和使用APP时影响响应速度的关键是什么：
- CPU性能瓶颈
- IO

### 3.1 如何解决CPU瓶颈
项目变大，组件变多，需要做大量计算时就容易遇到CPU瓶颈。

例如：向试图渲染3000个li:

```javascript
function App() {
  const len = 3000;
  return (
    <ul>
      {Array(len).fill(0).map((_, i) => <li>{i}</li>)}
    </ul>
  );
}

const rootEl = document.querySelector("#root");
ReactDOM.render(<App/>, rootEl); 

```

主流浏览器刷新频率为60Hz，即每（1000ms / 60Hz）16.6ms浏览器刷新一次。

浏览器的GUI渲染线程与JS线程是互斥的，也就是说二者不能同时执行。所以，JS脚本执行时，浏览器渲染、绘制就要暂停。

如果在16.6ms 内完成以下任务就可能不会出现卡顿：
> JS脚本执行  > 样式布局 > 样式绘制

看下例子的执行情况如何：

![react执行性能火焰图](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/fa51f399dda1425a8f796f498a17d7f2~tplv-k3u1fbpfcp-watermark.image)

可以看出，JS执行时间为291.91ms,远远超出了16.6ms，多于一帧时间后就会出现卡顿现象，ms级别的卡顿你可能没有察觉，如果改成30000次呢？

### 3.2 如何解决卡顿问题？
> 答案是：在浏览器每一帧的时间中，预留一些时间给JS线程，React 利用这部分时间更新组件（源码中可以看到预留时间为5ms）。

```javascript
// react/packages/scheduler/src/forks/SchedulerDOM.js
let yieldInterval = 5;
```
当预留的时间不够用时，React将线程控制权交还给浏览器使其有时间渲染UI，React则等待下一帧时间到来继续被中断的工作。

这种将长任务分拆到每一帧中，像蚂蚁搬家一样一次执行一小段任务的操作，被称为**时间切片**（time slice）

接下来我们开启Concurrent Mode：<span style="color: #ff9966">开启后会启用**时间切片**</span>

```javascript
// 通过使用ReactDOM.unstable_createRoot开启Concurrent Mode
// ReactDOM.render(<App/>, rootEl);  
// 该特性目前在实验阶段，稳定版本中尚不可用
ReactDOM.unstable_createRoot(rootEl).render(<App/>);
```

**时间切片**的关键是：<span style="color: #ff9966">将同步的更新变为可中断的异步更新</span>。

### 3.3 如何解决IO瓶颈？

**网络延迟**是前端开发者无法解决的。

前端可以减少用户对网络延迟的感知，达到“欺骗”没有延迟的效果。

React给出的答案是<span style="color: #ff9966">将人机交互研究的结果整合到真实的 UI 中</span>。

简单说就是延迟跳转，利用延迟的时间去请求数据，用户看到的跳转页面是提前请求过数据的，而不是先跳转过去再请求数据。

为此，React实现了Suspense (opens new window)功能及配套的hook——useDeferredValue (opens new window)。

而在源码内部，为了支持这些特性，同样需要将同步的更新变为可中断的异步更新。

## 参考

[React技术揭秘](https://react.iamkasong.com/preparation/idea.html)