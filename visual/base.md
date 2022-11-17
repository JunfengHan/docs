# 可视化

## 1. 什么是可视化

[可视化](https://zh.wikipedia.org/wiki/%E5%8F%AF%E8%A7%86%E5%8C%96)

> 可视化是指用于创建图形、图像或动画，以便交流沟通讯息的任何技术和方法。 -- wikipedia

### 1.1 前端的可视化

> 前端开发者口中的【可视化】，往往是指“数据可视化”。

前端可视化包括但不限于：2D、3D 图形与动画，以及 VR 等。

- 理论基础

  数学、图形学

- 图形系统

  HTML/CSS
  Svg
  Canvas
  WebGL

- 图形库

  ThreeJS:

  ZRender: ECharts 依赖的图形渲染引擎

  [Rough.js](https://github.com/rough-stuff/rough): 手绘风格的图形库

- 工具

  D3、GIS、图表库（ECharts、G2 等）

  图表库：呈现各种类型图表，如 ECharts

  地理库：处理地图、地理位置的可视化，GIS 地图库：[Mapbox](https://www.mapbox.com/)、[Leaflet](https://leafletjs.com/)、Deck.gl、CesiumJS 等等

  渲染库：处理视觉呈现,绘制其他更灵活的图形、图像或者物理模型: [ThreeJS](https://threejs.org/)、

  数据驱动框架：处理数据，[D3.js]()数据驱动,门槛高

![可视化知识图谱](./img/base_map.jpeg)

## 2. 可视化基础

- HTML/CSS
- Canvas
- Svg
- WebGL

## 3. Canvas

SVG 属于**声明式**绘图系统，即：根据数据创建各种不同的图形元素。

Canvas2D 通过调用绘图指令，然后引擎直接在页面上绘制图形，是一种**指令式**的绘图系统。

### 3.1 什么是 Canvas

### 3.2 Canvas 原理

Canvas 元素在浏览器上**1. 创造一个空白的画布**，通过提供渲染上下文，赋予我们绘制内容的能力。

我们只需要调用渲染上下文，**2. 设置各种属性**，然后**3. 调用绘图指令**完成输出，就能在画布上呈现各种各样的图形了。

Canvas 能够直接操作绘图上下文，不需要经过 HTML、CSS 解析、构建渲染树、布局等一系列操作。

因此单纯绘图的话，Canvas 比 HTML/CSS 和 SVG 要快得多。

### 3.3 Canvas 优缺点

**缺点**：

- Canvas 绘制整个柱状图的过程就是一系列指令的执行过程，这让我们很难单独对 Canvas 绘图的**局部进行控制**，如：添加点击事件。

如何解决？

通过数学计算我们是可以**通过定位的方式来获取局部图形**。

**优点**：

- 简单易用
- 渲染起来相当高效

### 3.4 Canvas 使用

1. 设置画布大小

```html
<body>
  <canvas width="512" height="512"></canvas>
</body>
```

2. 获取 canvas 上下文

```js
// 获取canvas对象
const canvas = document.querySelector("canvas");
// 获取canvas上下文
const context = canvas.getContext("2d");
```

3. 调用 canvas 上下文方法绘图

```js
// 在canvas中心点绘制一个 100 * 100 的红色正方形
// 定义尺寸
const rectSize = [100, 100];
// 定义填充颜色
context.fillStyle = "red";
context.beginPath();
// 绘图
context.rect(0.5 * canvas.width, 0.5 * canvas.height, ...rectSize);
// 填充颜色
context.fill();
```

步骤总结：

---

1. 获取 Canvas 对象，通过 getContext(‘2d’) 得到 2D 上下文；
2. 设置绘图状态，比如填充颜色 fillStyle，平移变换 translate 等等；
3. 调用 beginPath 指令开始绘制图形；
4. 调用绘图指令，比如 rect，表示绘制矩形；
5. 调用 fill 指令，将绘制内容真正输出到画布上。

### 3.5 Canvas 知名库与原理

## 4. Svg

### 4.1 什么是 Svg

<code style="color: #708090; background-color: #F5F5F5; font-size: 18px">SVG</code>（Scalable Vector Graphics，可缩放矢量图）是一种基于 XML 语法的**图像格式**，可以用图片（img 元素）的 src 属性加载。

<code style="color: #708090; background-color: #F5F5F5; font-size: 18px">SVG</code> 则弥补了 HTML/CSS 绘制不规则图形的不足，让不规则图形的绘制变得更简单了。

**缺点**：

- 在渲染引擎中，SVG 元素和 HTML 元素一样，在输出图形前都需要经过引擎的解析、布局计算和渲染树生成

- 一个 SVG 元素只表示一种基本图形，数据复杂时绘制图形很多，性能急剧下降

**结论**：

> 只适合应用于元素较少的简单可视化场景.

### 4.2 Svg 可以做什么

### 4.3 Svg 怎么用

```html
<svg xmlns="http://www.w3.org/2000/svg" version="1.1">
  <circle
    cx="100"
    cy="50"
    r="40"
    stroke="black"
    stroke-width="2"
    fill="orange"
  />
</svg>
```

- svg 元素: 是 SVG 的根元素
- xmlns 属性: 值是"http://www.w3.org/2000/svg"，浏览器根据这个属性值就能够识别出这是一段 SVG 的内容了
- circle 元素: 表示这是一个绘制在 SVG 图像中的圆形
- cx 和 cy 是坐标: 表示圆心的位置在图像的 x=100、y=50 处

![svg绘图](./img/svg_img.png)

### 4.4 Svg 在项目中可以实现哪些业务

### 4.5 Svg 知名库与原理

## 5. WebGL

WebGL 是基于 OpenGL ES 规范的浏览器实现，API 相对更底层，使用起来略微麻烦。

WebGL 是浏览器提供的功能强大的绘图系统，它使用比较复杂，但是功能强大，能够充分利用 GPU 并行计算的能力，来快速、精准地操作图像的像素，在同一时间完成数十万或数百万次计算。

另外，它还内置了对 3D 物体的投影、深度检测等处理，这让它更适合绘制 3D 场景。

### 5.1 什么是 WebGL

### 5.2 WebGL 可以做什么

### 5.3 WebGL 怎么用

### 5.4 WebGL 在项目中可以实现哪些业务

google -- Web component, model-viewer

### 5.5 WebGL 知名库与原理

3D 框架

- Babylon.js 3D 引擎，微软出品
- Three.js 3D 开发工具箱，功能丰富，开源社区
- PlayCanvas Web 游戏引擎框架，被 Snapchat 收购
- Cocos Creator 跨平台 2D、3D 游戏创造工具

组件化开发库

- React Three Fiber 封装 React 组件的 Three.js

## 6. Canvas、Svg、WebGL 对比

![选型对比](./img/compare.jpg)

## 7. 数学基础

> 计算机绘制图形，就是控制像素点在一个坐标系内的展现。

### 7.1 坐标系

- SVG

- Canvas

- WebGL

## 8. 视觉呈现

基础阶段，我们主要讨论如何以像素化的方式处理图案，来实现各种细节效果。

在高级阶段，我们讨论动画、3D 和交互等更深入的话题。

## 9. 性能优化

## 参考
