## 一、从传统库类开始

传统框架/库（`jQuery`为例）与MVVM型框架（`vue`为例）

### 1.1 理论

- `vue`主要是操作数据，将`数据层`和`视图层`完全分离开的，通过对数据操作来改变视图；

- `jQuery`主要是操作DOM，一般是获取到指定DOM后，通过绑定事件来完成页面交互；

### 1.2 写法

**一个简单但经典的例子**

需求：输入框中输入的内容展示在“输入的内容后面”，并实时关联

![](/vue/assets/hello_vue.jpg)

传统jquery处理方式：
```html
输入框：<input type="text" id="input">
输入的内容：<p id="showArea"></p>
```
```javascript
$('#input').on('input', (e) => {
  $('#showArea').html(e.target.value);
})
```
vue处理方式(模块内)：
```html
<template>
  <div>
    输入框：<input type="text" v-model="inputVal"><br>
    <p>输入的内容：{{inputVal}}</p>
  </div>
</template>

<script>
export default {
  data() {
    return {
      inputVal: '',
    };
  },
};
</script>
```

从这个简单的例子中可以看出，jquery以DOM和事件操作为主，vue以数据为主。


在vue项目中，什么是组件？

代码层面：
1. 最常见的`*.vue`文件
2. Render 函数
3. mixin