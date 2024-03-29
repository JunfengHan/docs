# JS 篇

## 1. 重要概念及 API 等

问 ❓：var、let、const 有什么区别？（腾讯一面原题）

> 答：let 和 const 在块级作用域内有效，let 和 const 的区别是 const 定义常量一样，定义后不可以重新赋值。
>
> 在代码执行编译时，var 定义的变量在赋值之前为 _undefined_，let 和 const 在赋值之前为*uninitialized*；
>
> let 和 const 定义的变量在赋值前不能访问，会**未定义**报错。

问 ❓：说一说什么是“执行上下文”？

> 答：
>
> <1><code style="color: #708090; background-color: #F5F5F5; font-size: 18px">执行上下文</code>分为三种，“全局上下文、“函数上下文”和“eval()上下文”
>
> <2>"执行上下文"在 JS 编译阶段形成，里面包含**变量环境**、**词法环境**、**outer（父级词法环境（作用域）等）**、**this**等
>
> <3>"执行上下文"保存在**栈**空间，只有在执行时存在；
>
> <4>**全局上下文**在 JS 代码执行完之前都是存在的，会被最先压入**栈底**，**函数上下文**在执行到函数时形成，会被<span style="color: #ff0000; font-size: 16px;">压入**栈**</span>，函数执行完从栈中弹出，该函数上下文即被清除.

[执行上下文详解](https://www.shushuo.me/#/js/deep?id=_33-%e6%89%a7%e8%a1%8c%e4%b8%8a%e4%b8%8b%e6%96%87)

问 ❓：for...in 与 for...of 区别？

> <code style="color: #708090; background-color: #F5F5F5; font-size: 18px">for...in</code>遍历对象除*Symbol*以外的**可枚举**属性,包括继承的可枚举属性；
>
> <code style="color: #708090; background-color: #F5F5F5; font-size: 18px">for...of</code>语句在**可迭代对象**上创建一个迭代循环；

_示例_

```js
Object.prototype.objCustom = function () {};
Array.prototype.arrCustom = function () {};

let iterable = [3, 5, 7];
iterable.foo = "hello";

for (let i in iterable) {
  console.log(i);
}
// 打印结果： 0, 1, 2, "foo", "arrCustom", "objCustom"
// objCustom 和 arrCustom 是 iterable 继承的属性
// foo 是直接定义在 iterable 的属性

for (let i of iterable) {
  console.log(i);
}
// 打印结果：3, 5, 7
```

_iterable 图示_

![for...in](./img/ask_js_forIn.png)

问 ❓：for 与 Array.prototype.forEach() 区别？

> 答：区别主要有两点：
>
> 1.<code style="color: #708090; background-color: #F5F5F5; font-size: 18px">forEach</code>是 Array.prototype 上的方法，只能循环数组；
>
> 2.<code style="color: #708090; background-color: #F5F5F5; font-size: 18px">for 循环</code>可以跳出循环，⚠️<code style="color: #708090; background-color: #F5F5F5; font-size: 18px">forEach</code>除了抛出异常以外，**没有办法中止或跳出 forEach() 循环**。；

问 ❓：Map 与 Object 区别？

> 答：1.Object 是无序的集合，Map 保证了遍历的有序性；
>
> 2.Map 可以使用任意数据类型作为“键”，Object 只能使用字符串；
>
> 3.Object 查询性能略好，Map 的增、删、改性能更好。

问 ❓：装饰器的原理是什么？

## 2. 异步与 Event Loop

问 ❓：下面代码打印结果是什么？为什么？（腾讯一面原题）

```js
Promise.resolve().then(() => {
  console.log("P1");
  setTimeout(() => {
    console.log("S2");
  }, 0);
});

setTimeout(() => {
  console.log("S1");
  Promise.resolve().then(() => {
    console.log("P2");
  });
}, 0);

console.log(1);
```

> 答：1, P1，S1，P2，S2。
>
> 1. 整个代码片段是个宏任务，进行第一个**事件循环**，开始执行里面代码，往<code style="color: #708090; background-color: #F5F5F5; font-size: 18px">微任务队列</code>添加了一个**微任务**（.then()里的回调函数，我们叫它 Promise1）；同时，往<code style="color: #708090; background-color: #F5F5F5; font-size: 18px">宏任务队列</code>添加了一个**宏任务**（下方 setTimeout 里的回调，我们叫它 Time1）
>
> 2. 执行到 console.log(1)，会直接打印 1；
>
> 3. 然后，依次执行<code style="color: #708090; background-color: #F5F5F5; font-size: 18px">微任务队列</code>里所有**微任务**，微任务只有一个 Promise1，所以会打印 "P1"；同时，添加一个新的**宏任务**（setTimeout 的回调，我们叫它 Time2）到宏任务队列
>
> 4. 执行完微任务 Promise1 后，<code style="color: #708090; background-color: #F5F5F5; font-size: 18px">宏任务队列</code>上有两个**宏任务**，分别是 Time1 和 Time2; 先执行 Time 1，会打印 "S1";同时，添加了一个**微任务**（我们叫他 Promise2）到<code style="color: #708090; background-color: #F5F5F5; font-size: 18px">微任务队列</code>；
>
> 5. 此时，<code style="color: #708090; background-color: #F5F5F5; font-size: 18px">微任务队列</code>有一个 Promise2；宏任务队列还有一个没执行的 Time2;先执行 Promise2，所以会打印“P2”;
>
> 6. 最后，只剩下 <code style="color: #708090; background-color: #F5F5F5; font-size: 18px">宏任务队列</code>上的 Time2，所以，打印“S2”.

升级版：

```js
Promise.resolve()
  .then(() => {
    console.log("P1");
    setTimeout(() => {
      console.log("S2");
    }, 0);
  })
  .then(() => {
    console.log("P3");
    setTimeout(() => {
      console.log("S3");
    }, 0);
  });

setTimeout(() => {
  console.log("S1");
  Promise.resolve().then(() => {
    console.log("P2");
  });
}, 0);

Promise.resolve()
  .then(() => {
    console.log("P4");
    setTimeout(() => {
      console.log("S4");
    }, 0);
  });

console.log(1);
```

> 打印：1，P1，P4，P3，S1，P2，S2，S4，S3
>
> 简述：
>
> 1. 先执行同步，往**微任务队列**添加“P1”和“P4”，往**宏任务队列**添加“S1”，打印 1 ，同步执行结束;
>
> 2. 然后，开始下一轮事件循环，执行**微任务队列**上的微任务，所以打印 P1和P4，同时把P3添加到**微任务队列**,然后添加 S2、S4 到**宏任务队列**；
> 3. 此时微任务队列有任务，继续执行微任务，打印 P3，添加 S3 到**宏任务队列**，此时**微任务队列**为空。
> 4. 然后依次执行，S1,S2,S4,S3。因为执行 S1 时，新增了微任务 P2,所以，打印顺序为 S1,P2,S2,S4,S3。

## 3. 手写实战

问 ❓：如何实现虚拟列表？

> 答：之所以需要虚拟列表，是因为展示长列表时会出现性能问题。
>
> 虚拟列表的核心是只展示部分需要展示的列表数据。
>
> [如何实现虚拟列表](https://juejin.cn/post/7132277540806213645)

问 ❓：能手写节流和防抖吗？

> 节流：多次触发，规定时间内只执行一次。如：鼠标滚轮缩放地图大小、长列表的滚动等。
>
> 防抖：多次触发，只执行最后一次。如：连续点击某个按钮，停止后再做操作，而不是点击一次操作一次。
>
> [节流和防抖手写](https://juejin.cn/post/6844903839322079240)
