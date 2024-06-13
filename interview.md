# 面试题目

## 1.自我介绍

## 2. CSS 相关

- css里的单位：rem、em、px、vw/vh、%

  ```
  1.4 长度单位问题：rem、em、vh、vw、100%
  问 ❓：rem与em和px区别？
  
  px是绝对数值，只和屏幕像素有关；
  
  em是和rem是相对长度，em相对于自身元素的字体大小（font-size: 1em则是相对于父元素字体大小）；
  
  rem相对于跟元素<html>字体大小。
  
  问 ❓：vh包含不包含浏览器标签栏？
  
  答：不包括，隐藏标签栏会影响vh的计算，如果使用了vh布局等，会受到影响。
  
  问 ❓：width 100%是相对于谁？
  
  相对于其最近的块级父元素的内容区域（content area），这意味着，如果父元素有任何内边距（padding）或边框（border），这些都不会被包含在计算的宽度中。
  
  如：font-size相对于的父元素的font-size,with相对于父元素的width;
  
  问 ❓：给font-size 设置 100%是相对于谁？
  
  父元素字体大小。
  ```

  
- 弹性（flex）布局：flex: 1 1 auto;

- 

  ```
  BFC 是什么？开发中有没有利用BFC来解决过实际的问题？
  
  答：BFC 是块级格式化上下文，是页面中可视 CSS 渲染的一部分，可以理解为一个相对独立的布局区域。
  
  可以解决让浮动元素高度塌陷、上下 margin 折叠等问题；
  ```

## 3. JS/TS

1. 看一道题目，说一下是如何执行的；
   1. 基础类型和引用类型，JS数据深拷贝
   2. var、let区别？
   3. this指向谁？指向调用该函数的对象
   
      1. 作为对象方法执行时，this指向对象
      2. 函数不是作为对象的方法被调用的，则“this”指向全局对象（很重要🌟）
      3. call和apply可以改变this指向
      4. 箭头函数没有this，其this继承了外部作用域中的this
   
      ```js
      var a = 1;
      var b = 2;
      function fn() {
          var a = 3;

          function fn1() {
            console.log('a1:', a);
            console.log('a2:', this.a);
            console.log('b:', this.b);
          }

          fn1();
      }
      fn();
      console.log('a3:', a);
      // 3, 1, 2, 1
      ```
   
      
   4. 执行上下文、词法作用域（静态作用域）与动态作用域
   5. 异步同步，event loop
   
   ```javascript
    var a = 1;
      
    function fa() {
      a = 2;
      console.log('a1:', a);
    };

    setTimeout(() => {
      let a = 3;
      console.log('a2:', this.a);
      console.log('a3:', a);
    }, 0);

    Promise.resolve().then(() => {
      console.log('a4:', a);
    }).then(() => {
      console.log('a5:', a);
    });

    fa();
   ```
   
   

## 4. VUE/REACT

1. React hooks有用过吗？常用的有哪些？

2. 解释一下 useEffect 如何使用？useEffect 在何时执行？写一个useCustomEffect 初次加载时不执行，只在依赖发生变化时执行；
  
    ```javascript
    const useCustomEffect = (effect, dependencies) => {
      const isFirstRender = useRef(true);
      useEffect(() => {
        if (isFirstRender.current) {
          isFirstRender.current = false;
        } else {
          return effect();
        }
      }, dependencies);
    }
    ```

3. React 为什么需要hooks? 
  
   1. 使组件逻辑复用更容易；
   2. 使复杂组件更易理解；
   3. 使逻辑与UI分离；
   4. 使组件更容易拆分；
   5. 使自定义hook更容易编写。

4. Redux 在你的项目里都管理了哪些状态？为什么要使用 Redux？useContext 好像也能轻松实现你这些功能吧？



## 5. 项目

1. 如何封装axios的？axios可以取消请求吗？
2. 登陆功能前端都做了哪些工作？cookie 和 token 区别？token如何取消？
3. 根据权限生成了导航菜单，如果用户直接访问一个没有权限的菜单会发生什么？

## 6. 综合

1. 什么是组件的懒加载？

   1. 不可见的部分初始化时不加载，需要加载（滚动可见、路由切换等）时再加载。

2. 基于 webpack 构建的vue 项目如何处理懒加载？

   1. 使用 webpack的 import() 函数将懒加载组件导入（返回 Promise函数）；
   2. 在路由配置中使用懒加载组件；
   3. 在webpak 中配置启用代码分割（Code Spliting）,将需要懒加载的组件单独打包成 chunk.

   ```json
   // webpack.config.js
   module.exports = {
     // ...
     optimization: {
       splitChunks: { // 用于配置代码分割
         chunks: 'async', // 只对异步加载的模块进行代码分割
         minSize: 30000,
         maxSize: 0,
         minChunks: 1,
         maxAsyncRequests: 5,
         maxInitialRequests: 3,
         automaticNameDelimiter: '~',
         name: true,
         cacheGroups: { // 用于配置分组
           vendors: { // vendors 分组用于将来自 node_modules 目录下的模块单独打包成一个 chunk
             test: /[\\/]node_modules[\\/]/,
             priority: -10
           },
           default: { // default 分组用于将重复引用的模块单独打包成一个 chunk
             minChunks: 2,
             priority: -20,
             reuseExistingChunk: true
           }
         }
       }
     }
   }
   ```

   

   ​		d. 生产环境下启用压缩功能。

   ```
   
   ```

   

3. 算法

   ```javascript
   // 写一个转换函数，把下面数组转化为树形结构。输出结构如下：
   // {
   // "id": '',
   // "name": '',
   // "children": []
   // }
   const data = [
     { id: 1, name: "Electronics", parent: 0 },
     { id: 2, name: "Mobiles", parent: 1 },
     { id: 3, name: "Laptops", parent: 1 },
     { id: 4, name: "Computers", parent: 1 },
     { id: 5, name: "Samsung", parent: 2 },
     { id: 6, name: "iPhone", parent: 2 },
     { id: 7, name: "Dell", parent: 3 },
     { id: 8, name: "Lenovo", parent: 3 },
     { id: 9, name: "HP", parent: 3 },
     { id: 10, name: "Asus", parent: 4 },
     { id: 11, name: "Acer", parent: 4 },
     ];
   ```


## 5. 有没有什么问题需要我来解答的？

前端基础比较薄弱，不能很好的解释flex布局，JS基础也不够扎实，不清楚函数的执行上下文，错误理解函数的this，代码能力一般，有自己的思路，但是未完成数组转树的算法需求。