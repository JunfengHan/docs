# 算法学习笔记

## 什么是算法

## 算法基本概念

**算法的速度**:

算法的速度指的是随着输入的增加，其运行时间将以什么样的速度增加。

**大 O 表示法**:

大 O 表示法指出了算法有多快。

大 O 表示法指出了<span style="color: #ff0000; font-size: 16px;">最糟糕情况下的运行时间</span>。

## 常见算法分类

- 简单查找: 按顺序一个一个的找
  大O运行时间：O(n)

- 二分查找: 每次排除掉一半的找法
  大O运行时间：O(log2 n)

- 选择排序：
  大O运行时间：O(n log2 n)

- 快速排序：
  大O运行时间：O(n)

- 旅行商问题算法：
  大O运行时间：O(n!)

## 二分查找

**适用场景**：
  必须是<span style="color: #ff0000; font-size: 16px;">有序数列</span>。

如查找有序的一个数列：[1, 2, 3, 4 ... 100]，找出46.

大 O 表示法：O(log 2 n);

```js
// 大 O 时间
Math.log2(100)
```

```js
// 找出 24
let a = [1, 4, 5, 7, 8, 9,13, 14, 15, 16, 17, 18, 19, 20 ,21,23,24,25,26,30];

let lengthA = a.length;

let times;
// 简单查找
for (let i = 1; i < lengthA; i++) {
  if (a[i] === 24) {
    times = i + 1;
    break;
  }
}

// 17
console.log(times);
```

```js
// 二分法
let list = [1, 4, 5, 7, 8, 9,13, 14, 15, 16, 17, 18, 19, 20 ,21,23,24,25,26,30, 31];

function getNum (a, item) {
  // 最小值
  let low = a[0];
  // 最大值
  let heigh = a[a.length - 1];
  // 查找次数
  let times = 0;
  let middleIndex = Math.floor(a.length / 2);
  let lengthA = a.length;

  while (low <= heigh) {
    let guess = a[middleIndex];
    times++;

    if (guess === item) {
      return times;
    } else if (guess > item) {
      heigh = guess;
      // 向下取整
      middleIndex = Math.floor(middleIndex - (lengthA / Math.pow(2, times + 1)));
    } else {
      low = guess;
      // 向上取整
      middleIndex = Math.ceil(middleIndex + (lengthA / Math.pow(2, times + 1)));
    }
  }
}

let times = getNum(list, 7);
```





## 参考

