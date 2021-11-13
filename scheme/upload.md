# 彻底搞懂文件上传

# 文件上传

## <span style="color:#ffcc99">1.上传的本质</span>

### <span style="color:#ffe6cc">1.1. 文件是什么</span>

### <span style="color:#ffe6cc">1.1.1 图像是什？</span>

电脑或手机上所谓的图像是数字图像，它不像挂在墙上的照片可以摸得着。图像的数字化就是将模拟图像转化为数字图像的过程，包括扫描、采样、量化三个步骤。采样就是对图像空间的离散化处理，将图像分成一个一个的小像素，而量化就是对图像幅值的离散化处理，使图像像素的数值与有限数值范围中的某一个相对应。而采样点数和量化级数会直接影响分辨率，采样点数越多，量化级数越高，则图像分辨率越高，图像越清晰，但存储图像所需要的空间也就越大，这需要根据不同的情况来选择不同的分辨率，例如微信等软件发送图片时便可自己选择原图或者是压缩后的图片。

<span style="color:#ff661a">图像的表示？</span>

图像数字化之后在计算机中其实就是一个数字矩阵，通常有三种表示形式，灰度图像，彩色图像，二值图像。

灰度图像用一个通道来表示，图像的灰度用像素值来表示，数值越大则图片越白。彩色图像常用三个通道来表示，分别是红绿蓝这三个通道，组成的是一个三维向量矩阵。<span style=" color:#666666; background:#eee; padding: 0 4px">而在计算机中常见的灰度或者红绿蓝的数值范围通常是 0 到 255，这是因为图像的每个像素是使用 8 位来表示的，2^8=256。</span> 这个 8 位是指<span style="color:#ff1a1a">色深</span>。

但这只是适合人类的彩色视觉辨别系统，其实还存在许多其他的范围，例如二值图像只有黑白两色，数值范围便只有 0 和 1，而高级的摄像机或者卫星图片，为了提高图片的清晰度，会采用更大的范围。如 10bit 采样的视频，10bit 的显示器

在图像实际进行存储时，通常会转换成各种格式，常见的有 JPG，PNG，GIF 等。

<span style="color:#ff661a">像素？</span>

1. 图像像素（image pixel）是构成位图的最小单位，它记录的是颜色和位置信息，一个像素只能有一种颜色，而一堆像素的集合就是我们看到的图像。

2. 像素不是一个可以客观衡量的物理单位，它既不是长度单位也不是面积单位，而是像点一样构成了长度，填充了面积。

