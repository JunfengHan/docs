# 前端面试题之--React 篇

## 🍅React 架构 🍅

## 🍅React 核心 🍅

### setState 是同步还是异步 ❓

🪶 **简答：**

一般情况下是异步。如周期函数、合成事件中设置 setState 是异步的。

但是，setTimeout 中设置的话就是同步的。

🪜 **高阶：**

React17 的运行模式有 3 种，Legacy 模式下就像上面说的一样，但是 Concurrent 模式下 setState 全部是异步的。

🐎 **源码：**

源码中有个判断是否异步执行的变量 xx,合成事件和周期函数中调用 setState 这个变量。。。，setTimeout 时则不会，所以会同步执行。

### React 生命周期有哪些 ❓

## 🍅React Hooks🍅

### 常用的 Hooks 有哪些 ❓

### 简单说一下 Hooks 的原理 ❓

## 🍅React Router🍅

### 什么是前端路由 ❓

## 🍅Redux🍅
