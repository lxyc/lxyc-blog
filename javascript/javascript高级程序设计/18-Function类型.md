这么长时间没有写博客，就是因为函数这部分比较麻烦，自己一直想抽出大把的时间来研究这个，可是结果却是一拖再拖，这样不好。有时间就写才是王道啊，不然这计划得一直卡在这里了..

## 1. 几个概念

**函数**：将代码进行封装, 复用的逻辑单元（代码）
**对象**：无序键值对的集合
**数组**：有序键值对的集合, 数组的索引号就是键
**属性**：对象属性的值是一个普通的数据
**方法**：对象的属性是一个函数
**对象的成员**：包括属性和方法

另外关于函数，你应该时刻记住的是：**函数也是对象，函数名是一个指向函数对象的指针。因此，函数也可以作为参数传递**

## 2. 函数的三种创建方式

### 2.1 函数表达式

标准语法：
```javascript
var fn = function(){}; // 只能在声明之后调用
```
有一种写法也需要注意，可能在面试题中会遇到：
```javascript
var fn = function fn1(){}; // 不会报错
```
有这样一个例子，能够很好的区分两种写法的差异
```javascript
// 标准写法
var fn = function(){
    console.log(fn); // function (){ console.log(fn) }
};
fn();
console.log(fn); // function (){ console.log(fn) }

// 非主流写法
var fn1 = function fn2() {
    console.log(fn1);
    console.log(fn2);
}
fn1();
console.log(fn1);
console.log(fn2);
```
为了更有说服力，我直接上图吧！

![clipboard.png](/img/bVUmzn)

**结论**：
**函数表达式声明方式中，function后面也可以添加函数名，但仅能在函数内部使用**

### 2.2 函数声明

语法：
```
function fn(){}; // 能够在作用域下的任何地方使用
```

### 2.3 构造函数声明

语法：
```
var fn = new Function(param1, param2, body)
```

Function构造函数可以接受任意数量的参数，但最后一个参数始终被看做函数体，相当于一个小型的js编译器（执行效率低）

```
var fn = new Function(num1, num2, “return num1+num2”);
// 等价于
function fn(num1, num2) {
    return num1 + num2;
}
// 或
var fn = function(num1, num2) {
    return num1 + num2;
}
```

### 2.3 函数声明和函数表达式的区别

1 函数声明会把整个函数体提升到最前面`function fn() {}`，因此调用的位置没有先后之分；函数表达式只会把函数名字提升`var fn = function() {};` 此时函数作为数据，因此必须**先声明后使用**

2 函数声明不能够出现在其他语句块中！**函数声明能够出现的位置： 全局环境中其他函数内部**

个人建议：**从代码可维护性出发，尽量使用函数表达式的方式来声明函数**

## 3. 函数内部属性

**1.name属性**

函数名.name => 获取字符串类型的函数名称
```
// 外部
function fn1(){};
console.log(fn1.name); // fn1
// 内部
function fn2() {
    console.log(fn2.name); // fn2
}
fn2();
```

**2.length属性**

函数名.length => 获取形参的个数，未传参时返回0
```
// 外部
function fn1(a,b){};
console.log(fn1.length); // 2
// 内部
function fn2(a,b,c) {
    console.log(fn2.length); // 3
}
fn2();
```

**3.caller属性**

函数名.caller => 获得调用函数的函数引用（函数在哪被调用），如果在全局中调用当前函数，值为null
```
var inner = function() {
    alert(inner.caller);
};
var outer = function() {
    inner();
};
outer(); // function() { inner(); };
inner(); // null
```

**4.arguments属性**

arguments只能在函数内部使用，用来获取传入的实参。是一个[伪数组][1]

其内部有如下属性：
arguments.length：获取实参的个数
arguments.callee：获取当前函数的引用，一般用在匿名函数递归中
```
// 阶乘
function factorial(num) {
    if(num <= 1) {
        return 1;
    } else {
        return num * arguments.callee(num - 1);
    }
}
```

**5.this属性**

this会在下一篇博客中详细讲解，先记住一句话：
**this的指向和函数执行的环境相关，与它声明的环境无关**

### 4.函数的形参和实参

形参：函数声明时的参数，起占位作用——函数名.length（形参个数）
实参：函数调用时的参数，实际参与运算的值—arguments.length（实参个数）

当形参个数大于实参时，多余的形参将设置成undefined
当实参个数大于形参时，无法直接获得所有的实参，通过arguments获得实参

参数传递的理解：**形参相当于一个具有局部作用域不需要声明的和实参指向相同堆地址对象的值，其类型与实参相同**



  [1]: https://segmentfault.com/a/1190000010660044