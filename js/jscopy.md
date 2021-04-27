# JS 的数据拷贝

## 1.JS 数据类型存储

> JavaScript 是一种弱类型或者说动态语言。这意味着你不用提前声明变量的类型，在程序运行过程中，类型会被自动确定。

原始数据类型：在内存中占据固定大小，保存在 ==栈内存== 中（不包含闭包中的变量）。

引用类型：引用类型的值是对象，保存在 ==堆内存== 中，调用时通过指针来访问。

## 2.什么是浅拷贝

> 这个 MDN 并没有给出明确的定义，MDN 只说 Object.asign()方法\*拷贝的是属性值，假如源对象的属性值是一个对象的引用，**那么它也只指向那个==引用==\***。

```javascript
let obj = {
  a: 1,
  b: {
    b1: 2,
  },
};

let obj1 = Object.assign({}, obj);
// 修改拷贝后的值
obj1.a = 4;
obj1.b.b1 = 3;
// 原来的引用类型的值会发生变化
// 原来的基本数据类型不会变化
console.log(obj); // {a:1, b: {b1: 3}}
console.log(obj1); // {a:4, b: {b1: 3}}
```

#### 2.1 JS 有哪些浅拷贝方法

- **Array.prototype.slice**: 这一对象是一个由 begin 和 end 决定的原数组的浅拷贝（包括 begin，不包括 end）。原始数组不会被改变。

MDN 里的实例：

```javascript
const animals = ["ant", "bison", "camel", "duck", "elephant"];

console.log(animals.slice(2));
// expected output: Array ["camel", "duck", "elephant"]

console.log(animals.slice(2, 4));
// expected output: Array ["camel", "duck"]

console.log(animals.slice(1, 5));
// expected output: Array ["bison", "camel", "duck", "elephant"]
```

- Array.prototype.concat
- ...扩展运算符（MDN 叫剩余参数）

扩展运算符的浅拷贝实例：

```javascript
let obj = { a: 1, b: { c: 1 } };
let obj2 = { ...obj };
obj.a = 2;
console.log(obj); //{a:2,b:{c:1}}
console.log(obj2); //{a:1,b:{c:1}}

obj.b.c = 2;
console.log(obj); //{a:2,b:{c:2}}
console.log(obj2); //{a:1,b:{c:2}}
```

## 3.什么是深拷贝

> 顾名思义，深拷贝就是拷贝出一个新的对象，新对象的改变不会对原对象有任何影响。

**JS 没有深拷贝方法。**

通常使用如下方法：

```javascript
let obj = {
  a: 1,
  b: {
    b1: 2,
  },
};

// 通过这种方法实现深拷贝，但是有缺陷
obj1 = JSON.parse(JSON.stringfy(obj));
```

JSON.parse(JSON.stringfy())方法的缺陷：

1. 日期对象被当作字符串处理
2. 正则对象
3. 循环引用

_深拷贝：_

**原理**：递归调用，单独处理正则和日期对象。利用 WeekMap 解决循环引用

```js
function deepClone(value) {
  let map = new WeakMap();
  function clone(value) {
    if (value instanceof Object) {
      if (map.has(value)) {
        return map.get(value);
      }

      let newValue;
      if (value instanceof Array) {
        newValue = [];
      } else if (value instanceof Function) {
        newValue = function () {
          // value 是函数时，继承父函数内 this 绑定的属性
          return value.apply(this, arguments);
        };
      } else if (value instanceof RegExp) {
        // 拼接正则
        newValue = new RegExp(value.source, value.flags);
      } else if (value instanceof Date) {
        // 处理日期对象
        newValue = new Date(value);
      } else {
        newValue = {};
      }

      // 克隆对象自身定义的属性
      let desc = Object.getOwnPropertyDescriptors(value);
      // 克隆对象原型上定义的属性
      let prototypeValue = Object.getPrototypeOf(value);
      let cloneValue = Object.create(prototypeValue, desc);
      map.set(value, cloneValue);

      // 循环递归调用clone
      for (let key in value) {
        if (value.hasOwnProperty(key)) {
          newValue[key] = clone(value[key]);
        }
      }

      return newValue;
    }
    return value;
  }

  return clone(value);
}
```

[理解 JS 浅拷贝与深拷贝](https://juejin.im/post/5d235d1ef265da1b855c7b5d)

[深拷贝的终极探究](https://segmentfault.com/a/1190000016672263)
