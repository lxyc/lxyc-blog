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

## 2. 组件的组织

## 3. 组件之间的数据传递

### 3.1 props方式

### 3.2 $emit方式

### 3.3 slot方式

### 3.4 $root方式

### 3.5 总线Bus方式

### 3.6 Vuex方式

## 4. 如何优雅的构建基础组件