# fs（文件系统）

<code style="color: #708090; background-color: #F5F5F5; font-size: 18px">fs 模块</code>可用于与文件系统进行交互。

所有的文件系统操作都有**同步的**、**回调的**（异步）、以及**基于 promise** 的形式。

## 1. 操作文件的形式

- 同步
- (回调)异步
- promise

_同步删除文件：_

```js
try {
  fs.unlinkSync("文件");
  console.log("已成功删除文件");
} catch (err) {
  // 处理错误
}
```

_异步删除文件：_

```js
fs.unlink("文件", (err) => {
  if (err) throw err;
  console.log("已成功地删除文件");
});
```

_Promise 形式删除文件：_

```js
(async function (path) {
  try {
    await fs.unlink(path);
    console.log(`已成功地删除文件 ${path}`);
  } catch (error) {
    console.error("出错：", error.message);
  }
})("文件");
```

### 1.1 同步 VS 异步

官方 Blog[不要阻塞你的事件循环（或是工作线程池）](https://nodejs.org/zh-cn/docs/guides/dont-block-the-event-loop/)告诉我们，以下 API 同步时开销很大：

- crypto 加密
- zlib 压缩
- fs 文件系统
- child_process 子进程

这些 API 是为脚本提供方便，**并非让你在服务器上下文中使用**。

在官方 Blog[阻塞对比非阻塞一览](https://nodejs.org/zh-cn/docs/guides/blocking-vs-non-blocking/)也对**阻塞**和**非阻塞**做了比较。

结论是：

- 阻塞方法**同步**执行，非阻塞方法**异步**执行
- 阻塞方法报错必须要被捕获，否则报错；非阻塞方法由你来决定是否抛出错误
- 非阻塞一般会提高性能
- 不要混合写阻塞和非阻塞代码

_混合写阻塞和非阻塞：_

```js
const fs = require("fs");
fs.readFile("/file.md", (err, data) => {
  if (err) throw err;
  console.log(data);
});
fs.unlinkSync("/file.md");
```

<code style="color: #708090; background-color: #F5F5F5; font-size: 18px">fs.unlinkSync()</code> 极有可能在 <code style="color: #708090; background-color: #F5F5F5; font-size: 18px">fs.readFile()</code> 之前执行，它会在实际读取之前删除 file.md

_更好的非阻塞处理：_

```js
const fs = require("fs");
fs.readFile("/file.md", (readFileErr, data) => {
  if (readFileErr) throw readFileErr;
  console.log(data);
  fs.unlink("/file.md", (unlinkErr) => {
    if (unlinkErr) throw unlinkErr;
  });
});
```

## 2. 文件路径

> 路径告诉 fs 模块，是想要处理哪个的文件。

**路径种类：**

- 字符串
- Buffer
- URL 对象（file:协议）

```js
fs.open("/文件.txt", fn);
fs.open(Buffer.from("/文件.txt"), fn);

const fileUrl = new URL("file:///文件");
fs.open(fileUrl, fn);
```

## 3. fs 类

有些 <code style="color: #708090; background-color: #F5F5F5; font-size: 18px">fs 操作</code>方法会返回一个 <code style="color: #708090; background-color: #F5F5F5; font-size: 18px">fs 类</code>，类的实例上有不同的方法。

- Dir 类: 表示目录流的类
  fs.opendir()、fs.opendirSync() 或 fsPromises.opendir() 创建

- Dirent 类: 目录项（可以是文件或目录中的子目录）的表示

- FSWatcher 类：文件监视类
  fs.watch() 方法会返回新建的 fs.FSWatcher 对象

- StatWatcher 类：
- ReadStream 类
  fs.createReadStream() 函数创建并返回的 fs.ReadStream 实例。

- Stats 类：提供了关于文件的信息
  fs.stat()、fs.lstat()、fs.fstat()、以及它们的同步方法返回的对象都是此类型

- WriteStream 类：

## 参考

官方文档 -- [fs(文件系统)](http://nodejs.cn/api/fs.html#fs_file_system)
