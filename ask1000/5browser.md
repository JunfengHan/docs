# 浏览器篇

问 ❓: 浏览器解析 HTML 遇到 \<script src="./jquery.js"\> 或 \<link \> 会怎么办？

> 答：会调用**网络进程**加载资源，同时会阻塞**渲染进程**对页面的渲染工作；

问 ❓: 知道哪些浏览器存储？有什么区别？

> 答：Cookie、LocalStorage、SessionStorage、IndexDB 等。
>
> <1>大小限制：Cookie 最多 4 k，LocalStorage、SessionStorage 一般为 5MB
>
> <2>时间限制：
