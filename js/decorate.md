# ES7 装饰器

> 装饰器用来修改class (类) 或 class的方法。

## 装饰器的使用

```ts
@decorate
class A {}

// 相当于
class A {}
A = decorator(A) || A;
```

*示例：*

```ts
@decorate
class A {}

decorate(target) {
  target.name = "target"
}

let a = new A();
console.log(a.name);
```

## 参考

[细说ES7 JavaScript Decorators](https://developer.aliyun.com/article/340945)
