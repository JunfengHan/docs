# 伪类与伪元素

## 伪类

<code style="color: #708090; background-color: #F5F5F5;">CSS 伪类</code> 是添加到选择器的关键字，指定要选择的元素的<span style="color: #ff0000; font-size: 16px;">特殊状态</span>。

### 伪类有哪些

- :active
- :checked
- :first
- :first-child 表示在一组兄弟元素中的第一个元素
- :focus
- :hover
- :link
- :last-child 代表父元素的最后一个子元素
- :nth-child() 首先找到所有当前元素的兄弟元素，然后按照位置先后顺序从 1 开始排序，选中匹配到的元素集合
- :right
- ...

## 伪元素

<code style="color: #708090; background-color: #F5F5F5;">伪元素</code>是一个附加至选择器末的关键词，允许你修改被选择元素的<span style="color: #ff0000; font-size: 16px;">特定部分样式</span>。

### 伪元素有哪些

"::"是 CSS3 写法，“:”是 CSS2 写法。

- ::after(:after) 在选择的元素后面创建一个伪元素，content 可以指定这个伪元素的内容。
- ::before(:before)
- ::cue(:cue)
- ::first-letter(:first-letter)
- ::first-line(:first-line)
- ::selection 应用于文档中被用户高亮的部分
- ...

## 区别

与伪元素比较，伪类能够根据状态改变元素样式。

## 注意 ⚠️

- 一个选择器中只能使用一个伪元素。

- 伪元素必须紧跟在语句中的简单选择器/基础选择器之后。

- 为了区别伪类与伪元素，伪类一般写双冒号(::)，伪元素一般写单冒号(:)

## 示例链接

[pseudo-classes && pseudo-elements](https://jsfiddle.net/legendHan/5tzjb3r1/)

## 参考

[伪类 - MDN](https://developer.mozilla.org/zh-CN/docs/Web/CSS/Pseudo-classes)

[伪元素 - MDN](https://developer.mozilla.org/zh-CN/docs/Web/CSS/Pseudo-elements)
