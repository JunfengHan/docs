# 算法实战

## 1. leetcode 经典例题

### 1.1 两数只和（1）

> 问题描述：❓
>
> 给定一个整数数组 nums 和一个整数目标值 target，请你在该数组中找出 和为目标值 的那 两个 整数，并返回它们的数组下标。

示例：

```js
输入：nums = [2,7,11,15], target = 9
输出：[0,1]
解释：因为 nums[0] + nums[1] == 9 ，返回 [0, 1]
```

> 1. 暴力破解，时间复杂度 O(n^2)

```js
var twoSum = function (nums, target) {
  let res = [];
  let lengthNums = nums.length;
  for (let i = 0; i < lengthNums; i++) {
    for (let j = i + 1; j < lengthNums; j++) {
      if (nums[i] + nums[j] === target) {
        res = [i, j];
        return res;
      }
    }
  }

  return;
};
```

结果：用时击败 47.68%; 内存击败了： 59.13%；

> 2. 静态哈希表法，时间复杂度 O(2n)

**以空间换时间**。

```js
var twoSum = function (nums, target) {
  let res = [];
  let lengthNums = nums.length;
  let map = new Map();
  for (let i = 0; i < lengthNums; i++) {
    map.set(nums[i], i);
  }

  for (let i = 0; i < lengthNums; i++) {
    let diff = target - nums[i];
    if (map.has(diff) && map.get(diff) !== i) {
      return [i, map.get(diff)];
    }
  }
};
```

结果：用时击败 80.59%; 内存击败了： 5%；

> 3. 动态哈希表法，时间复杂度 O(2n)

```js
var twoSum = function (nums, target) {
  //动态哈希表法
  const map = new Map();
  for (let i = 0; i < nums.length; i++) {
    const num1 = nums[i];
    const num2 = target - nums[i];
    if (map.has(num2)) {
      return [map.get(num2), i];
    } else {
      map.set(num1, i);
    }
  }
};
```

结果：用时击败 97.07%; 内存击败了： 78.24%；

### 2.2 无重复字符串的最长子串（3）

> 问题描述：❓
>
> 给定一个字符串，请你找出其中不含有重复字符的 **最长子串** 的长度。

示例：

```js
输入: s = "abcabcbb"
输出: 3
解释: 因为无重复字符的最长子串是 "abc"，所以其长度为 3。
```

> 1. 暴力解法，时间复杂度为 O(n^2)

```js
var lengthOfLongestSubstring = function (s) {
  let res = "";
  let newRes = "";
  let lengthS = s.length;
  let arrS = Array.from(s);
  let setS = new Set(arrS);

  // 字符串无任何重复项
  if (lengthS === [...setS].length) return lengthS;

  for (let i = 0; i < lengthS; i++) {
    for (let j = i + 1; j < lengthS + 1; j++) {
      // 截取字符串
      newRes = s.slice(i, j);
      // 新截取的字符串无任何重复字符
      if (newRes.length === [...new Set(newRes)].length) {
        if (newRes.length > res.length) {
          res = newRes;
        }
      } else {
        // 字符串一旦有重复，不再往下尝试
        break;
      }
    }
  }

  return res.length;
};
```

结果：无法通过超长字符串测试，一般字符串可以顺利通过。

> 2. 哈希表，分段处理所有字符串，时间复杂度为 O(n^2)

```js
var lengthOfLongestSubstring = function (s) {
  let res = 0,
    i = 0,
    count = 0;
  let lengthS = s.length;
  let setS = new Set(s);

  // 字符串无任何重复项
  if (lengthS === [...setS].length) return lengthS;

  let map = new Map();
  while (i < lengthS) {
    if (!map.has(s[i])) {
      map.set(s[i], i);
      i++;
      // 计数当前无重复字符的长度
      count++;
    } else {
      // 重置i
      i = map.get(s[i]) + 1;
      // 重新计数
      count = 0;
      // 重制map
      map.clear();
    }
    if (res < count) res = count;
  }

  return res;
};
```

