在上一篇文章（[《javascript高级程序设计》笔记：Function类型][1]）中稍微提及了一下函数对象的属性—this，在这篇文章中有深入的说明：

## 函数的三种简单调用模式

**1 函数模式**

定义的函数，如果单独调用，不将其与任何对象关联，那么就是函数调用模式

```
function fn(num1, num2) {
    console.log(this); 
}
// 直接在全局调用
fn();// window

// 在某个函数内部调用
!function(){
    fn(); // window
}()
```

`函数模式this指向window`

**2 方法模式**

定义的函数, 如果将函数作为一个对象的成员，那么利用对象调用它就是方法模式

```
var obj = {
    say: function() {
        console.log(this);
    }
};
// 直接在全局调用
obj.say(); // obj

// 在某个函数内部调用
!function(){
    obj.say(); // obj
}()
```

`方法模式this指向调用方法的对象`

**3 构造器模式**

定义的函数, 使用 new 来调用创建对象就是构造器模式

```
var Person = function(name) {
    this.name = name;// this指向当前实例对象
};
var p1 = new Person("xiong");
var p2 = new Person("lee");
```
`构造器模式this指向new创建的实例对象`

## 面试题

```
var age = 38;
var obj = {
    age: 18,
    getAge: function() {
        console.log(this.age);
    }
};

var a = obj.getAge();
console.log(a);
```

分析：函数`getAge`定义在`obj`对象中，属于方法模式，其`this`指向调用方法的函数`obj`，结果为 18；

```
var age = 38;
var obj = {
    age: 18,
    getAge: function() {
        function foo() {
            console.log(this.age);
        }
        foo();
    }
};

console.log(obj.getAge());
```
分析：`obj.getAge()`等同于直接执行
```
    function foo() {
        console.log(this.age);
    }
```
属于函数模式，其`this`指向window，结果为 38；

```
var age = 38;
var obj = {
    age: 18,
    getAge: function() {
        console.log(this.age);
    }
};

var f = obj.getAge;
f();
```
分析：分析函数的this指向要看函数的执行环境执行的，
    f = obj.getAge = function(){console.log(this.age)}
同样的，属于函数模式，其`this`指向window，结果为 38；

```
var length = 10;
function fn(){
    console.log(this.length);
}
var obj = {
    length: 5,
    method: function (fn) {
        fn();//
        arguments[0]();//
    }
};

obj.method(fn, 123);
```
分析：`坑！坑！坑！`
执行`fn();`时，属于函数模式，`this`指向为`window`，结果为 10；

执行`arguments[0]();`时，`arguments[0]`也是指向fn，然后`fn();`属于函数模式，`this`指向为`window`，结果为 10 ?

真的是这样吗，我们打印出来发现结果为 2，为什么呢？
看过上一篇文章的都应该知道，arguments是函数内部的属性，是一个伪数组（对象），索引0是其属性
```
arguments: {
    '0': function fn(){
        console.log(this.length);
    }
}
```
因此：调用方式属于方法模式，其`this`指向调用方法的函数`obj`，`this.length`为其实参个数，结果为 2；

## 借用方法调用模式

上面的三种函数调用方式中，this的指向都是确定的，但是如果对象A中包含了某个方法，对象B也想调用同样的方法，那么在不定义该方法的前提下有什么方式能够实现方法的共用呢？借用方法调用模式能够非常优雅的解决这个问题

**1 apply()与call()方法**

两个方法均能改变函数运行时的this指向，二者的作用完全一样，只是接受参数的方式不太一样
apply()接受两个参数，第一个是其中运行函数的作用域，另一个是参数数组
call()接受多个参数，第一个是其中运行函数的作用域，其他的参数都直接传给数组

```
fn.call(contextObj, arg1, arg2, arg3...)
fn.apply(contextObj, [arg1, arg2, aeg3...])

// 等效于
contextObj.fn(arg1,arg2,arg3)
```
网上有一个简单的等效，帮助理解：
foo.call(this,arg1,arg2,arg3) == foo.apply(this, arguments)==this.foo(arg1, arg2, arg3)

下面有几个实际应用例子:

（1）伪数组转换成数组
```
// arguments
// 返回值为数组，arguments保持不变
var arg = [].slice.call(arguments);

// nodeList
var nList = [].slice.call(document.getElementsByTagName('li'));
```

(2) 方法借用
```
var arr = [34,5,3,6,54,6,-67,5,7,6,-8,687];

// max、min 是 Math 中的静态方法，因此必然是没有使用上下文的必要的
Math.max.call(null, 34,5,3,6,54,6,-67,5,7,6,-8,687);
Math.max.apply(null, arr);
```

(3) 判断变量类型
```
// 判断是否为数组
function isArray(obj){
  return Object.prototype.toString.call(obj) == '[object Array]';
}
isArray([]) // true
isArray(123) // false
```

（4）实现继承
```
function Animal(name){
    this.name = name;
    this.showName = function(){
        alert(this.name);    
    }    
}

function Cat(name){
    Animal.apply(this,[name]);
    // call的用法
    Animal.call(this,name); 
}

var cat = new Cat("咕咕");
cat.showName();
```

**2 bind()方法**

bind()方法与上面两个方法最大的不同是：
**bind是返回对应函数，便于稍后调用；apply、call则是立即调用**

> bind()方法会创建一个新函数，称为绑定函数，当调用这个绑定函数时，绑定函数会以创建它时传入 bind()方法的第一个参数作为 this，传入 bind() 方法的第二个以及以后的参数加上绑定函数运行时本身的参数按照顺序作为原函数的参数来调用原函数。

```
var func = function(){
    console.log(this.x);
    console.log(arguments);
}
func();  // undefined, {}

var obj = {
    x: 2
}
var bar = func.bind(obj,1);

bar(); // 2 , {'0':1}
```

一个有意思的事：
```
var bar = function() {
    console.log(this.x);
}
var foo = {
    x: 3
}
var sed = {
    x: 4
}
var func = bar.bind(foo).bind(sed);
func(); //3

var fiv = {
    x: 5
}
var func = bar.bind(foo).bind(sed).bind(fiv);
func(); //3
```

分析：在Javascript中，多次 bind() 是无效的。更深层次的原因， bind() 的实现，相当于使用函数在内部包了一个 call / apply ，第二次 bind() 相当于再包住第一次 bind() ,故第二次以后的 bind 是无法生效的

注意：
（1）借用方法调用模式中，如果第一个参数不是对象：number string boolean，此时，会自动的被转化为其包装对象
```
number -> new Number()
string -> new String()
boolean -> new Boolean()
```

（2）**如果第一个参数为null,则this指向window(在node环境中则指向global)**
```
say.call(null); //this 指向window
say.call(window); //this同样指向window
```

参考：
1 [前端基础:call,apply,bind的的理解][2]
2 [call/apply的理解与实例分享][3]


  [1]: https://github.com/lxyc/lxyc-blog/issues/26
  [2]: https://segmentfault.com/a/1190000009650716
  [3]: https://segmentfault.com/a/1190000003977826