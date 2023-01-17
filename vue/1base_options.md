# Vue基础-选项式API

## 3. 响应式基础

### 3.1 声明响应式状态

### 3.2 声明方法

**有状态方法**

我们可能需要动态地创建一个方法函数，比如创建一个预置防抖的事件处理器：

```javascript
import { debounce } from 'lodash-es'

export default {
  methods: {
    // 使用 Lodash 的防抖函数，动态创建点击回调函数
    click: debounce(function () {
      // ... 对点击的响应 ...
    }, 500)
  }
}
```

这个预置防抖的函数是 **有状态的**：它在运行时维护着一个内部状态。如果多个组件实例都共享这同一个预置防抖的函数，那么它们之间将会互相影响。

要保持每个组件实例的防抖函数都彼此独立，我们可以改为在 `created` 生命周期钩子中创建这个预置防抖的函数：

```javascript
export default {
  created() {
    // 每个实例都有了自己的预置防抖的处理函数
    this.debouncedClick = _.debounce(this.click, 500)
  },
  unmounted() {
    // 最好是在组件卸载时
    // 清除掉防抖计时器
    this.debouncedClick.cancel()
  },
  methods: {
    click() {
      // ... 对点击的响应 ...
    }
  }
}
```

## 4. 计算属性

> 响应式状态复杂时，计算属性可以简化。

### 4.1 基础

```javascript
export default {
  data() {
    return {
      author: {
        name: 'John Doe',
        books: [
          'Vue 2 - Advanced Guide',
          'Vue 3 - Basic Guide',
          'Vue 4 - The Mystery'
        ]
      }
    }
  },
  // 使用 computed 选项定义计算属性
  computed: {
    // 一个计算属性的 getter
    publishedBooksMessage() {
      // `this` 指向当前组件实例
      return this.author.books.length > 0 ? 'Yes' : 'No'
    }
  }
}
```

使用计算属性：

```html
<p>Has published books:</p>
<span>{{ publishedBooksMessage }}</span>
```

计算属性看起来和方法差不多，有何区别？

**计算属性值会基于其响应式依赖被缓存**，像React里的 useMemo.

### 4.2 可写计算属性

通过定义计算属性的 `set()`  方法可以定义一个可写的计算属性。

```javascript
export default {
  data() {
    return {
      firstName: 'John',
      lastName: 'Doe'
    }
  },
  computed: {
    fullName: {
      // getter
      get() {
        return this.firstName + ' ' + this.lastName
      },
      // setter
      set(newValue) {
        // 注意：我们这里使用的是解构赋值语法
        [this.firstName, this.lastName] = newValue.split(' ')
      }
    }
  }
}
```

运行 `this.fullName = 'John Doe'` 时，setter 会被调用而 `this.firstName` 和 `this.lastName` 会随之更新。

## 5. Class 与 Style 绑定

### 5.1 绑定 HTML class

#### 动态切换 class:

```html
<div :class="{ active: isActive }"></div>
```

绑定一个返回对象的**计算属性**：

```javascript
data() {
  return {
    isActive: true,
    error: null
  }
},
computed: {
  classObject() {
    return {
      active: this.isActive && !this.error,
      'text-danger': this.error && this.error.type === 'fatal'
    }
  }
}
```

```html
<div :class="classObject"></div>
```

------

#### 绑定数组

```javascript
data() {
  return {
    activeClass: 'active',
    errorClass: 'text-danger'
  }
}
```

```html
<div :class="[activeClass, errorClass]"></div>
<!-- 有条件的数组 -->
<div :class="[isActive ? activeClass : '', errorClass]"></div>
```

------

#### 在组件上使用

- 只有一个跟元素的组件，class 会被添加到根元素上
- 组件有多个根元素，你可以通过组件的 `$attrs` 属性来实现指定

```html
<!-- MyComponent 模板使用 $attrs 时 -->
<p :class="$attrs.class">Hi!</p>
<span>This is a child component</span>

<!-- 组件上定义 class -->
<MyComponent class="baz" />
```

