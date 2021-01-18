# 一个基于TypeScript 的 React应用

> 记录使用TypeScript 构建一个 React应用 的过程。

## 1. 构建项目

### 1.1 使用 create-react-app 构建项目

```bash
npx create-react-app react-ts --template typescript
```

npx 是npm（版本高于5.2） 的一个命令，如果版本过低的话直接使用npm创建项目吧。

## 2. 项目分析

### 2.1 css in js

自动构建的项目想模块化使用CSS，需要使用特殊的命名方式 *.modules.css。

如：

```js
// 这样会报错，
import styles from './App.css';

// 以 *.module.css命名文件就可以import了
import styles from './App.module.css';
```

因为，在react—scripts模块已经做了处理。

```js
// react-scripts/lib/react.app.d.ts
...
declare module '*.module.css' {
  const classes: { readonly [key: string]: string };
  export default classes;
}
...
```

如果不想使用 *.module.css 的命名方式，需要我们手动添加声明文件才能 import 一个 CSS文件。

*根目录下新建声明文件：*

```js
// custom.d.ts
declare module '*.css' {
  const classes: { readonly [key: string]: string };
  export default classes;
}
```