3. 像素深度就是我们常说的色深（color depth）或者位深（bit depth），**它直接影响了像素颜色的精度与广度，也就色域**。计算机存储信息的最小单位是位（bit），二进制嘛，每个位非 0 即 1，位数越多，能表达的信息也越多。比如 1bit，那么只能表达 ![[公式]](https://www.zhihu.com/equation?tex=2%5E%7B1%7D) 种变化，8bit 就能表达 ![[公式]](https://www.zhihu.com/equation?tex=2%5E%7B8%7D%3D256) 种。

4. RGB 模式下，每种基色有 256 种变化，那么总共就可以表现出 ![[公式]](https://www.zhihu.com/equation?tex=256%5Ctimes256%5Ctimes256%3D16%2C777%2C216) 种颜色。

5. a7m3 相机的 2400 万像素是指，传感器（宽高 3 比 2）上横向排列着 6000\*4000 个像素。

   拍一张无损的图像大小就是:

   24000000x(8+8+8) = 576,000,000 Bits 位
   576,000,000 / 8 = 72,000,000 Bytes 字节

   72,000,000 / 1024 = 70,312.5 KB

   70,3125 / 1024 = 68.66 MB

<span style="color:#ff661a">图像的表示？</span>

图像的颜色也是可以由其他的颜色混合而成的，最常见的颜色模型是 RGB 模型，由三原色红绿蓝组成了其他的颜色，因此我们常称它为加法混色模型，电脑或者电视机这样的显示设备便是采用这种颜色模型。

在印刷行业则是使用另一种颜色模型--CMY 模型，它的原色分别是青色（Cyan）、品红（Magenta）、黄色（Yellow）这三种油墨色，与 RGB 正好互为补色，因此被叫做减色原色模型，而 RGB 可以与 CMY 通过相减来进行转换。之所以用减色是因为打印的东西不能发光，只能反射光线，和发光的加色相反。实际的印刷中，由于染料的纯度，纯粹的黑色不能由其他三种原色混合得到，所以增加了一种黑色（Black），实际印刷使用的模型其实是 CMYK 模型。

RGB 和 CMY 都是面向硬件的，而 HSV 和 HSI 颜色模型则是面向用户的。HSV 用色调（Hue）、饱和度（Saturation）和明度（Value）来描述彩色空间，它把图像的明度与颜色的两个本质特征--色度和饱和度分开，能够颜色光照对图像的影响，很好的弥补了 RGB 模型的缺点。HSI 模型也是面向用户，I 表示亮度（Intensity），与 HSV 相比，除了计算公式略有不同，其他几乎相似，也是面向用户的。

<span style="color:#ff661a">图像像素运算？</span>

<span style="color:#ff661a">视觉错觉</span>

对于图像的亮度而言，人的视觉系统通常与图像的亮度不是成正比的。奥地利物理学家马赫发现了一种亮度对比的视觉效应，称之为马赫带效用，这说的是人的视觉系统会在亮度变化的地方出现明亮或者黑暗的条纹。人的视觉通常会错过渐变，而能够抓住突变，甚至放大突变的亮度，这就是人眼对于突变亮度有着过度的响应。

![v2-bceb78a372aa06ed5fac6d759fac8589_720w](/Users/han/Desktop/souce/v2-bceb78a372aa06ed5fac6d759fac8589_720w.jpg)

而另一种视觉错觉则是同时对比错觉，人对某个区域的亮度感受并不仅仅取决于这个区域的亮度，而会受到周围亮度的对比，这类现象可以用近旁适应性来解释。如下图的中央圆形，即使你的大脑已经知道了两个颜色是一样的，但我们的眼睛感受总会认为右边的会更暗一点

![v2-1143a2593150cc2e032c2263a7688224_720w](/Users/han/Desktop/souce/v2-1143a2593150cc2e032c2263a7688224_720w.jpg)

### <span style="color:#ffe6cc">1.1.2 文件是什？(如：word、文件夹)</span>

## <span style="color:#ffcc99">2.如何上传</span>

### <span style="color:#ffe6cc">2.1 前端处理上传</span>

上传主要分为两步：

1. 获取文件信息
2. 把获取的信息传给服务端接口

### <span style="color:#ffe6cc">2.1.1 获取文件信息</span>

获取文件信息其实是将文件的转化为可以传输的对象，如二进制数据流、base64 格式的图像等。

这个转换过程通常是浏览器来做的，前端需要调用相关接口而已。

前端上传的几种形式：

1. form 表单处理上传

   ```html
   <form id="uploadForm" enctype="multipart/form-data">
     <input id="file" type="file" name="file" />
     <button id="upload" type="button">upload</button>
   </form>
   ```

2. input 单独上传

   通过获取 input 的文件内容，插入到 formData 实例，然后通过 ajax 等方法来传递文件信息。

   ```javascript
   <div id="uploadForm">
     <input id="file" type="file" multiple />
     <button id="upload" type="button">
       upload
     </button>
   </div>;

   var formData = new FormData();
   formData.append("file", $("#file")[0].files[0]);

   // 具体ajax的设置需要具体设置
   $.ajax({
     url: "/upload",
     type: "POST",
     cache: false,
     data: formData,
     processData: false,
     contentType: false,
   })
     .done(function (res) {
       // do something
     })
     .fail(function (res) {
       // do something
     });
   ```

### <span style="color:#ffe6cc">2.2 后端处理上传</span>

## 1. 文件的本质

## 2. 上传的本质

## 3. 上传的前端实现

## 4. 上传的后端实现

## 5. 支持大文件上传

## 6. 支持断点续传

## 参考

**基础知识：**

如果不是很了解 MIME Types，非常有必要看一下 [正确配置服务器 MIME Types -- MDN](https://developer.mozilla.org/zh-CN/docs/Learn/Server-side/Configuring_server_MIME_types#background)

权威的 MIME types 列表 -- [Media Types](http://www.iana.org/assignments/media-types/media-types.xhtml)

[HTTP range requests -- MDN](https://developer.mozilla.org/en-US/docs/Web/HTTP/Range_requests)

**Blog 文章：**

[字节跳动面试官：请你实现一个大文件上传和断点续传](https://juejin.cn/post/6844904046436843527#heading-16)

[前端大文件上传](https://juejin.cn/post/6844903860327186445)
