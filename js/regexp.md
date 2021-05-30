# 前端必须掌握的正则知识

<code style="color: #708090; background-color: #F5F5F5; font-size: 18px">正则表达式</code>（Regular Expression，简写为 regex、regexp）是前端绕不过去的坎。

学习正则时觉得好像挺简单，使用它时又觉得好难。

## 1. 什么是正则表达式

<code style="color: #708090; background-color: #F5F5F5; font-size: 18px">正则表达式</code>使用单个字符串来描述、匹配一系列匹配某个句法规则的字符串。

它可以用来操作字符串，如：

- 检索字符串
- 替换字符串

<code style="color: #708090; background-color: #F5F5F5; font-size: 18px">正则表达式</code>可以**用形式化语言理论的方式来表达**。

<code style="color: #708090; background-color: #F5F5F5; font-size: 18px">正则表达式</code>由<span style="color: #ff0000; font-size: 16px;">**常量**和**算子**组成，它们分别表示**字符串的集合**和在这些集合上的**运算**</span>。

在 <code style="color: #708090; background-color: #F5F5F5; font-size: 18px">Perl</code> 中就内建了一个功能强大的<code style="color: #708090; background-color: #F5F5F5; font-size: 18px">正则表达式</code>引擎。

<code style="color: #708090; background-color: #F5F5F5; font-size: 18px">JavaScript</code> 中也有<code style="color: #708090; background-color: #F5F5F5; font-size: 18px">正则表达式</code>。

## 2. 正则表达式基本语法

一个正则表达式通常被称为一个**模式**，<span style="color: #ff0000; font-size: 16px;">**用来**描述**或者**匹配**一系列**匹配某个句法规则的字符串</span>。

大部分正则表达式的形式都有如下的结构：

- 选择
- 数量限定
- 匹配

### 2.1 选择

- 竖线<code style="color: #708090; background-color: #F5F5F5; font-size: 18px">｜</code>，代表选择（或集），优先级较低。

  如：<code style="color: #708090; background-color: #F5F5F5; font-size: 18px">gray|grey</code> 可以匹配 grey 或 gray.

### 2.2 数量限定

某个字符串后的**数量限定符**用来**限定前面这个字符允许出现的个数**。

常见的数量限定符包括：<code style="color: #708090; background-color: #F5F5F5; font-size: 18px">+</code>、<code style="color: #708090; background-color: #F5F5F5; font-size: 18px">？</code>、<code style="color: #708090; background-color: #F5F5F5; font-size: 18px">\*</code>。

- <code style="color: #708090; background-color: #F5F5F5; font-size: 18px">+</code>: 至少出现一次(>= 1)，**必须得有**
- <code style="color: #708090; background-color: #F5F5F5; font-size: 18px">？</code>: 最多只可以出现一次(0 || 1)，简记：**有没有都行**
- <code style="color: #708090; background-color: #F5F5F5; font-size: 18px">\*</code>: 可以不出现，也可以出现一次或者多次(>= 0)

### 2.3 匹配

圆括号<code style="color: #708090; background-color: #F5F5F5; font-size: 18px">()</code>可以<span style="color: #ff0000; font-size: 16px;">用来定义操作符的范围和优先度</span>。

如：

- **gr(a|e)y** 等价于 **gray|grey**

  圆括号<code style="color: #708090; background-color: #F5F5F5; font-size: 18px">()</code>定义了操作符“｜”的范围。

- **(grand)?father** 匹配 **father** 和 **grandfather**

  圆括号<code style="color: #708090; background-color: #F5F5F5; font-size: 18px">()</code>定义了操作符“？”的范围。

上述这些构造子都可以自由组合;

如：<code style="color: #708090; background-color: #F5F5F5; font-size: 18px">H(ae?|ä)ndel</code>和<code style="color: #708090; background-color: #F5F5F5; font-size: 18px">H(a|ae|ä)ndel</code>是相同的。

因为 <code style="color: #708090; background-color: #F5F5F5; font-size: 18px">ae?</code>和<code style="color: #708090; background-color: #F5F5F5; font-size: 18px">a|ae</code>是等价的。

## 3. PCRE 表达式

正则表达式有不同的写法，使用最广泛的是 <code style="color: #708090; background-color: #F5F5F5; font-size: 18px">PCRE</code>。

<code style="color: #708090; background-color: #F5F5F5; font-size: 18px">PCRE</code> 的全称是 Perl Compatible Regular Expression，即 Perl 兼容的正则表达式。

各种编程语言如 Java、Python、C#、JavaScript 实现的就是这种正则表达式。

**PCRE 表达式(部分)：**

