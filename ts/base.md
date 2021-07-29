# TypeScript 入门

> TypeScript 的严谨比 JavaScript 更适合写 NodeJS 项目。

"Typed JavaScript at Any Scale."这是官网的宣传语，翻译一下应该是“<span style="color: #ff0000; font-size: 16px;">类型化任何规模的 JavaScript</span>”.

这句话总结了 TypeScript 的作用，也说明了和 JavaScript 的关系。

## 1. TS 的类型

### 1.1 定义基本数据类型

可以像 JavaScript 里一样声明变量,TypeScript 会自动识别它的类型。

```ts
// 直接声明一个变量,没有声明其类型
// 会被默认为string类型
let a = "hello"; // 相当于 let a: string = "hello";

// 报错 => 不能将类型“number”分配给类型“string”。ts(2322)
a = 2;
```

**可以看出，变量的类型是确定的，不能给变量赋值为其他数据类型。**

基本类型如下：

- Boolean
- Number
- String
- Array：定义类型相同的数列
- Tuple（元祖）：表示一个有固定数量元素的数组，其类型是已知的，且类型不必相同
- Enum（枚举）：为数值集赋予更友好名称
- Unknown：
- Any: 不进行类型检查，⚠️ 非必要勿使用
- Void：空，通常函数没有返回值时使用
- Null and Undefined
- Never
- Object
- Type assertions（类型断言）:断定某个类型，告诉 TypeScript 解析器这个类型没问题，解析器就会忽略类型检查。像其他语言的类型转换

_定义基本类型示例：_

```ts
let isDone: boolean = false;
let decimal: number = 6;

let color: string = "blue";
// string类型可以使用模板字符串
// 模板字符串可以用 `` 符号包含，可以通过 ${} 形式使用变量
let fullName: string = `My name is ${name}.`;
// 等同于
let fullName: string = "My name is" + name + ".";

// Array
let list: number[] = [1, 2, 3];
let list: Array<number> = [1, 2, 3];

// Tuple（元祖）
let x: [string, number];
x = ["hello", 10];

// Enum
enum Color {
  Red,
  Green,
  Blue,
}
let c: Color = Color.Green;
// or
enum Color {
  Red = 1,
  Green = 2,
  Blue = 4,
}
let c: Color = Color.Green;

// Unknown
let notSure: unknown = 4;
notSure = "maybe a string instead";
notSure = false;

// Any

// Void
function warnUser(): void {
  console.log("This is my warning message");
}
// 虽然没啥意义，但是可以这样用，只能给undefined和null
let unusable: void = undefined;
// OK if `--strictNullChecks` is not given
unusable = null;

// Type assertions
let someValue: unknown = "this is a string";
// as语法
let strLength: number = (someValue as string).length;
// <>语法
let strLength: number = (<string>someValue).length;
```

### 1.2 定义 interface 类型

JS 的设计模式多种多样，有时会难以自动推断其类型。

TypeScript 支持 JavaScript 语言的扩展，它提供了一些地方让你告诉 TypeScript 应该是什么类型。

例如：创建一个推断类型的对象

```ts
const user = {
  name: "Hayes",
  id: 0,
};
```

**我们可以使用 interface 来明确定义对象的结构:**

```ts
interface User {
  name: string;
  id: number;
}
```

然后可以声明一个符合该结构的变量

```ts
const user: User = {
  name: "Hayes",
  id: 0,
};
```

**可选属性：**

```ts
interface SquareConfig {
  color?: string;
  width?: number;
}
```

**只读属性：**

```ts
interface Point {
  readonly x: number;
  readonly y: number;
}

let p1: Point = { x: 10, y: 20 };
p1.x = 5; // error!
```

### 1.3 定义组合类型

