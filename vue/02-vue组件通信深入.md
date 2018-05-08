**建议：博客中的例子都放在[vue_blog_project](https://github.com/lxyc/vue_blog_project)工程中，推荐结合工程实例与博客一同学习**

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

> 有这样一个需求：要求按照下图组织页面结构

![](/vue/assets/compImg1.jpg)

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

> 现有新的**需求**：在上面例子的基础上，需要满足：header中有一个数值，side中新增重置和增加按钮，body中新增数组输入框，当对按钮和表单作操作时，对应的数值作相应改变

![](/vue/assets/compImg2.jpg)


基本**思路**：将数值放在几个组件公共上层组件中，header中prop接受该值，side和body中点击按钮向他们的公共上层组件分发$emit事件，改变该数值，核心思路：**多个组件操作的值均为上层组件的变量**

代码如下：

（1）**父级组件**：主要用于数据传递与接收子组件分发的事件来改变对应的变量
```html
<div class="comp">
  <m-header :num="num" />
  <div class="main">
    <m-side @add="handleAdd" @reset="handleReset" />
    <m-body :num="num" @change="handleChange" />
  </div>
  <m-footer />
</div>
```
```javascript
export default {
  data () {
    return {
      num: 0
    }
  },
  methods: {
    handleAdd () {
      this.num += 1
    },
    handleChange (val) {
      this.num = val
    },
    handleReset () {
      this.num = 0
    }
  },
  // ...
}
```

（2）**Header组件**：接受并展示数值
template中仅添加`{{ num }}`
```javascript
props: {
  num: {
    type: Number,
    default: 0
  }
}
```

（3）**Side组件**：向上分发增加和重置事件
```html
<!-- 新增 -->
<el-button @click="add">ADD</el-button>
<el-button @click="reset">RESET</el-button>
```
```javascript
methods: {
  add () {
    this.$emit('add')
  },
  reset () {
    this.$emit('reset')
  }
}
```

（4）**Body组件**：监控传值，向上分发事件
```html
<!-- 新增 -->
<el-input-number v-model="currentVal" @change="handleChange"></el-input-number>
```
```javascript
props: {
  num: {
    type: Number,
    default: 0
  }
},
data () {
  return {
    currentVal: 0
  }
},
// 外层数据改变时，currentVal值需要同步修改
watch: {
  num: {
    handler (val) {
      this.currentVal = val
    },
    immediate: true
  }
},
methods: {
  handleChange (val) {
    this.$emit('change', val)
  }
}
```

这种简单的数据交互使用prop和$emit足以应付，但是
（1）对于深层组件嵌套中的数据传递，使用这种通信方式则需要一层一层向下prop，改变时需要一层一层向上$emit
（2）对于兄弟组件之间的数据传递，先要向上分发，再向下prop，**过于繁琐且不易监控调试**

> 这里有一个新的需求：在最初组件组合的基础上，side组件中有一个数据展示，要求通过body中深层嵌套的组件操作以改变side中的数据

修改：在body组件中添加`<slot></slot>`，并新增一个组件挂载在该插槽上，用以模拟深层嵌套（当然了，实际的工作中的嵌套可能涉及到四层甚至更多）

### 3.2 $root方式

上面方法的核心是所有子组件统一管理和操作父组件的数据，子组件负责展示和分发事件，实际操作值的始终在父组件，Vue提供了一个能访问到根组件的方法，官网中如是描述：[处理边界情况](https://cn.vuejs.org/v2/guide/components-edge-cases.html)中访问根实例部分

（1）在入口文件`main.js`中添加：
```javascript
new Vue({
  data: {
    rootNum: 0
  },
  // ...
})
```
（2）在**父组件**中添加：
```html
<!-- 局部注册不作详述 -->
<m-body>
  <m-body-item></m-body-item>
</m-body>
```

（3）**新添加的组件**`MBodyItem`
```html
<template>
  <div class="m-body-item">
    <el-button @click="add">ADD</el-button>
    <el-button @click="reset">RESET</el-button>
  </div>
</template>

<script>
// 可直接操作$root中声明的变量
export default {
  methods: {
    add () {
      this.$root.rootNum += 1
    },
    reset () {
      this.$root.rootNum = 0
    }
  }
}
</script>
```

（4）**side组件**：
```html
<div class="m-side">
  我是侧边栏{{ $root.rootNum }}
</div>
```

对于 demo 或非常小型的有少量组件的应用来说直接使用$root的方式很方便。不过这个模式扩展到中大型应用来说就不然了，数据量过大不易维护，也不易追踪数据的变化

### 3.3 总线Bus方式

总线Bus的思路：将事件的注册和触发单独放在一个Vue实例中，点击按钮时触发指定的事件以驱动接下来的操作。Bus总线仅仅是用来驱动事件的，具体的数据操作还是在原有的组件中

在$root的结构基础上，作如下更改：
（1）原入口文件`main.js`还原，去掉data属性
（2）新定义一个总线文件`bus.js`
```javascript
import Vue from 'vue'
export default new Vue()
```
（3）side组件中注册总线事件并显示数据
```javascript
import Bus from './bus'
export default {
  data () {
    return {
      sideNum: 0
    }
  },
  created () {
    Bus.$on('change', (step) => {
      this.sideNum += step
    })
    Bus.$on('reset', () => {
      this.sideNum = 0
    })
  }
}
```
（4）bodyItem组件中分发总线事件
```javascript
import Bus from './bus'
export default {
  methods: {
    add () {
      Bus.$emit('change', 1)
    },
    reset () {
      Bus.$emit('reset')
    }
  }
}
```
**总线的方式，将原有的数据传递转换成了事件驱动的形式，这一点规避了组件层级的嵌套问题，但是开发人员无法追踪调试数据**

### 3.4 Vuex方式

由于内容较多，将在下一篇博客中详细介绍，敬请期待..