## undefined 和 null 的简单介绍
undefined 和 null 都是简单数据类型，且对应的我数据类型下有且仅有一个值，分别为undefined和null；

《javascript高级程序设计》书中这样描述：
> 在使用 var 声明变量但未对其加以初始化时，这个变量的值就是 undefined；
> 从逻辑角度来看，null 值表示一个空对象指针，而这也正是使用 typeof 操作符检测 null 值时会返回"object"的原因；

JavaScript的最初版本是这样区分的：**null是一个表示"无"的对象，转为数值时为0；undefined是一个表示"无"的原始值，转为数值时为NaN**
```
Number(undefined)
// NaN

Number(null)
// 0
```

## 目前的用法
null和undefined在实际使用中基本是同义的，只有一些细微的差别。

**null表示"没有对象"，即该处不应该有值**。典型用法是：
（1） 作为函数的参数，表示该函数的参数不是对象。
（2） 作为对象原型链的终点。

```javascript
Object.getPrototypeOf(Object.prototype)
// null
```

**undefined表示"缺少值"，就是此处应该有一个值，但是还没有定义**。典型用法是：
（1）变量被声明了，但没有赋值时，就等于undefined。
（2）调用函数时，应该提供的参数没有提供，该参数等于undefined。
（3）对象没有赋值的属性，该属性的值为undefined。
（4）函数没有返回值时，默认返回undefined。

```
var i;
i // undefined

function f(x){console.log(x)}
f() // undefined

var  o = new Object();
o.p // undefined

var x = f();
x // undefined
```

## 如何区分为定义和未初始化

```javascript
var age;

alert(age); // 'undefined'
alert(msg); // 报错：age is not defined

alert(typeof age); // undefined
alert(typeof msg); // undefined
```

困惑：对未初始化的变量执行 typeof 操作符会返回 undefined 值，而对未声明 的变量执行 typeof 操作符同样也会返回 undefined 值。我们应该如何区分？

```javascript
// 判断某个变量x是否声明
try{
    x in window;
    alert(true);
    // ...
} catch(e) {
    alert(false);
    // ...
}
```

参考：[阮一峰 undefined与null的区别][1]


  [1]: http://www.ruanyifeng.com/blog/2014/03/undefined-vs-null.html