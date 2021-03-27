# 内存管理

> C 语言这样的底层语言一般都有底层的内存管理接口，而 JavaScript 没有。

JavaScript 是在创建变量（对象，字符串等）时自动进行了分配内存，并且在不使用它们时<code style="color: #708090; background-color: #F5F5F5; font-size: 18px">“自动”</code>释放。

释放的过程称为<code style="color: #708090; background-color: #F5F5F5; font-size: 18px">垃圾回收</code>。

**这个“自动”是混乱的根源**，并让 JavaScript（和其他高级语言）开发者错误的感觉他们可以不关心内存管理。

## 1. 内存生命周期

不管什么程序语言，内存生命周期基本是一致的:

- 1. 分配你所需要的内存
- 2. 使用分配到的内存（读、写）
- 3. 不需要时将其释放\归还

### 1.1 JavaScript 的内存分配

#### 1.1.1 值的初始化

JavaScript 在**定义变量时就完成了内存分配**。

```js
var n = 123; // 给数值变量分配内存
var s = "azerty"; // 给字符串分配内存

var o = {
  a: 1,
  b: null,
}; // 给对象及其包含的值分配内存

// 给数组及其包含的值分配内存（就像对象一样）
var a = [1, null, "abra"];

function f(a) {
  return a + 2;
} // 给函数（可调用的对象）分配内存

// 函数表达式也能分配一个对象
someElement.addEventListener(
  "click",
  function () {
    someElement.style.backgroundColor = "blue";
  },
  false
);
```

### 1.2 通过函数调用分配内存

```js
// 有些函数调用结果是分配对象内存
var d = new Date(); // 分配一个 Date 对象
var e = document.createElement("div"); // 分配一个 DOM 元素

// 有些方法分配新变量或者新对象
var s = "azerty";
/* 因为字符串是不变量，
 * JavaScript 可能决定不分配内存，
 * 只是存储了 [0-3] 的范围。
 */
var s2 = s.substr(0, 3); // s2 是一个新的字符串
var a = ["ouais ouais", "nan nan"];
var a2 = ["generation", "nan nan"];
// 新数组有四个元素，是 a 连接 a2 的结果
var a3 = a.concat(a2);
```

### 1.3 当内存不再需要使用时释放

> 大多数内存管理的问题都在这个阶段.

释放内存的关键点是：**哪些被分配的内存确实已经不再需要了！**

这件事往往需要开发人员来决定。

高级语言解释器嵌入了**垃圾回收器**，它的主要工作是：**跟踪内存的分配和使用，以便当分配的内存不再使用时，自动释放它**。

可惜的是，准确地知道是否仍然需要某块内存是**无法判定的**。

## 2. 垃圾回收

<code style="color: #708090; background-color: #F5F5F5; font-size: 18px">垃圾回收</code>的实现只能有限制的解决内存释放问题。

### 2.1 引用

垃圾回收算法主要依赖于<code style="color: #708090; background-color: #F5F5F5; font-size: 18px">引用</code>的概念。

引用举例：

- 一个 Javascript 对象具有对它原型的引用（隐式引用），和对它属性的引用（显式引用）
- 函数作用域的引用

### 2.2 垃圾回收算法

垃圾回收机制主要有两种：

- 引用计数垃圾收集（早期算法，基本淘汰）：
  “对象是否不再需要”简化定义为“对象有没有其他对象引用到它”。如果没有引用指向该对象（零引用），对象将被垃圾回收机制回收。

  缺点：循环引用会造成内存泄漏

- 标记-清除算法（现在的主流算法）：

  假定设置一个叫做根（root）的对象（在 Javascript 里，根是全局对象）。垃圾回收器将定期从根开始，找所有从根开始引用的对象，然后找这些对象引用的对象……从根开始，垃圾回收器将找到所有可以获得的对象和收集所有不能获得的对象。

**说明：**

> 本文内容主要来之下面的参考链接，有需要请查看原文

## 参考

[内存管理 -- MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Memory_Management)
