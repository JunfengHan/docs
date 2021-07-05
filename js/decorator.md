# Decorator(装饰器)

> 装饰器（Decorator）是一种与类（class）相关的语法，用来注释或修改类和类方法。

<code style="color: #708090; background-color: #F5F5F5; font-size: 18px">装饰器（Decorator）</code>的主要作用是：<span style="color: #ff0000; font-size: 16px;">在多个类之间共享或者扩展类的方法</span>，是一种**非侵入式的行为修改**。

可以让我们的代码更加<code style="color: #708090; background-color: #F5F5F5; font-size: 18px">简洁</code>、<code style="color: #708090; background-color: #F5F5F5; font-size: 18px">易懂</code>。

## 1. Decorator 的用法

```js
@tag
class A {
  @method
  hi() {}
}

function tag(constructor) {
  console.log(constructor === A); // true
}

function method(target) {
  console.log(target.constructor === A, target === A.prototype); // true, true
}
```

上面这段代码是无法在浏览器中直接执行的，我们需要用 <code style="color: #708090; background-color: #F5F5F5; font-size: 18px">Babel</code> 把它编译成 ES5 的语法。

上面<code style="color: #708090; background-color: #F5F5F5; font-size: 18px">@tag</code>是一个装饰器，它用来修饰<code style="color: #708090; background-color: #F5F5F5; font-size: 18px">class A</code>;

上面<code style="color: #708090; background-color: #F5F5F5; font-size: 18px">@method</code>也是一个装饰器，它用来修饰类的方法<code style="color: #708090; background-color: #F5F5F5; font-size: 18px">hi</code>;

## 2. Decorator 的本质

<code style="color: #708090; background-color: #F5F5F5; font-size: 18px">Decorator</code>是<code style="color: #708090; background-color: #F5F5F5; font-size: 18px">Object.defineProperty()</code>的语法糖。

## 3. Decorator 的妙用

## 试一试

[用 Babel 看看把装饰器编译成什么了](https://babeljs.io/repl#?browsers=defaults%2C%20not%20ie%2011%2C%20not%20ie_mob%2011&build=&builtIns=false&corejs=3.6&spec=false&loose=false&code_lz=Q&debug=false&forceAllTransforms=false&shippedProposals=false&circleciRepo=&evaluate=false&fileSize=false&timeTravel=false&sourceType=module&lineWrap=true&presets=env%2Cstage-2%2Cstage-3&prettier=false&targets=&version=7.14.7&externalPlugins=)

## 参考

[Javascript 装饰器的妙用](https://segmentfault.com/a/1190000015566627#articleHeader4)
