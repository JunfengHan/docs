# 数据结构（Data Structure）

> 从广义上讲，数据结构就是指**一组数据的存储结构**。

不同的数据结构适用不同的业务场景，数据结构没有好坏之分。

## 1. 内存的工作原理

<code style="color: #708090; background-color: #F5F5F5; font-size: 18px">计算机存储器</code>可分为<code style="color: #708090; background-color: #F5F5F5; font-size: 18px">内部存储器</code>（又称内存或主存）和<code style="color: #708090; background-color: #F5F5F5; font-size: 18px"> 外部存储器</code>，其中<span style="color: #ff0000; font-size: 16px;">内存是 CPU 能直接寻址的储存空间，由半导体器件制成</span>。

<code style="color: #708090; background-color: #F5F5F5; font-size: 18px">内存</code>用于暂时存放 CPU 中的运算数据，计算机中所有程序的运行都在内存中进行。

只要计算机开始运行，<code style="color: #708090; background-color: #F5F5F5; font-size: 18px">操作系统</code>就会把需要运算的数据从<code style="color: #708090; background-color: #F5F5F5; font-size: 18px">内存</code>调到（寻址） <code style="color: #708090; background-color: #F5F5F5; font-size: 18px">CPU</code> 中进行运算，当运算完成，CPU 将结果传送出来。

内存是如何存放数据的呢 ❓

_内存 IC 引脚：_

![IC电路](../_media/structure_ic.png)

- 1. 内存的内部是由各种**IC 电路**组成的
- 2. **内存 IC** 是一个完整的结构，它内部也有**电源**、**地址信号**、**数据信号**、**控制信号**和用于寻址的 **IC 引脚**来进行数据的读写
- 3. A0 - A9 是地址信号的引脚，数据就存储在它上面。地址信号共十个，表示可以指定 00000 00000 - 11111 11111 共 2 的 10 次方 = 1024 个地址。每个地址都会存放 1 byte 的数据，因此，**内存 IC** 的容量是 1 byte \* 1024 = 1 KB
- 4. 512 MB 的内存，这就相当于是 512000（512 \* 1000） 个**内存 IC**
- 5. D0 - D7 表示的是数据信号，也就是说，一次可以输入输出 8 bit（比特） = 1 byte（字节）的数据，例如，字母“A”的二进制表示为<code style="color: #708090; background-color: #F5F5F5; font-size: 18px">01000001</code>，它占用的内存大小是 1 byte(字节).

可以做一个类比，把数据存储到内存，相当于我们去超市时的物品寄存。

![IC电路](../_media/structure_box.jpg)

## 2. 数据结构分类

不同的数据结构有各自的优缺点。

_数据结构分类：_

- 线性表
  - 数组（Array）
  - 链表（Linked List）
  - 堆栈（Stack）
  - 队列（Queue）
- 非线性表
  - 树（Tree）
    - 二叉树
    - 堆积（Heap）
  - 图（Graph）
- 散列表，也叫哈希表（Hash table）

_线性表数据结构图示：_

![线性表数据结构](../_media/structure_line.jpg)

_非线性表数据结构图示：_

![非线性表数据结构](../_media/structure_notLine.jpg)

### 2.1 数组

<code style="color: #708090; background-color: #F5F5F5; font-size: 18px">数组</code>（Array）是一种**线性表**数据结构。它用一组**连续**的内存空间，一般用来存储一组具有**相同类型的数据**。

最大的特点就是**支持随机访问**（因为有下标）。

<code style="color: #708090; background-color: #F5F5F5; font-size: 18px">数组</code>为了保持内存数据的连续性，会导致<span style="color: #ff0000; font-size: 16px;">插入、删除这两个操作比较低效</span>。

### 2.2 链表

<code style="color: #708090; background-color: #F5F5F5; font-size: 18px">链表</code>（Array）是一种**线性表**数据结构。它用一组**非连续**的内存空间，因此，**链表可以存储在内存的任何地方**。

<code style="color: #708090; background-color: #F5F5F5; font-size: 18px">链表</code>通过**指针**来存储它的下一个元素地址，从而**使一系列随机的内存地址串在一块儿**。

### 2.3 栈(Stack)

<code style="color: #708090; background-color: #F5F5F5; font-size: 18px">栈</code>只允许在有序的线性资料集合的一端（称为堆栈顶端，top）进行加入数据（push）和移除数据（pop）的运算，因此遵循<code style="color: #708090; background-color: #F5F5F5; font-size: 18px">后进先出</code>（LIFO, Last In First Out）的原理运作。

<code style="color: #708090; background-color: #F5F5F5; font-size: 18px">栈</code>常用**一维数组**或**链表**来实现。

### 2.4 队列(Queue)

## 3. 数组(Array) VS 链表(Linked List)

数组和链表区别：

- 1. <code style="color: #708090; background-color: #F5F5F5; font-size: 18px">数组</coe>支持随机访问，根据下标随机访问的时间复杂度为 O(1)

- 2. <code style="color: #708090; background-color: #F5F5F5; font-size: 18px">链表</coe>的**插入**、**删除**只是修改其指针指向，时间复杂度为 O(1)

![非线性表数据结构](../_media/structure_arrayVsLink.jpg)

**结论**：<span style="color: #ff0000; font-size: 16px;">数组**更擅长**查询**操作，**链表**更擅长**插入**、**删除**操作**</span>

## 4. 队列(Queue) VS 栈(Stack)

## 参考

推荐 👍 -- [程序员需要了解的硬核知识之内存](https://www.cnblogs.com/cxuanBlog/p/11751609.html)