### 5.2 绑定內联样式

```javascript
data() {
  return {
    activeColor: 'red',
    fontSize: 30
  }
}
```

```html
<div :style="{ color: activeColor, fontSize: fontSize + 'px' }"></div>
```

## 6. 条件渲染

### `v-if`

用于*条件性地*渲染一块内容，指令的表达式返回真值时才被渲染。

`v-else`

跟在一个 `v-if` 或者 `v-else-if` 元素后面，否则不被识别。

`v-show`

会在 DOM 渲染中保留该元素；不支持在 `<template>` 元素上使用。

>  `v-if` 有更高的切换开销，而 `v-show` 有更高的初始渲染开销，因为无论如何`v-show` 的元素都会创建，只是 `display`属性回切换。

## 7. 列表渲染

### 7.1 v-for

遍历数组：

```javascript
data() {
  return {
    items: [{ message: 'Foo' }, { message: 'Bar' }]
  }
}
```

```html
<li v-for="(item, index) in items">
  {{ item.message }}
</li>
```

遍历对象：

```javascript
data() {
  return {
    myObject: {
      title: 'How to do lists in Vue',
      author: 'Jane Doe',
      publishedAt: '2016-04-10'
    }
  }
}
```

```html
<ul>
  <li v-for="(value, key, index) in myObject">
    {{ value }}
  </li>
</ul>
```

------

## 通过 key 管理状态

> Vue "就地更新" `v-for`渲染的列表，即：数据顺序改变时会销毁重建元素，而不是移动元素。
>
> 通过定义 `key`可以追踪每个节点，如果 DOM复杂时会显著提高性能。

## 8. 事件处理

事件处理器的值可以是：

- 內联事件处理器：执行內联JS语句（类似onclick）
- 方法事件处理器：自定义的方法的属性名或路径

### 8.1 內联事件处理器

```javascript
data() {
  return {
    count: 0
  }
}
methods: {
  say(message) {
    alert(message)
  }
}
```

```html
<button @click="count++">Add 1</button>
<p>Count is: {{ count }}</p>
<!-- 內联传参 -->
<button @click="say('hello')">Say hello</button>
<button @click="say('bye')">Say bye</button>
<!-- 访问事件参数，使用特殊的 $event 变量 -->
<button @click="warn('Form cannot be submitted yet.', $event)">
  Submit
</button>

<!-- 访问事件参数，使用内联箭头函数 -->
<button @click="(event) => warn('Form cannot be submitted yet.', event)">
  Submit
</button>
```

### 8.2 方法事件处理器

```js
data() {
  return {
    name: 'Vue.js'
  }
},
methods: {
  greet(event) {
    // 方法中的 `this` 指向当前活跃的组件实例
    alert(`Hello ${this.name}!`)
    // `event` 是 DOM 原生事件
    if (event) {
      alert(event.target.tagName)
    }
  }
}
```

```html
<!-- `greet` 是上面定义过的方法名 -->
<button @click="greet">Greet</button>
```

### 8.3 修饰符

#### 事件修饰符

`v-on` 的**事件修饰符**，用 `.` 表示的指令后缀，可以减少处理 DOM 事件的细节。

```js

<!-- 单击事件将停止传递 -->
<a @click.stop="doThis"></a>

<!-- 提交事件将不再重新加载页面 -->
<form @submit.prevent="onSubmit"></form>

<!-- 修饰语可以使用链式书写 -->
<a @click.stop.prevent="doThat"></a>

<!-- 也可以只有修饰符 -->
<form @submit.prevent></form>

<!-- 仅当 event.target 是元素本身时才会触发事件处理器 -->
<!-- 例如：事件处理器不来自子元素 -->
<div @click.self="doThat">...</div>
```

#### 按键修饰符

```html
<!-- 仅在 `key` 为 `Enter` 时调用 `submit` -->
<input @keyup.enter="submit" />
```

#### 鼠标按键修饰符

`.left`、`.right`、`.middle`
