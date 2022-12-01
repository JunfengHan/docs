# 项目构建

## 1. webpack

问 ❓：webpack的工作流程？

> 

问 ❓：webpack的loader和plugin区别？

## 2. Bable

问 ❓：webpack的工作流程？

> 

## 3. 项目方案

问 ❓：react项目如何实现懒加载？

> 答：懒加载是指组件被切割开，按需要加载到应用。
>
> 1. [Code Splitting](https://v5.reactrouter.com/web/guides/code-splitting)是懒加载的前提；
>
> 2. React.layzy可以实现React租价的懒加载，但是[有缺陷，所以有了 *Loadable Components*库](https://loadable-components.com/docs/loadable-vs-react-lazy/)。
>
> 3. 我们需要 [@babel/plugin-syntax-dynamic-import](https://www.npmjs.com/package/@babel/plugin-syntax-dynamic-import)来使Bable支持模块动态导入;
> 4. webpack实现打包后的组件[加载需要的文件](https://juejin.cn/post/6850418111599165448)；

