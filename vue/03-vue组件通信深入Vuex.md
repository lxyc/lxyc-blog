上一篇博客中，介绍了多种方法来实现组件之间的通信，但是涉及到深层嵌套和非直接关联组件之间的通信时，都会遇到**无法追踪数据和调试的问题**，vuex可以非常方便的解决组件之间数据传递和数据追踪调试问题，这篇博客将简要的介绍vuex的基本用法，然后完成下面的demo

![](/vue/assets/shoppingCart.gif)

## 1. Vuex简介

声明：在此仅介绍Vuex精华知识，更详尽的知识请参考[Vuex中文官网](https://vuex.vuejs.org/zh-cn/intro.html)

（1）Vuex是什么？解决了什么问题？
> Vuex 是一个专为 Vue.js 应用程序开发的状态管理模式。它采用集中式存储管理应用的所有组件的状态，并以相应的规则保证状态以一种可预测的方式发生变化

Vuex解决了`多个视图依赖于同一状态`和`来自不同视图的行为需要变更同一状态`的问题，将开发者的精力聚焦于数据的更新而不是数据在组件之间的传递上

（2）Vuex各个模块的作用及用法

`state`：用于数据的存储，是store中的**唯一数据源**
`mutations`：
`actions`：
`module`：

（3）各个模块的辅助函数说明


## Vuex安装

（1）在项目中安装`Vuex`：
```
npm install vuex --save
```

（2）在src目录下新建`store/index.js`，其中代码如下：
```javascript
import Vue from 'vue'
import Vuex from 'vuex'
// 修改state时在console打印
import createLogger from 'vuex/dist/logger'

Vue.use(Vuex)

const debug = process.env.NODE_ENV !== 'production'

const state = {

}
const getters = {

}
const mutataions = {

}
const actions = {

}

export default new Vuex.Store({
  state,
  getters,
  mutataions,
  actions,
  // 严格模式，非法修改state时报错
  strict: debug,
  plugins: debug ? [createLogger()] : []
})
```
（3）在入口文件`main.js`中添加：
```javascript
// ...
import router from './router'
import store from './store'

new Vue({
  el: '#app',
  router,
  store,
  // ...
})
```
可以对比vue-router和vuex的安装方式：它们**均为vue插件，并在实例化组件时引入，在该实例下的所有组件均可由`this.$router`和`this.$store`的方式查询到对应的插件实例**