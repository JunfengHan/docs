# 模块化

> 模块化使大型前端项目的管理起来更加容易，代码更加高效。

## 1.模块化历程

- Step1: 没有模块化的年代

那是个古老的年代，前端在整个应用中的作用远不如现在，代码相对较少，比较容易管理。

不同的文件使用 \<script\> 标签来区分。

- Step2: CommonJS

随着 JS 程序越来越复杂，有必要考虑提供一种<span style="color: #ff0000; font-size: 16px;">将 JavaScript 程序拆分为**可按需导入**的**单独模块**的机制</span>。

<code style="color: #708090; background-color: #F5F5F5; font-size: 18px">CommonJS</code> 是伴随着 Node.js 出现的，是 Node.js 的模块化方案。

- Step3: AMD 与 CMD

然后出现了基于 <code style="color: #708090; background-color: #F5F5F5; font-size: 18px">AMD</code>(Asynchronous Module Definition) 规范的模块系统，如：**RequireJS**.

**缺陷：**

> 必须指定所有的依赖项 ，这些依赖项会被当做形参传到 factory 中,对于依赖的模块会提前执行，这被称为：**依赖前置**。（在 RequireJS 2.0 也可以选择延迟执行）

**依赖前置**会导致引入的另一个模块中的内容是条件性执行的。

于是出现了 <code style="color: #708090; background-color: #F5F5F5; font-size: 18px">CMD</code>规范和基于它的库 <code style="color: #708090; background-color: #F5F5F5; font-size: 18px">SeaJS</code>。

与 AMD 规范最大的不同点：**CMD 推崇依赖就近 + 延迟执行**.

因为就近引入规则（用到时随时引入），<code style="color: #708090; background-color: #F5F5F5; font-size: 18px">CMD</code>的缺点是：**依赖关系不直观**。

经过不断的迭代与发展，AMD 和 CMD 如今的功能基本相同。

- Step4: ES6 Module

**CommonJS**主要基于后端，**AMD**、**CMD**主要基于前端。

ES6 标准新增了**模块体系**，我们叫它<code style="color: #708090; background-color: #F5F5F5; font-size: 18px">ES6 Module</code>.

<code style="color: #708090; background-color: #F5F5F5; font-size: 18px">ES6 Module</code>是 JS 语言层的模块化方案，因此，可以使用在前端和后端。

> 随着 Node.js 的不断壮大，CommonJS 活了下来；由于 ES Module 的官方属性，且挺好用，所以，前端目前基本被它统一。

## 2. 前端为何不用 CommonJS

既然<code style="color: #708090; background-color: #F5F5F5; font-size: 18px">CommonJS</code>在 Node.js 中用的顺风顺水，为什么前端还要自己搞那么多方案出来？

浏览器不兼容 CommonJS 直接原因是：**缺少 Node.js 环境变量**

- module
- exports
- require
- global

<code style="color: #708090; background-color: #F5F5F5; font-size: 18px">CommonJS</code> 的**require()**语法是<span style="color: #ff0000; font-size: 16px;">同步</span>的。

Node.js 在服务端，他的模块是一个个文件，只需从本地硬盘读取即可，而且有**缓存**，所以速度很快，**同步是可以满足需求的**。

浏览器端就不一样了，文件一般存放在**服务器**或者**CDN**上，如果使用同步的方式加载一个模块还需要由网络来决定快慢，可能时间会很长，这样浏览器很容易进入“假死状态”。所以才有了后面的 AMD 和 CMD 模块化方案，二者都是<span style="color: #ff0000; font-size: 16px;">异步</span>的。

显然，**ES Module 也是异步加载模块的**。

## 3. 如何使用

### 3.1 CommonJS

**导出**：

使用 <code style="color: #708090; background-color: #F5F5F5; font-size: 18px">exports</code> 或 <code style="color: #708090; background-color: #F5F5F5; font-size: 18px">module.exports</code>

_CommonJS 导入导出示例_

