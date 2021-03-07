# JavaScript 基础

> 基础扎实，走得更稳、更远。

## 1. 数据类型

### 1.1 数据类型分类

![JS数据类型](../_media/js_data_type.png)

_JS 数据分为两类：_

- 基础类型：String、Number、Undefined、Null、Boolean、Symbol、BigInt

  <span style="color: #ff0000; font-size: 16px;">基础类型存储在栈内存，</span>被引用或拷贝时会创建一个完全相等的变量

- 引用类型：Object(Function、Array、Math)
  <span style="color: #ff0000; font-size: 16px;">引用类型存储在堆内存，</span>存储的是地址，多个引用指向同一个地址

_例 1：_

```js
let a = {
  name: "kobe",
  age: 18
}

// b和a指向同一内存地址
let b = a;
// 修改b中name的值
b.name = "bryant";
console.log(a.name); // "bryant"

function getObj(o) {
  o.name = "kk";
  o = {
    name = "kkbb",
    age = 19
  }
  return o;
}
let c = getOjb(a);

console.log(c.name); // "kkbb"
console.log(a.name); // "kk"
```

```js
let a = {
  name: "kobe",
  age: 18,
};
function change(o) {
  // 相当于修改实参的值，会改变实参
  o.age = 24;
  // 这里给o重新赋值，和传入的实参已经无关了
  o = {
    name: "lbj",
    age: 30,
  };
  return o;
}

let b = change(a);
console.log(b.age); // 30
console.log(a.age); // 24
```

### 1.1 数据类型判断

#### typeof

> typeof 操作符返回一个字符串，表示未经计算的操作数的类型。

_例：_

```js
console.log(typeof "abc"); // string
console.log(typeof 2); // number
console.log(typeof false); // boolean
console.log(typeof undefined); // undefined
console.log(typeof null); // object
console.log(typeof [3]); // object
console.log(typeof { a: 1 }); // object
console.log(typeof Symbol()); // symbol
console.log(typeof console); // object
console.log(typeof console.log); // function
```

结论：

- typeof 只能判断基本数据类型（null 除外）
- typeof 判断引用类型只能判断出函数为 function,其他引用类型数据都为 object

#### instanceof

> instanceof 运算符用于检测构造函数的 prototype 属性是否出现在某个实例对象的原型链上.

_例：_

```js
let a = new String("kobe");
console.log(a instanceof String); // true

console.log("abc" instanceof String); // false
console.log(2 instanceof Number); // false
console.log(false instanceof Boolean); // false
console.log(Symbol() instanceof Symbol); // false
console.log([3] instanceof Array); // true
console.log({ a: 1 } instanceof Object); // true
console.log(console instanceof Object); // true
console.log(console.log instanceof Function); // true
```

可以看出，instanceof 判断引用基础数据类型是不准确的：

- instanceof 能有效判断引用数据类型
- instanceof 不能准确判断基础数据类型

#### Object.prototype.toString()

> Object.prototype.toString() 方法返回一个表示该对象的字符串.

```js
Object.prototype.toString({}); // "[object Object]"
Object.prototype.toString.call({}); // 同上结果，加上call也ok
Object.prototype.toString.call(1); // "[object Number]"
Object.prototype.toString.call("1"); // "[object String]"
Object.prototype.toString.call(true); // "[object Boolean]"
Object.prototype.toString.call(function () {}); // "[object Function]"
Object.prototype.toString.call(null); //"[object Null]"
Object.prototype.toString.call(undefined); //"[object Undefined]"
Object.prototype.toString.call(/123/g); //"[object RegExp]"
Object.prototype.toString.call(new Date()); //"[object Date]"
Object.prototype.toString.call([]); //"[object Array]"
Object.prototype.toString.call(document); //"[object HTMLDocument]"
Object.prototype.toString.call(window); //"[object Window]"
```

#### 实现一个判断判断函数

```js
function getType(obj) {
  let type = typeof obj;
  if (type !== "object") {
    // 先进行typeof判断，如果是基础数据类型，直接返回
    return type;
  }
  // 对于typeof返回结果是object的，再进行如下的判断，正则返回结果
  let toStringType = Object.prototype.toString
    .call(obj)
    .replace(/^\[object (\S+)\]$/, "$1"); // 注意正则中间有个空格
  // 返回转化为小写的数据类型
  return toStringType && toStringType.toLowerCase();
}
console.log(getType("8")); // string
console.log(getType("8")); // number
console.log(getType([])); // array
```

### 1.3 数据类型转换

## 参考
