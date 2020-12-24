# 函数（function）

> 一个函数是 JavaScript 过程 — 一组执行任务或计算值的语句。

## 1.定义函数

函数可以大致分为两种：

- 普通函数
- 箭头函数

### 1.1 定义函数

函数声明定义函数：

```js
function square(number) {
  return number * number;
}
```

函数表达式定义函数：

```js
// 函数表达式定义函数
// 注意这里是有分号的
const square = function(number) { 
  return number * number; 
};
```

定义箭头函数：

```js
let sum = (num1, num2) => {
  return num1 + num2;
};
```

使用Function 构造函数：

```js
// 非常不推荐这种方式，也没人这么用
// 因为函数本身就是Function 的实例，所以这样能很好的说明“函数名”是个指针而已
let sum = new Function("num1", "num2", "return num1 + num2");
```

**函数声明与函数表达式的唯一区别是：函数声明提升。**

通俗来说就是：浏览器的JS引擎会把函数声明方式定义的函数名提升到源代码树的顶部。

例：声明式定义函数，函数提升

```js
// 因为 addNum 是声明式定义的，所以函数“addNum” 会被提升到代码的最上方
// 所以这里可以正确执行
let sum1 = addNum(1, 2);

function addNum(num1, num2) {
  return num1 + num2;
}
```

例：表达式定义函数，函数不会提升

```js
// 报错，无法执行
// Uncaught ReferenceError: minusNum is not defined
let minus1 = minusNum(1, 2);

let minusNum = function (num1, num2) {
  return num1 - num2;
};

```

### 1.2 定义箭头函数的其它方式

只有一个参数可以不用括号：

```js
let double = (x) => { return 2 * x; };
let double = x => {return 2 * x; };
```

没有参数必须要括号：

```js
let getRandom = () => { return Math.random(); };
```

多个参数必须要括号：

```js
let sum = (a, b) => { return Math.random(); };
```

如果函数体只有一行，可以省略大括号：

```js
let double = (x) => return 2 * x;
```

## 2.函数是什么

> 先知道函数是什么，才能更好地使用。

打印下看看函数是什么:

```js
function sum(num1, num2) {
    return num1 + num2;
}

console.dir(sum);
```

// 控制台输出结果
![function](../_media/function.png)

**可以看到函数sum有6个属性：**

- arguments: 已经废弃
- caller: 返回调用函数的函数(非标准,了解一下就行了)
- length: 函数参数个数
- name：函数名
- prototype：函数原型对象
- __proto__: Chrome 暴露的指向构造函数的原型对象

其中最核心也是最常用的是 prototype 属性.

> 说明:函数sum是 Function对象 的实例,sum的内部\[\[Prototype\]\]指针会被赋
> 值为构造函数的原型对象,但是我们无法访问,所以由Chrome 暴露出来让我们看到.

```js
// 证明结论1
console.log(sum.prototype)
// 输出 -> {constructor: ƒ}

// 证明结论2
console.log(sum.prototype.constructor === sum)
// 输出 -> true

// 证明结论3
console.log(sum.prototype.constructor.__proto__ === Function.prototype)
// 输出 -> true

console.log(sum.__proto__ === Function.prototype)
// 输出 -> true

```

___
函数结论📝:
1. 函数的 prototype 是个对象,会自动获取一个名为 constructor 的属性
2. 函数的 prototype 对象的 constructor 属性指向函数本身
3. 函数的 __proto__ 属性指向它的构造函数 Function 的 prototype
___

我们知道 Function 是个[基本对象](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects),同时也是个构造函数。

Function 相关代码示例👇：

```js

// 证明结论1
console.log(Function.prototype === Function.__proto__);
// 输出 -> true

// 证明结论2
console.log(Function.prototype.constructor === Function);
// 输出 -> true

// 证明结论3
console.log(Function.prototype);
console.log(Function.__proto__);
// 输出 -> ƒ anonymous()
//        apply: ƒ apply()
//        arguments: (...)
//        bind: ƒ bind()
//        call: ƒ call()
//        caller: (...)
//        constructor: ƒ Function()
//        length: 0
//        name: ""
//        toString: ƒ toString()
//        Symbol(Symbol.hasInstance): ƒ [Symbol.hasInstance]()
//        get arguments: ƒ ()
//        set arguments: ƒ ()
//        get caller: ƒ ()
//        set caller: ƒ ()
//        __proto__: Object
// 证明结论4
console.log(Function.__proto__.__proto__.constructor === Object);
// 证明结论4
console.log(Function.__proto__.__proto__.toString === Object.toString);


```

___
Function函数 相关结论📝:
1. Function函数的原型对象 prototype 等于Function函数的  __proto__属性
2. Function函数的原型对象 prototype 的 constructor 属性同样指向函数本身
3. Function函数的原型对象和 __proto__ 指向同一个匿名函数对象
4. Function函数的原型对象和 __proto__ 指向的匿名函数对象是 Object 的实例
___