```js
exports.count = 1;

// 两者等效
module.exports = {
  count: 1,
};
```

**exports 与 module.exports 的关系**：

```js
// exports 只是 module.exports 的一个引用
exports === module.exports; // true

// 说明 exports 初始化时是个空对象
console.log(exports); // {}
```

CommonJS <span style="color: #ff0000; font-size: 16px;">真正控制模块导出的是 module.exports</span>，exports 只是 module.exports 决定导出一个对象时的一个快捷方式。

```js
// 这样 exports 就没意义了，因为它已经不指向 module.exports了，即：断开了和 module.exports 的关系
exports = 4;

// 这样是有意义的，只不过引用该模块只能获得一个数字4
module.exports = 4;
```

**引入**：

```js
// 获取 module.js 模块导出的变量
let { count } = require("./module");

// 相当于
let moduleCount = require("./module");
let count = moduleCount.count;
```

CommonJS 的具体工作原理看[这里](https://www.shushuo.me/#/node/base?id=_9-%e6%a8%a1%e5%9d%97%e7%b3%bb%e7%bb%9f%ef%bc%88commonjs%ef%bc%89)

### 3.2 ES6 Module

#### 3.2.1 导出（export）

分别使用<code style="color: #708090; background-color: #F5F5F5; font-size: 18px">export</code>和<code style="color: #708090; background-color: #F5F5F5; font-size: 18px">import</code>。

<code style="color: #708090; background-color: #F5F5F5; font-size: 18px">export</code>可以导出：**变量**、**对象**、**函数**和**Class**。

```js
// 导出变量
export const a = 1;
export const b = 2;
// 引用
import { a } from "./module";

// 导出对象
let c = 3;
let d = 4;
export { c, d };

// 导出函数和类
export function Fn() {}
export class Cat {}

// 导出默认
export default function () {}
// 引用
import Fn from "./module";
// 相当于
import { default as Fn } from "./module";
```

#### 3.2.2 导入(import)

JavaScript 引擎会根据需要导入模块的位置，将它导入到当前模块中。

1. 正常使用

```js
// 加载模块中的变量
import { firstName, lastName } from "./profile.js";

// 使用 as 关键字，重命名输入的变量
import { lastName as surname } from "./profile.js";
```

2. <code style="color: #708090; background-color: #F5F5F5; font-size: 18px">import</code>输入的变量是**只读**的。

```js
import { a } from "./xxx.js";

// a 只读，所以会报错
a = {}; // Syntax Error : 'a' is read-only;

// 但，如果 a 是一个对象，可以修改它的属性
a.foo = "hello";
```

3. <code style="color: #708090; background-color: #F5F5F5; font-size: 18px">import</code> 命令具有提升效果，会提升到整个模块的头部，首先执行。

```js
// 这样是合法的，foo 会被提升，像函数一样
foo();

import { foo } from "my_module";
```

4. 由于 <code style="color: #708090; background-color: #F5F5F5; font-size: 18px">import</code> 是静态执行，所以不能使用表达式和变量，这些只有在运行时才能得到结果的语法结构。

```js
import { 'f' + 'oo' } from 'my_module';

// 报错
let module = 'my_module';
import { foo } from module;

// 报错
if (x === 1) {
  import { foo } from 'module1';
} else {
  import { foo } from 'module2';
}
```

**说明：**

> 目前阶段，通过 Babel 转码，CommonJS 模块的 require 命令和 ES6 模块的 import 命令，可以写在同一个模块里面，但是最好不要这样做。

## 3. CommonJS 和 ES Module 差异

主要差异有两点：

- 1. CommonJS 模块输出的是一个值的**拷贝**，ES6 模块输出的是值的**引用**
- 2. CommonJS 模块输出的是**运行时加载**，ES6 模块是**编译时输出接口**

### 3.1 拷贝与引用

**拷贝**显然是指引入模块时发生了一次拷贝，拷贝的模块不会随着原来模块的改变而改变。

_定义一个 CommonJS 模块_

```js
// lib.js
var counter = 3;
function incCounter() {
  counter++;
}
module.exports = {
  counter: counter,
  incCounter: incCounter,
};
```

_使用模块_

```js
var mod = require("./lib");
console.log(mod.counter); // 3
// 改变引入模块 counter 的值
mod.incCounter();
// counter 没有发生变化
console.log(mod.counter); // 3
```

显然，**CommonJS 引入的模块是动态可变的**。

CommonJS 其实加载的是一个**对象**，这个对象只有在脚本运行时才会生成，而且**只会生成一次**。🌟🌟

_定义一个 ES6 Module 模块_

```js
// lib.js
export let counter = 3;
export function incCounter() {
  counter++;
}
```

_使用模块_

```js
import { counter, incCounter } from "./lib";
console.log(counter); // 3
incCounter();
console.log(counter); // 4
```

显然，**ES6 Module 引入的模块是动态可变的**。

ES6 模块不是对象，它的对外接口只是**一种静态定义（引用），在代码静态解析阶段就会生成**，这样我们就可以使用各种工具对 JS 模块进行依赖分析，优化代码。

Webpack 中的 <code style="color: #708090; background-color: #F5F5F5; font-size: 18px">tree shaking</code> 和 <code style="color: #708090; background-color: #F5F5F5; font-size: 18px">scope hoisting</code> 实际上就是依赖 ES6 模块化。

### 3.2 循环加载

#### 3.2.1 CommonJS 的循环加载

CommonJS 的一个模块，一般就是一个文件，使用 require() 第一次加载一个模块的时候，且**只在第一次加载时执行一次模块代码**，就会在内存中生成一个对象。大概长这个样子：

```js
{
  id: '...',
  exports: { ... },
  loaded: true,
  ...
}
```

它会被**放进缓存中**，后面都会从缓存中读取。

CommonJS 模块的特性就是加载时执行，当脚本被 require 的时候，就会全部执行。

**循环加载**会得到已经执行的那部分的值。

```js
// 模块 a.js
exports.done = false;
var b = require("./b.js");
console.log("在 a.js 之中，b.done = %j", b.done);
exports.done = true;
console.log("a.js 执行完毕");
```

```js
// 模块 b.js
exports.done = false;
var a = require("./a.js");
console.log("在 b.js 之中，a.done = %j", a.done);
exports.done = true;
console.log("b.js 执行完毕");
```

```js
// main.js
var a = require("./a.js");
var b = require("./b.js");
console.log("在 main.js 之中, a.done=%j, b.done=%j", a.done, b.done);
```

上面的 a.js 和 b.js 发生了**循环依赖**。

_打印结果如下_

```js
在 b.js 之中，a.done = false
b.js 执行完毕
在 a.js 之中，b.done = true
a.js 执行完毕
在 main.js 之中, a.done=true, b.done=true
```

#### 3.2.2 ES6 Module 的循环加载

ES6 Module 遇到模块加载命令 <code style="color: #708090; background-color: #F5F5F5; font-size: 18px">import</code> 时，不会去执行模块，而是只生成一个**引用**。

等到真的需要用到时，再到模块里面去取值。

ES6 根本**不会关心是否发生了"循环加载"**，只是生成一个指向被加载模块的引用，需要开发者自己保证，真正取值的时候能够取到值。

## 参考

[JavaScript modules 模块 | MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Guide/Modules)

[从 IIFE 聊到 Babel 带你深入了解前端模块化发展体系](https://juejin.cn/post/6844903829448687624)

[浏览器模块的原理与实现 ｜ 阮一峰](http://www.ruanyifeng.com/blog/2015/05/commonjs-in-browser.html)

[浏览器加载 CommonJS 模块的原理与实现 ｜ 阮一峰](http://www.ruanyifeng.com/blog/2015/05/commonjs-in-browser.html)

[JavaScript 模块的循环加载 ｜ 阮一峰](http://www.ruanyifeng.com/blog/2015/11/circular-dependency.html)