| 字符        | 描述                                                                                   | 示例                                                                                                                 |
| ----------- | -------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------- |
| \           | 将下一个字符**标记为一个特殊字符**                                                     | "\n"匹配一个换行符                                                                                                   |
| ^           | 匹配输入字符串的**开始位置**                                                           | “^a”匹配以“a”开头的字符串                                                                                            |
| $           | 匹配输入字符串的**结束位置**                                                           | “$a” 匹配以“a”结束的字符串                                                                                           |
| \*          | 匹配前面子表达式，**有没有都行**                                                       | “zo\*”可以匹配“z”、“zo”、“zoo”                                                                                       |
| +           | 匹配前面的子表达式**一次或多次**                                                       | “zo+”可以匹配“zo”、“zoo”                                                                                             |
| ？          | 匹配前面的子表达式**零次或一次**                                                       | “zo+”可以匹配“z”、“zo”                                                                                               |
| {n}         | n 是一个非负整数,匹配**确定的 n 次**                                                   | “o{2}”不能匹配“Bob”中的“o”，但是能匹配“food”中的两个 o                                                               |
| {n,}        | n 是一个非负整数,**至少匹配 n 次**                                                     | “o{2,}”不能匹配“Bob”中的“o”，但能匹配“foooood”中的所有 o ;“o{1,}”等价于“o+”。“o{0,}”则等价于“o\*”                    |
| {n,m}       | {n,m} m 和 n 均为非负整数，其中 n<=m                                                   | 请注意在逗号和两个数之间不能有空格                                                                                   |
| ？          | 非贪心量化，跟在重复修饰符（\*,+,?，{n}，{n,}，{n,m}）后，尽可能少的匹配所搜索的字符串 | 对于字符串“oooo”，“o+?”将匹配单个“o”，而“o+”将匹配所有“o”                                                            |
| .           | 匹配除“\r”“\n”之外的任何单个字符                                                       | “(.&#124;\r&#124;\n)”的模式可以匹配任意字符                                                                          |
| (pattern)   | 匹配 pattern 并**获取**这一匹配的子字符串                                              |
| (?:pattern) | 匹配 pattern 但**不获取**匹配的子字符串（shy groups）                                  | “industr\(?:y &#124; ies)”就是一个比“industry &#124; industries”更简略的表达                                         |
| (?=pattern) | 正向肯定预查，在任何**匹配 pattern 的字符串开始处**匹配查找字符串                      | “Windows(?=95 &#124; 98 &#124; NT &#124; 2000)”能匹配“Windows2000”中的“Windows”，但不能匹配“Windows3.1”中的“Windows” |
| (?!pattern) | 正向否定预查，在任何**不匹配 pattern 的字符串开始处**匹配查找字符串                    | “Windows(?!95 &#124; 98 &#124; NT &#124; 2000)”能匹配“Windows3.1”中的“Windows”，但不能匹配“Windows2000”中的“Windows” |
| \[xyz\]     | 字符集合,匹配所包含的**任意**一个字符                                                  | “\[abc\]”可以匹配“plain”中的“a”                                                                                      |
| \[^xyz\]    | 排除型字符集合,匹配**未列出的**任意字符                                                | “\[^abc\]”可以匹配“plain”中的“plin”                                                                                  |
| \[a-z\]     | 字符**范围** , 匹配指定**范围内的任意字符**                                            | “\[a-z\]”可以匹配“a”到“z”范围内的任意小写字母字符                                                                    |
| \[^a-z\]    | 排除型的字符范围,匹配任何**不在指定范围内**的任意字符                                  | “\[^a-z\]”可以匹配任何不在“a”到“z”范围内的任意字符                                                                   |
| \\b         | 匹配一个**单词边界** ,指单词和空格间的位置                                             | “er\b”可以匹配“never”中的“er”，但不能匹配“verb”中的“er”                                                              |
| \\B         | 匹配**非单词边界**                                                                     | “er\B”能匹配“verb”中的“er”，但不能匹配“never”中的“er”                                                                |
| \\d         | 匹配一个**数字字符**                                                                   | 等价于\[0-9\]                                                                                                        |
| \\D         | 匹配一个**非数字字符**                                                                 | 等价于\[^0-9\]                                                                                                       |
| \\n         | 匹配一个换行符                                                                         | 等价于\x0a 和\cJ                                                                                                     |
| \\r         | 匹配一个回车符                                                                         | 等价于\x0d 和\cM                                                                                                     |
| \\s         | 匹配任何空白字符，包括空格、制表符、换页符等                                           | 等价于\[ \f\n\r\t\v\]                                                                                                |
| \S          | 匹配任何非空白字符                                                                     | 等价于[^ \f\n\r\t\v]                                                                                                 |
| \\w         | 匹配包括下划线的**任何单词字符**                                                       | 等价于\[A-Za-z0-9\_\]                                                                                                |
| \\W         | 匹配包括下划线的**任何非单词字符**                                                     | 等价于\[^A-Za-z0-9\_\]                                                                                               |

**总结**

---

- \ 代表特殊字符
- {} 处理范围问题
- [] 处理字符集合
- () 包含独立的匹配模式块

## 4. 创建正则表达式

_表达式字面量：_

```js
var re = /ab+c/;
```

_使用 RegExp 对象的构造函数：_

```js
var re = new RegExp("ab+c");
```

## 5. 编写一个正则表达式的模式

- **简单字符**正则表达式模式, 如 <code style="color: #708090; background-color: #F5F5F5; font-size: 18px">/abc/</code>

- 简单和特殊字符的**组合**, 如<code style="color: #708090; background-color: #F5F5F5; font-size: 18px">/ab*c/</code> 或 <code style="color: #708090; background-color: #F5F5F5; font-size: 18px">/Chapter (\d+)\.\d*/</code>

### 5.1 标志

通过**标志**可以进行高级搜索。

- g : 全局搜索
- i : 不区分大小写搜索
- m : 多行搜索
- s : 允许 . 匹配换行符
- u : 使用 unicode 码的模式进行匹配
- y : 执行“粘性(sticky)”搜索

```js
var re = /\w+\s/g;
var re = new RegExp("\\w+\\s", "g");
```

## 6. 使用正则表达式

正则表达式主要做两件事：

- <span style="color: #ff0000; font-size: 16px;">查找</span>
- <span style="color: #ff0000; font-size: 16px;">测试</span>

JavaScript 中，正则表达式可以被用于：

- **RegExp** 的 <code style="color: #708090; background-color: #F5F5F5; font-size: 18px">exec</code> 和 <code style="color: #708090; background-color: #F5F5F5; font-size: 18px">test</code> 方法
- **String** 的 <code style="color: #708090; background-color: #F5F5F5; font-size: 18px">match</code>、<code style="color: #708090; background-color: #F5F5F5; font-size: 18px">replace</code>、<code style="color: #708090; background-color: #F5F5F5; font-size: 18px">search</code> 和 <code style="color: #708090; background-color: #F5F5F5; font-size: 18px">split</code> 方法

| 序号 | 字符     | 描述                                                             | 返回值                                   |
| ---- | -------- | ---------------------------------------------------------------- | ---------------------------------------- |
| 1    | exec     | **查找匹配**的 RegExp 方法                                       | 返回一个数组或 null                      |
| 2    | test     | **测试是否匹配**的 RegExp 方法                                   | Boolean                                  |
| 3    | match    | **查找匹配**的 String 方法                                       | 返回一个数组或 null                      |
| 4    | replace  | **查找匹配**的 String 方法                                       | 返回一个数组                             |
| 5    | matchAll | **查找所有匹配**的 String 方法                                   | 一个迭代器（iterator）                   |
| 6    | search   | **测试匹配**的 String 方法                                       | 并且使用替换字符串替换掉匹配到的子字符串 |
| 7    | split    | **分隔**一个字符串，将分隔后的子字符串存储到数组中的 String 方法 | 返回一个数组                             |

**应用示例**：

_RegExp 的 exec 方法：_

```js
var myArray = /d(b+)d/g.exec("cdbbdbsbz");
console.log(myArray);
// ["dbbd", "bb", index: 1, input: "cdbbdbsbz", groups: undefined]
```

结果：**exec 方法**会返回**匹配结果**和**其他正则属性**。

_String 的 match 方法：_

```js
var myArray = "cdbbdbsbzdbbbbbbbbdfkk".match(/d(b+)d/g);
console.log(myArray);
// ["dbbd", "dbbbbbbbbd"]
```

结果：**exec 方法**会返回**匹配结果**和**其他正则属性**。

## 7. 常见正则

**反转字符串:**

```js
let re = /(\w+)\s(\w+)/;
let str = "John Smith";
// $1 和 $2 指明括号里先前的匹配
let newstr = str.replace(re, "$2, $1");
console.log(newstr);
// Smith, John
```

**从 URL 中提取子域名:**

```js
var url = "http://xxx.domain.com";
console.log(/[^x]+/.exec(url)[0].substr(7));
// /[^.]+/ 的意思是：多个非"."的字符串集合，指匹配“.”之前的字符集合
// logs "xxx"
```

**从 URL 中提取 params:**

```js
let url = "http://www.nowcoder.com?key=1&key=2&key=3&key=4&test=4#hehe";
function getUrlParam(sUrl, sKey) {
  var result = {};
  // “\??“ -> 匹配有0个或1个 "？"
  // "(\w+)=(\w+)&?" -> 匹配以“=”相连且0或1个“&”结尾的任意多个字符
  sUrl.replace(/\??(\w+)=(\w+)&?/g, function (a, k, v) {
    if (result[k] !== void 0) {
      var t = result[k];
      result[k] = [].concat(t, v);
    } else {
      result[k] = v;
    }
  });
  if (sKey === void 0) {
    return result;
  } else {
    return result[sKey] || "";
  }
}
console.log(getUrlParam(url, key));
```

**判断手机号是否合法:**

```js
function isPhoneNum(num) {
  if (typeof num === "number") return false;

  // 以 1 开头，第二个字符为 [35789] 中任意一个，然后是 9 个数字结尾
  let reg = /^1[35789]\d{9}$/g;

  return reg.test(num);
}
```

## 参考

[正则表达式 ｜ Wikipedia](https://zh.wikipedia.org/wiki/%E6%AD%A3%E5%88%99%E8%A1%A8%E8%BE%BE%E5%BC%8F)

[正则表达式 ｜ MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Guide/Regular_Expressions)

[RegExp(正则表达式) | MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/RegExp)
