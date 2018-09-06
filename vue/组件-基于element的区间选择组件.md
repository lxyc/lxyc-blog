公司的系统中，产品经理在设计时经常要求对某个字段进行区间阈值设置或者作为筛选条件。但是苦于element中没有非常契合的组件（slider组件并不支持两端均能设定），于是自己动手造了一个。

## 成果
最终的展示效果如下：
![](/vue/assets/threshold.gif)


### 需求
这里以项目的需求为例，基本的需求如下：
1. 分为左右值，包含左右值，正整数
2. 左侧必须大于等于1，右侧不得大于100000，右侧值必须不小于左侧
3. 默认：左侧20，右侧100000，均必填
4. 失焦校验

### 主要思路
1. 单个表单校验：必填项校验、正整数校验、区间校验
2. 关联校验：右侧阈值不得小于左侧阈值

根据上面的思路，单个表单的校验属于公共校验方法，关联校验需要分别校验（因为对比对象不同，且提示语不同），由此在自定义校验中有了如下定义：
```javascript
rules: {
        min: [
          { required: true, message: '必填项，请维护', trigger: 'blur' },
          { validator: this.validateCom, trigger: 'blur' },
          { validator: this.validateMin, trigger: 'blur' },
        ],
        max: [
          { required: true, message: '必填项，请维护', trigger: 'blur' },
          { validator: this.validateCom, trigger: 'blur' },
          { validator: this.validateMax, trigger: 'blur' },
        ],
      },
```