上一篇博客中，介绍了多种方法来实现组件之间的通信，但是涉及到深层嵌套和非直接关联组件之间的通信时，都会遇到**无法追踪数据和调试的问题**，vuex可以非常方便的解决这个问题

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