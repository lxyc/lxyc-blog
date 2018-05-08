导读：这篇文章以dome的形式引导了组件的组织和组件间的数据传递，并在文章最后教授如何优雅的实现一个基础组件，具有较好的实战性

> vue中，组件是带有一个名字、可复用的** Vue 实例**。由于 Vue 是面向视图的MVVM框架，组件可以看做是对数据和方法的简单封装、具有独立的逻辑和功能的界面，多个组件按照一定规则的组合最终成为一个完整的应用

## 1. 组件的注册

### 1.1 全局注册

`Vue.component()`用来创建全局组件，一旦注册，即可在该实例Vue下的任何子组件中使用，常用于一些使用较为频繁的基础组件，如Alert组件、Button组件、布局组件等

使用方式：

```javascript
Vue.component('my-component', {
    // vue实例方法和生命周期（el除外）
})
```
如果你使用过 `element-ui` ，下面的写法你可能比较熟悉：
```javascript
import Vue from 'vue';
import ElementUI from 'element-ui';
import 'element-ui/lib/theme-chalk/index.css';
import App from './App.vue';

Vue.use(ElementUI);

new Vue({
  el: '#app',
  render: h => h(App)
});
```
其中`Vue.use(ElementUI);`的方式便是间接调用了全局组件注册的方式，在`element-ui`内部：
（插件中，使用`Vue.use()`的方式，相当于调用了其中的install方法）

```javascript
const install = function(Vue, opts = {}) {
  // ...
  components.map(component => {
    Vue.component(component.name, component);
  });
  // ...
};
```
可以看出，在其内部也是依次全局注册了`element`中的插件

### 1.2 局部注册

Vue官网上如是说：
> 全局注册往往是不够理想的。比如，如果你使用一个像 webpack 这样的构建系统，全局注册所有的组件意味着即便你已经不再使用一个组件了，它仍然会被包含在你最终的构建结果中。这造成了用户下载的 JavaScript 的无谓的增加。

正是因为上面的原因，**除了一些常用的基础组件外，尽可能的使用局部注册的方式**

```javascript
// 普通引入方式
var ComponentA = { /* ... */ }

// ES6引入方式
import ComponentA from './ComponentA.vue'

export default {
  // ...
  components: {
    'component-a': ComponentA,
  }
}
```

值得注意的是：**局部注册方式仅能在当前组件中使用，在其子组件中使用需要再次注册**

## 2. 组件的组织

上面提到，`多个组件按照一定规则的组合最终成为一个完整的应用`，因此，我们可以将组件看作是Vue页面中的最小单元，那么应该如何组织组件，整合成一个页面呢？

有这样一个需求：要求按照下图组织页面结构

![](/vue/assets/compImg.jpg)

我们可以这样组织：
```html
<template>
  <div class="m-body">
    我是主体内容
  </div>
</template>

<script>
export default {

}
</script>

<style scoped>
.m-body {
  min-height: 500px;
  color: #fff;
  padding: 20px;
  background-color: #39f;
}
</style>
```
按照这种方式，依次写出header、aside、content、footer四个组件，并用一个组件作为这四个组件的父组件来组织页面结构，最后的结构如下：

![](/vue/assets/compConstruct.jpg)

父组件如下：
```html
<template>
  <div class="comp">
    <m-header />
    <div class="main">
      <m-side />
      <m-body />
    </div>
    <m-footer />
  </div>
</template>

<script>
import MHeader from './MHeader'
import MFooter from './MFooter'
import MBody from './MBody'
import MSide from './MSide'

export default {
  components: {
    MHeader,
    MFooter,
    MBody,
    MSide
  }
}
</script>

<style lang="scss" scoped>
.main {
  margin: 10px 0;
  display: flex;
  .m-side {
    width: 200px;
    margin-right: 10px;
  }
  .m-body {
    flex: 1;
  }
}
</style>
```
打开Vue调试界面，将看到如下的结构

![](/vue/assets/compDebug1.jpg)

注意：**父组件负责控制容器结构样式（各个直接子组件的位置、大小等），子组件负责其内部的样式，不要在子组件中写自己的容器样式**

## 3. 组件之间的数据传递

组件的组合仅仅只是将页面结构搭建了起来，要完成页面的交互功能，组件之间必定会有数据传递
按照页面结构，大体上可以将组件间的数据传递分成两种：
1. 父子组件间的数据传递
2. 兄弟组件间的数据传递

### 3.1 组件间简单的数据通信

**Vue官网中对props、$emit、slot有非常详细的描述，在此不再唠述**

现有新的需求：在上面例子的基础上，需要满足：header中有一个数值，当aside和body中点击增加和清除按钮时，对应的数值作相应改变

### 3.1 $root方式

### 3.2 总线Bus方式

### 3.3 Vuex方式

## 4. 如何优雅的构建基础组件