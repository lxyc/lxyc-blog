这篇博客将会从下面四个常见的应用诠释组件的生命周期，以及各个生命周期应该干什么事
1. 单组件的生命周期
2. 父子组件的生命周期
3. 兄弟组件的生命周期
4. 宏mixin的生命周期

> 生命周期：Vue 实例从开始创建、初始化数据、编译模板、挂载Dom→渲染、更新→渲染、卸载等一系列过程，我们称这是 Vue 的生命周期，各个阶段有相对应的事件钩子

## 1. 单个组件的生命周期
下面这张图是vue生命周期各个阶段的执行情况：
![](/vue/assets/lifecycle.png)

现根据实际代码执行情况分析：
```html
<template>
  <div>
    <h3>单组件</h3>
    <el-button @click="dataVar += 1">更新 {{dataVar}}</el-button>
    <el-button @click="handleDestroy">销毁</el-button>
  </div>
</template>
```

```javascript
export default {
  data() {
    return {
      dataVar: 1
    }
  },
  beforeCreate() {
    this.compName = 'single'
    console.log(`--${this.compName}--beforeCreate`)
  },
  created() {
    console.log(`--${this.compName}--created`)
  },
  beforeMount() {
    console.log(`--${this.compName}--beforeMount`)
  },
  mounted() {
    console.log(`--${this.compName}--mounted`)
  },
  beforeUpdate() {
    console.log(`--${this.compName}--beforeUpdate`)
  },
  updated() {
    console.log(`--${this.compName}--updated`)
  },
  beforeDestroy() {
    console.log(`--${this.compName}--beforeDestroy`)
  },
  destroyed() {
    console.log(`--${this.compName}--destroyed`)
  },
  methods: {
    handleDestroy() {
      this.$destroy()
    }
  }
}
```

初始化组件时，打印：
![](/vue/assets/singlePrint1.jpg)
当data中的值变化时，打印：
![](/vue/assets/singlePrint2.jpg)
当组件销毁时，打印：
![](/vue/assets/singlePrint3.jpg)

从打印结果可以看出:
1. 初始化组件时，仅执行了beforeCreate/Created/beforeMount/mounted四个钩子函数
2. 当改变data中定义的变量（响应式变量）时，会执行beforeUpdate/updated钩子函数
3. 当切换组件（当前组件未缓存）时，会执行beforeDestory/destroyed钩子函数
4. **初始化和销毁时的生命钩子函数均只会执行一次，beforeUpdate/updated可多次执行**

## 2. 父子组件的生命周期

将单组件作为基础组件（由于props在beforeCreate()中未初始化），需要做如下更改：

```javascript
props: {
  compName: {
    type: String,
    default: 'single'
  }
},
beforeCreate() {
  // this.compName = 'single'
  // console.log(`--${this.compName}--beforeCreate`)

  console.log(`  --data未初始化--beforeCreate`)
},
```

父组件代码如下：
```html
<template>
  <div class="complex">
    <h3>复杂组件</h3>
    <lifecycle-single compName="child"></lifecycle-single>
  </div>
</template>
```

```javascript
const COMPONENT_NAME = 'complex'

import LifecycleSingle from './LifeCycleSingle'

export default {
  beforeCreate() {
    console.log(`--${COMPONENT_NAME}--beforeCreate`)
  },
  created() {
    console.log(`--${COMPONENT_NAME}--created`)
  },
  beforeMount() {
    console.log(`--${COMPONENT_NAME}--beforeMount`)
  },
  mounted() {
    console.log(`--${COMPONENT_NAME}--mounted`)
  },
  beforeUpdate() {
    console.log(`--${COMPONENT_NAME}--beforeUpdate`)
  },
  updated() {
    console.log(`--${COMPONENT_NAME}--updated`)
  },
  beforeDestroy() {
    console.log(`--${COMPONENT_NAME}--beforeDestroy`)
  },
  destroyed() {
    console.log(`--${COMPONENT_NAME}--destroyed`)
  },
  components: {
    LifecycleSingle
  }
}
```

初始化组件时，打印：
![](/vue/assets/nestPrint1.jpg)
当子组件data中的值变化时，打印：
![](/vue/assets/nestPrint2.jpg)
当父组件data中的值变化时，打印：
![](/vue/assets/nestPrint3.jpg)

当组件销毁时，打印：
![](/vue/assets/nestPrint3.jpg)

