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

## 4. Vite

问 ❓：vite有哪些优点？原因是什么？

> 答：快。

问 ❓：vite 的静态导入和动态导入有什么区别？

> 答：

问 ❓：解释一下 import('./img.png') 和 import(\`./img.png\`)有没有区别？

> 答：

问 ❓：vite 开发模式和生产模式有何不同？

> 答：
>
> - 开发模式：
>   1. 启动一个本地服务器，通过HMR提供快速的开发体验；
>   2. 使用 ESbuild 来构建（编译JS/TS），ESbuild功能较单一，可以很好的处理输入/输出，但是使用了GO语言编写，构建效率非常高；
>   3. 开发模式下使用ES Module的形式，模块发生变化时不需要重新编译整个bundle，只需要替换单个模块即可；
> - 生成模式：
>   1. 使用 Rollup来打包，Rollup是一个功能完善的打包工具，可以很好的处理复杂项目的模块依赖等问题，很好地支持 TreeShaking，有丰富的生态；
>   2. 生成模式下，Rollup会像webpack一样构建出项目的静态文件，并做压缩等处理。