结果：时间击败 16.63%；内存击败 15.21%；

> 3. 更好的方法？？

```js

```

## 2. 排序算法

### 2.1 快排

_快排例_:

```js

```

总结：

---

1. 快排算是最理想的排序算法

### 2.2 组合排序

_组合排序_:

```js

```

## 3. 动态规划

## 3.1 最少硬币找零问题

> 问题描述：
>
> 有 d1 = 1, d2 = 5, d3 = 10, d4 = 25 四种面值的硬币，如果要找零 36 美分，求最小硬币数是多少 ❓

```js
function minCoinChange(coins, amount) {
  // 缓存某个最小找零的组合
  // 如：找零为 3 时，可以由 [1,1,1] 或 [1, 2] 或 [3] 组成。
  // 缓存找零为 3 => cache[3] = [3];
  const cache = [];
  const makeChange = (value) => {
    // 找零为 0 直接返回
    if (value <= 0) {
      return [];
    }

    // 递归循环调用阶段
    // 如果已经缓存某个找零额度，直接返回
    if (cache[value]) {
      return cache[value];
    }

    let min = [];
    let newMin;
    let newAmount;

    // 遍历所有面值的硬币
    for (let i = 0; i < coins.length; i++) {
      const coin = coins[i];
      newAmount = value - coin;
      if (newAmount >= 0) {
        // 递归调用
        // ⚠️：2.需要反复执行解决子问题
        newMin = makeChange(newAmount);
      }

      // ⚠️：3.基线条件
      if (
        newAmount >= 0 && // newAmount 必须大于0，小于0 说明找零结束
        (newMin.length < min.length - 1 || !min.length) && // 边界条件
        (newMin.length || !newAmount) // 边界条件
      ) {
        // ⚠️：1.明确子问题，替换找零额度最小组合
        // [2, 3] 中的 [2] 会取代 [1, 1, 3] 中的 [1, 1]
        min = [coin].concat(newMin);
        console.log("new Min" + min + " for " + amount);
      }
    }

    // 递归循环执行阶段
    // 使用新的更小的值取代之前的值
    // 如：找零 5，[1, 4] 取代 [1, 1, 1, 1, 1]
    return (cache[value] = min);
  };

  return makeChange(amount);
}
```

总结：

---

1. 最小找零数量就是把大问题（找零总数）分解成相关联的小问题（每个找零额度最小的数量）
2. **缓存不同找零额度的最小组合**是问题关键
3. 循环不同面值的钱 + 递归调用，确实不太好理解

## 3.2 背包问题

> 问题描述：
>
> 又一个背包，最多只能装 7 kg 的物品，有 3 个物品，重量和价值分别为【2， 3】、【3， 4】、【4， 5】，如何在背包里装进最大价值的物品 ❓

```js
/*
 * @params capacity Number 容量
 * @params weights Array 物品重量
 * @params values Array 物品价值
 * @return
 */
function knapSack(capacity, weights, values) {
  const n = weights.length;
  const kS = [];
  for (let i = 0; i <= n; i++) {
    kS[i] = [];
  }

  // 获取物品 重量/价值 矩阵
  for (let i = 0; i < n; i++) {
    for (let w = 0; w <= capacity; w++) {
      if (i === 0 || w === 0) {
        // 忽略矩阵为 0 的列和行
        kS[i][w] = 0;
      } else if (weights[i - 1] <= w) {
        // 处理当前物品重量小于当前矩阵重量
        // a = 当前重量的物品的价值 + 剩余重量的价值
        const a = values[i - 1] + kS[i - 1][w - weights[i - 1]];
        const b = kS[i - 1][w];
        kS[i][w] = a > b ? a : b;
      } else {
        kS[i] = kS[i - 1][w];
      }
    }
  }

  findValues(n, capacity, kS, weights, values);

  return kS[n][capacity];
}
```

## 参考

👍👍👍 github 10 万点赞 [JavaScript 算法与数据结构](https://github.com/trekhleb/javascript-algorithms/blob/master/README.zh-CN.md)
