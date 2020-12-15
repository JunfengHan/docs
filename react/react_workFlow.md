# React 创建

> 一步步看看React都做了些什么。

## 从 “Hello,World!” 开始

> 如果你不知道怎么使用React在屏幕显示一个“Hello,World!”,说明你不适合这篇文章，建议先从[这里](https://zh-hans.reactjs.org/)开始。

我们兴奋地开始写React,一切都是那么新奇。

```js
// 我会用React了
// import React from "react";
import ReactDOM from "react-dom";

function App () {
  return "Hello,World!";
}

ReactDOM.render(
  <App></App>,
  document.getElementById("root")
);
```

我们使用 ReactDom 的 render方法 在屏幕构建了一个视图，如下：

![hello](../_media/react_workFlow01.png)

打开浏览器的开发者工具，看看Performance 下的函数执行流程，别的先不管，直接从render方法开始看。这个render就是ReactDom模块里的render方法。

图2: react执行火焰图 🔥
![hello function flow](../_media/react_workFlow02.png)

我们来找找源码在哪里：

如果你是直接用npm 安装的 react 和 react-dom 等包的话，看到的就是下面这样的，这个是react团队打包放到npm供我们下载使用的。学习源码需要到github去拉取[react-源码](https://github.com/facebook/react),需要说明的是，react源码更新是直接更在master分支的，所以就算是同一个版本也可能略有差异。学习源码主要是思路，这点差异几乎是可以忽略的。

VS Code 按住Command 键，鼠标点击查看到的react-dom 包中 render方法：

![hello](../_media/react_workFlow03.png)

上面的操作是不是有点太详细（啰嗦）了 😊 ，为了不给某些新来的小伙伴造成阅读困难，这里感觉有必要说一下，后面说的源码是指github拉取的 v17.0.1。

去源码中查找这个方法：

-> 到react-dom包中 的 index.js 找到了 render 方法

```js
// packages/react-dom/index.js
export {
  ...
  render,
  ...
} from './src/client/ReactDOM';
```

-> 到ReactDOM.js看看这个方法

```js
// packages/react-dom/src/client/ReactDOM.js
import {
  render,
  ...
} from './ReactDOMLegacy';
```

-> 到ReactDOMLegacy 看看

<details>
  <summary>查看源码 💻</summary>

```js
// packages/react-dom/src/client/ReactDOMLegacy.js
export function render(
  element: React$Element<any>,
  container: Container,
  callback: ?Function,
) {
  ...
  return legacyRenderSubtreeIntoContainer(
    null,
    element,
    container,
    false,
    callback,
  );
}
```

</details>

一通乱找，发现render方法返回了 legacyRenderSubtreeIntoContainer 方法的返回值。

我们看下这个方法的源码：

<details>
  <summary>查看源码 💻</summary>

```js
// packages/react-dom/src/client/ReactDOMLegacy.js
function legacyRenderSubtreeIntoContainer(
  parentComponent: ?React$Component<any, any>,
  children: ReactNodeList,
  container: Container,
  forceHydrate: boolean,
  callback: ?Function,
) {
  ...
  let root: RootType = (container._reactRootContainer: any);
  let fiberRoot;
  if (!root) {
    // Initial mount
    root = container._reactRootContainer = legacyCreateRootFromDOMContainer(
      container,
      forceHydrate,
    );
    fiberRoot = root._internalRoot;
    if (typeof callback === 'function') {
      const originalCallback = callback;
      callback = function() {
        const instance = getPublicRootInstance(fiberRoot);
        originalCallback.call(instance);
      };
    }
    // Initial mount should not be batched.
    unbatchedUpdates(() => {
      updateContainer(children, fiberRoot, parentComponent, callback);
    });
  } else {
    fiberRoot = root._internalRoot;
    if (typeof callback === 'function') {
      const originalCallback = callback;
      callback = function() {
        const instance = getPublicRootInstance(fiberRoot);
        originalCallback.call(instance);
      };
    }
    // Update
    updateContainer(children, fiberRoot, parentComponent, callback);
  }
  return getPublicRootInstance(fiberRoot);
}
```

</details>

"legacyRenderSubtreeIntoContainer" 看看这个方法名是不是比较眼熟？

对，没错。就是上面图2中render方法下面的那个方法。

函数名称起的也很直白，直译过来就是“传统的渲染子树到容器中”。

图2中也可以看到，legacyRenderSubtreeIntoContainer 方法中分别调用了 legacyCreateRootFromDOMContainer 和 unbatchedUpdates 这两个方法。

所以我们兵分两路往下看。

### 第1路： legacyCreateRootFromDOMContainer

> 函数名直译过来是“传统的从DOMContainer创建Root”,这个Root是什么？

分解上面的legacyRenderSubtreeIntoContainer方法，看下这个代码片段：

<details>
  <summary>查看源码 💻</summary>

```js
let root: RootType = (container._reactRootContainer: any);
let fiberRoot;
if (!root) {
  // Initial mount
  root = container._reactRootContainer = legacyCreateRootFromDOMContainer(
    container,
    forceHydrate,
  );
  fiberRoot = root._internalRoot;
  if (typeof callback === 'function') {
    const originalCallback = callback;
    callback = function() {
      const instance = getPublicRootInstance(fiberRoot);
      originalCallback.call(instance);
    };
  }
  // Initial mount should not be batched.
  unbatchedUpdates(() => {
    updateContainer(children, fiberRoot, parentComponent, callback);
  });
}
```

</details>

初次挂载时root不存在，所以进入if判断，执行 legacyCreateRootFromDOMContainer，生成 root。

```js
fiberRoot = root._internalRoot;
```

可以看到这里得到了 <span style="color: #ff0000; font-size: 24px;">fiberRoot</span>,在后期调度更新的过程中这个节点非常重要。他就是 Fiber架构的产物。

仔细看看 root 和 fiberRoot 是怎样的吧 👇

<details>
  <summary>查看源码 💻</summary>

```js
// packages/react-dom/src/client/ReactDOMLegacy.js
function legacyCreateRootFromDOMContainer(
  container: Container,
  forceHydrate: boolean,
): RootType {
  const shouldHydrate =
    forceHydrate || shouldHydrateDueToLegacyHeuristic(container);
  // First clear any existing content.
  if (!shouldHydrate) {
    ...
  }
  if (__DEV__) {
    ...
  }

  return createLegacyRoot(
    container,
    shouldHydrate
      ? {
          hydrate: true,
        }
      : undefined,
  );
}
```

</details>

legacyCreateRootFromDOMContainer 调用了 createLegacyRoot

```js
// packages/react-dom/src/client/ReactDOMRoot.js
export function createLegacyRoot(
  container: Container,
  options?: RootOptions,
): RootType {
  return new ReactDOMBlockingRoot(container, LegacyRoot, options);
}
```

createLegacyRoot 调用了 ReactDOMBlockingRoot

```js
// packages/react-dom/src/client/ReactDOMRoot.js
function ReactDOMBlockingRoot(
  container: Container,
  tag: RootTag,
  options: void | RootOptions,
) {
  this._internalRoot = createRootImpl(container, tag, options);
}
```

注意了⚠️，this._internalRoot 就是 fiberRoot,也就是 createRootImpl方法的返回值。

<details>
  <summary>查看源码 💻</summary>

```js
// packages/react-dom/src/client/ReactDOMRoot.js
function createRootImpl(
  container: Container,
  tag: RootTag,
  options: void | RootOptions,
) {
  // Tag is either LegacyRoot or Concurrent Root
  const hydrate = options != null && options.hydrate === true;
  const hydrationCallbacks =
    (options != null && options.hydrationOptions) || null;
  const mutableSources =
    (options != null &&
      options.hydrationOptions != null &&
      options.hydrationOptions.mutableSources) ||
    null;
  const root = createContainer(container, tag, hydrate, hydrationCallbacks);
  markContainerAsRoot(root.current, container);

  const rootContainerElement =
    container.nodeType === COMMENT_NODE ? container.parentNode : container;
  listenToAllSupportedEvents(rootContainerElement);

  if (mutableSources) {
    for (let i = 0; i < mutableSources.length; i++) {
      const mutableSource = mutableSources[i];
      registerMutableSourceForHydration(root, mutableSource);
    }
  }

  return root;
}
```

</details>

createRootImpl方法的返回值是 createContainer 方法的返回值。
createRootImpl方法 还做了另外一件事，就是 <span style="color: #ffcc00">listenToAllSupportedEvents</span>。

```js
// packages/react-reconciler/src/ReactFiberReconciler.old.js
export function createContainer(
  containerInfo: Container,
  tag: RootTag,
  hydrate: boolean,
  hydrationCallbacks: null | SuspenseHydrationCallbacks,
): OpaqueRoot {
  return createFiberRoot(containerInfo, tag, hydrate, hydrationCallbacks);
}
```

createContainer 方法返回值是 createFiberRoot方法的返回值；

createFiberRoot 方法的作用应该很明显了，就是创建 FiberRoot;

<details>
  <summary>查看源码 💻</summary>

```js
export function createFiberRoot(
  containerInfo: any,
  tag: RootTag,
  hydrate: boolean,
  hydrationCallbacks: null | SuspenseHydrationCallbacks,
): FiberRoot {
  const root: FiberRoot = (new FiberRootNode(containerInfo, tag, hydrate): any);
  if (enableSuspenseCallback) {
    root.hydrationCallbacks = hydrationCallbacks;
  }

  // Cyclic construction. This cheats the type system right now because
  // stateNode is any.
  const uninitializedFiber = createHostRootFiber(tag);
  root.current = uninitializedFiber;
  uninitializedFiber.stateNode = root;

  initializeUpdateQueue(uninitializedFiber);

  return root;
}
```

</details>

createFiberRoot 方法做了两件事：

- 声明并返回了 root, root 是 FiberRootNode 构造函数的一个实例
- 执行了 initializeUpdateQueue 方法(mount 时初始化更新 queue)

<details>
  <summary>查看源码 💻</summary>

```js
export function createHostRootFiber(tag: RootTag): Fiber {
  let mode;
  if (tag === ConcurrentRoot) {
    mode = ConcurrentMode | BlockingMode | StrictMode;
  } else if (tag === BlockingRoot) {
    mode = BlockingMode | StrictMode;
  } else {
    mode = NoMode;
  }

  if (enableProfilerTimer && isDevToolsPresent) {
    // Always collect profile timings when DevTools are present.
    // This enables DevTools to start capturing timing at any point–
    // Without some nodes in the tree having empty base times.
    mode |= ProfileMode;
  }

  return createFiber(HostRoot, null, null, mode);
}
```

</details>

createHostRootFiber 返回了 createFiber的返回值。

```js
// packages/react-reconciler/src/ReactFiber.old.js
const createFiber = function(
  tag: WorkTag,
  pendingProps: mixed,
  key: null | string,
  mode: TypeOfMode,
): Fiber {
  // $FlowFixMe: the shapes are exact here but Flow doesn't like constructors
  return new FiberNode(tag, pendingProps, key, mode);
};
```

最终 createFiber 返回了一个 FiberNode构造函数的实例。

<details>
  <summary>查看源码 💻</summary>

```js
// packages/react-reconciler/src/ReactFiber.old.js
function FiberNode(
  tag: WorkTag,
  pendingProps: mixed,
  key: null | string,
  mode: TypeOfMode,
) {
  // Instance
  this.tag = tag;
  this.key = key;
  this.elementType = null;
  this.type = null;
  this.stateNode = null;

  // Fiber
  this.return = null;
  this.child = null;
  this.sibling = null;
  this.index = 0;

  this.ref = null;

  this.pendingProps = pendingProps;
  this.memoizedProps = null;
  this.updateQueue = null;
  this.memoizedState = null;
  this.dependencies = null;

  this.mode = mode;

  // Effects
  this.flags = NoFlags;
  this.nextEffect = null;

  this.firstEffect = null;
  this.lastEffect = null;
  this.subtreeFlags = NoFlags;
  this.deletions = null;

  this.lanes = NoLanes;
  this.childLanes = NoLanes;

  this.alternate = null;

  if (enableProfilerTimer) {
    ...
  }

  if (__DEV__) {
    ...
  }
}
```

</details>

最终，构造函数 FiberNode 构造了一个 fiberRoot.

___

我们上面说了createRootImpl方法还做了另一件事，listenToAllSupportedEvents。

<details>
  <summary>查看源码 💻</summary>

```js
export function listenToAllSupportedEvents(rootContainerElement: EventTarget) {
  if (!(rootContainerElement: any)[listeningMarker]) {
    (rootContainerElement: any)[listeningMarker] = true;
    allNativeEvents.forEach(domEventName => {
      // We handle selectionchange separately because it doesn't bubble and needs to be on the document.
      if (domEventName !== 'selectionchange') {
        if (!nonDelegatedEvents.has(domEventName)) {
          listenToNativeEvent(domEventName, false, rootContainerElement);
        }
        listenToNativeEvent(domEventName, true, rootContainerElement);
      }
    });
    const ownerDocument =
      (rootContainerElement: any).nodeType === DOCUMENT_NODE
        ? rootContainerElement
        : (rootContainerElement: any).ownerDocument;
    if (ownerDocument !== null) {
      // The selectionchange event also needs deduplication
      // but it is attached to the document.
      if (!(ownerDocument: any)[listeningMarker]) {
        (ownerDocument: any)[listeningMarker] = true;
        listenToNativeEvent('selectionchange', false, ownerDocument);
      }
    }
  }
}
```

</details>

这里单独处理了不会冒泡的“selectionchange”事件，然后执行了 listenToNativeEvent。

<details>
  <summary>查看源码 💻</summary>

  ```js
  export function listenToNativeEvent(
    domEventName: DOMEventName,
    isCapturePhaseListener: boolean,
    target: EventTarget,
  ): void {
    if (__DEV__) {
      ...
    }

    let eventSystemFlags = 0;
    if (isCapturePhaseListener) {
      eventSystemFlags |= IS_CAPTURE_PHASE;
    }
    addTrappedEventListener(
      target,
      domEventName,
      eventSystemFlags,
      isCapturePhaseListener,
    );
  }
  ```

</details>

listenToNativeEvent 方法调用了 addTrappedEventListener。

addTrappedEventListener 方法经过一系列的判断会调用 “packages/react-dom/src/events/EventListener.js” 文件中的方法，EventListener.js 里的方法就是给元素绑定原生的**监听事件**。

<details>
  <summary>查看源码 💻</summary>

  ```js
  export function addEventBubbleListener(
    target: EventTarget,
    eventType: string,
    listener: Function,
  ): Function {
    target.addEventListener(eventType, listener, false);
    return listener;
  }
  ```

</details>

### 第2路： unbatchedUpdates

> "unbatchedUpdates" 可以直译为“无批量更新”。

<details>
  <summary>查看源码 💻</summary>

  ```js
  // packages/react-reconciler/src/ReactFiberReconciler.old.js
  export function updateContainer(
    element: ReactNodeList,
    container: OpaqueRoot,
    parentComponent: ?React$Component<any, any>,
    callback: ?Function,
  ): Lane {
    if (__DEV__) {
      onScheduleRoot(container, element);
    }
    const current = container.current;
    // 当前时间
    const eventTime = requestEventTime();
    if (__DEV__) {
      ...
    }
    // requestUpdateLane 调用 findUpdateLane ，
    // findUpdateLane 会根据传过来的参数匹配计算，并返回优先级的值，值越大优先级越高；
    // 这个是 v17 中重要的 lane架构。
    const lane = requestUpdateLane(current);

    if (enableSchedulingProfiler) {
      markRenderScheduled(lane);
    }

    const context = getContextForSubtree(parentComponent);
    if (container.context === null) {
      container.context = context;
    } else {
      container.pendingContext = context;
    }

    if (__DEV__) {
      ...
    }

    const update = createUpdate(eventTime, lane);
    // Caution: React DevTools currently depends on this property
    // being called "element".
    update.payload = {element};

    callback = callback === undefined ? null : callback;
    if (callback !== null) {
      if (__DEV__) {
        ...
      }
      update.callback = callback;
    }

    enqueueUpdate(current, update);
    scheduleUpdateOnFiber(current, lane, eventTime);

    return lane;
  }
  ```

</details>

updateContainer 方法做了很多，主要是scheduleUpdateOnFiber。

**scheduleUpdateOnFiber** 里调用了 <span style="color: #ff0000; font-size: 24px;"> performSyncWorkOnRoot </span> 方法， performSyncWorkOnRoot 里调用了<span style="color: #ff0000; font-size: 14px;">renderRootSync方法</span>和<span style="color: #ff0000; font-size: 14px;">commitRoot方法</span>。

performSyncWorkOnRoot开始了 render 阶段。

commitRoot 开始了commit阶段。

#### render 阶段

> render阶段开始与 performSyncWorkOnRoot方法或 performConcurrentWorkOnRoot 方法，取决于本次更新是同步更新还是异步更新。显然，初始化属于同步更新。

<details>
  <summary>查看源码 💻</summary>

  ```js
  // packages/react-reconciler/src/ReactFiberWorkLoop.old.js的 scheduleUpdateOnFiber 方法
  // 判断是不是同步，执行不同逻辑
  if (lane === SyncLane) {
      if (
        // Check if we're inside unbatchedUpdates
        (executionContext & LegacyUnbatchedContext) !== NoContext &&
        // Check if we're not already rendering
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
      ...
      // Schedule other updates after in case the callback is sync.
      ensureRootIsScheduled(root, eventTime);
      schedulePendingInteractions(root, lane);
    }
  ```

</details>

从判断可以看出，同步的话会做进一步的处理，非同步的话会直接执行 ensureRootIsScheduled 方法。

**ensureRootIsScheduled 方法里可能会调用 performConcurrentWorkOnRoot，它是每一个并发任务的切入点，即任何通过Scheduler进行的任务。**

我们先看看 performSyncWorkOnRoot 方法做了什么？

- renderRootSync: 主要是执行 workLoopSync
- commitRoot: 启动 commit 阶段

再看看 performConcurrentWorkOnRoot 做了什么？

- renderRootConcurrent：主要执行 
- ...

```js
// performSyncWorkOnRoot会调用该方法
function workLoopSync() {
  // Already timed out, so perform work without checking if we need to yield.
  while (workInProgress !== null) {
    performUnitOfWork(workInProgress);
  }
}

// performConcurrentWorkOnRoot会调用该方法
function workLoopConcurrent() {
  // Perform work until Scheduler asks us to yield
  while (workInProgress !== null && !shouldYield()) {
    performUnitOfWork(workInProgress);
  }
}
```

可以看到，他们唯一的区别是是否调用shouldYield。如果当前浏览器帧没有剩余时间，shouldYield会中止循环，直到浏览器有空闲时间后再继续遍历。

workInProgress代表当前已创建的workInProgress fiber。

performUnitOfWork方法会创建下一个Fiber节点并赋值给workInProgress，并将workInProgress与已创建的Fiber节点连接起来构成<span style="color: #ff0000; font-size: 14px;">Fiber树</span>。

**Fiber Reconciler**通过遍历的方式实现可中断的递归，所以performUnitOfWork的工作可以分为两部分：“递”和“归”。

**1> “递”阶段**

首先从rootFiber开始向下深度优先遍历。为遍历到的每个Fiber节点调用 ***beginWork方法***。

该方法会根据传入的Fiber节点创建子Fiber节点，并将这两个Fiber节点连接起来。

当遍历到叶子节点（即没有子组件的组件）时就会进入“归”阶段。

<details>
  <summary>查看performUnitOfWork源码 💻</summary>

  ```js
  // packages/react-reconciler/src/ReactFiberWorkLoop.old.js
  function performUnitOfWork(unitOfWork: Fiber): void {
    ...
    let next;
    ...
    next = beginWork(current, unitOfWork, subtreeRenderLanes);
    ...
    if (next === null) {
      completeUnitOfWork(unitOfWork);
    } else {
      workInProgress = next;
    }

    ReactCurrentOwner.current = null;
  }
  ```

</details>

<details>
  <summary>查看beginWork源码 💻</summary>

  ```js
  // packages/react-reconciler/src/ReactFiberBeginWork.old.js
  function beginWork(
    current: Fiber | null,
    workInProgress: Fiber,
    renderLanes: Lanes,
  ): Fiber | null {
    ...
  }
  ```

</details>

beginWork 做了很多，又是一个400多行的方法。

**2> “归”阶段**

在“归”阶段会调用 ***completeWork*** 处理Fiber节点。

当某个Fiber节点执行完completeWork，如果其存在兄弟Fiber节点（即fiber.sibling !== null），会进入其兄弟Fiber的“递”阶段。

如果不存在兄弟Fiber，会进入父级Fiber的“归”阶段。

“递”和“归”阶段会交错执行直到“归”到rootFiber。至此，render阶段的工作就结束了。

**3> 示例**

```js
function App() {
  return (
    <div>
      i am
      <span>boy</span>
    </div>
  )
}

ReactDOM.render(<App />, document.getElementById("root"));
```

对应的Fiber树结构：

![Fiber 树结构](../_media/react_workFlow05.png)

render阶段的 beginWork 会执行：

```js
1. rootFiber beginWork
2. App Fiber beginWork
3. div Fiber beginWork
4. "i am" Fiber beginWork
5. "i am" Fiber completeWork
6. span Fiber beginWork
7. span Fiber completeWork
8. div Fiber completeWork
9. App Fiber completeWork
10. rootFiber completeWork
```



#### commit 阶段

> commit阶段从 commitRoot 方法的调用开始。

commitRoot 方法主要是调用了commitRootImpl方法，commitRootImpl方法做了很多事，这个方法总共有400行左右代码。

<details>
  <summary>查看源码 💻</summary>

  ```js
  // packages/react-reconciler/src/ReactFiberWorkLoop.old.js
  function commitRootImpl(root, renderPriorityLevel) {
    do {
      ...
      flushPassiveEffects();
    } while (rootWithPendingPassiveEffects !== null);
    flushRenderPhaseStrictModeWarningsInDEV();
    ...
  }
  ```

</details>




