# 算法实战

## 1. 排序

### 1.1 快排

_快排例 1_:

```js

```

总结：

---

1.

### 1.2 组合排序

_快排例 1_:

```js

```

## 2. leetcode 例题

### 2.1 两数只和（1）

给定一个整数数组 nums 和一个整数目标值 target，请你在该数组中找出 和为目标值 的那 两个 整数，并返回它们的数组下标。

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

给定一个字符串，请你找出其中不含有重复字符的 **最长子串** 的长度。

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

> 3. 再次尝试

```js

```

## 参考