```ts
// 定义自己的组合类型；
type MyBool = true | false;
type WindowStates = "open" | "closed" | "minimized";
type LockStates = "locked" | "unlocked";
type OddNumbersUnderTen = 1 | 3 | 5 | 7 | 9;

// 使用
let a: MyBool = false;
let b: MyBool = "hello"; // 不能将类型"hello"分配给类型“MyBool”
```

```ts
// obj 可以是 string 或 array 类型
function getLength(obj: string | string[]) {
  return obj.length;
}
```

## 2. 函数

### 2.1 普通函数

像 JavaScript 一样写函数：

```ts
// Named function
function add(x, y) {
  return x + y;
}

// Anonymous function
let myAdd = function (x, y) {
  return x + y;
};
```

### 2.1 有类型的函数

```ts
function add(x: number, y: number): number {
  return x + y;
}

let myAdd = function (x: number, y: number): number {
  return x + y;
};
```

**可选参数和默认参数：**

```js
// 可选参数加"?"
function buildName(firstName: string, lastName?: string) {
  // ...
}

// 默认参数加"="
function buildName(firstName: string, lastName = "Smith") {
  // ...
}
```

## 3. Class(类)

_一个简单的类：_

```ts
// 定义类
class Greeter {
  // 定义属性的类型
  greeting: string;

  // ES7 语法，相当于在constructor中定义: this.name = "Legend"
  name = "Legend";

  // 定义构造函数
  constructor(message: string) {
    this.greeting = message;
  }

  // 实例方法
  greet() {
    return "Hello, " + this.greeting;
  }
}

// 实例化类
let greeter = new Greeter("world");
```

## 4.泛型

### 4.1 什么是泛型

> 泛型指的是**类型参数化**，即将原来某种具体的类型进行参数化。

和定义函数参数一样，我们可以给泛型定义若干个类型参数，并在调用时给泛型传入明确的类型参数。

设计泛型的**目的**在于<span style="color: #ff0000; font-size: 16px;">有效约束类型成员之间的关系</span>，比如函数参数和返回值、类或者接口成员和方法之间的关系。

<code style="color: #708090; background-color: #F5F5F5; font-size: 18px">泛型</code>最常用的场景是*用来约束函数参数的类型*。

### 4.2 泛型类型参数

当一个函数的参数不确定时，怎么办？用<code style="color: #708090; background-color: #F5F5F5; font-size: 18px">unknown</code>.🤔️

```ts
function reflect(param: unknown) {
  return param;
}

const str = reflect("string"); // str 类型是 unknown
const num = reflect(1); // num 类型 unknown
```

显然，上面的函数不能满足我们的需求。

我们想要的是：**参数的类型随着参数的变化而变化**，即<span style="color: #ff0000; font-size: 16px;">类型参数化</span>。

<code style="color: #708090; background-color: #F5F5F5; font-size: 18px">泛型</code>可以满足这样的需求。

_定义一个泛型 P：_

```ts
<P>
```

_给函数定义泛型参数 P：_

```ts
function reflect<P>(param: P) {
  return param;
}
```

_使用函数：_

```ts
const reflectStr = reflect<string>("string"); // str 类型是 string
const reflectNum = reflect<number>(1); // num 类型 number
```

泛型参数的入参可以从参数的类型中进行推断，而无须再显式指定类型（可缺省），因此上边的示例可以简写为如下示例：

```ts
const reflectStr2 = reflect("string"); // str 类型是 string
const reflectNum2 = reflect(1); // num 类型 number
```

## 参考

[TypeScript for JavaScript Programmers](https://www.typescriptlang.org/docs/handbook/typescript-in-5-minutes.html)

[The TypeScript Handbook](https://www.typescriptlang.org/docs/handbook/intro.html)

[TypeScript 手册中文版](https://www.tslang.cn/docs/handbook/basic-types.html)

[<<深入理解 TypeScript>>](https://jkchao.github.io/typescript-book-chinese/)

[TS 常见问题整理 --> 掘金/秋天不落叶](https://juejin.cn/post/6844904055039344654#heading-13)
