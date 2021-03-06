## 进制表示

谈到Number类型，不得不提进制（此处不做过多介绍）
**八进制字面值的第一位必须是零(0)，然后是八进制数字序列(0~7)**
**十六进制字面值的前两位必须是 0x，后跟任何十六进制数字(0~9 及 A~F)**

```
var intNum = 55;         // 整型

var octalNum1 = 070;     // 八进制的56
var octalNum2 = 079;     // 无效的八进制-解析为79

var hexNum1 = 0xA;       // 十六进制的10
var hexNum2 = 0x1f;      // 十六进制的31
```

## 浮点数值

**所谓浮点数值，就是该数值中必须包含一个小数点，并且小数点后面必须至少有一位数字**。虽然小 数点前面可以没有整数，但我们不推荐这种写法。以下是浮点数值的几个例子:  
  
```
var floatNum1 = 1.1;
var floatNum2 = 0.1;
var floatNum3 = .1; // 有效，但不推荐
```

由于保存浮点数值需要的内存空间是保存整数值的两倍，因此 ECMAScript 会不失时机地将浮点数值转换为整数值。显然，**如果小数点后面没有跟任何数字，那么这个数值就可以作为整数值来保存**。同样地，**如果浮点数值本身表示的就是一个整数(如 1.0)，那么该值也会被转换为整数**，如下面的例子所示:

```
var floatNum1 = 1.; // 小数点后面没有数字——解析为 1 
var floatNum2 = 10.0; // 整数——解析为 10
```

对于那些极大或极小的数值，可以用 e 表示法(即科学计数法)表示的浮点数值表示。用 e 表示法表示的数值等于 e 前面的数值乘以 10 的指数次幂。ECMAScript 中 e 表示法的格式也是如此，即**前面是一个数值(可以是整数也可以是浮点数)，中间是一个大写或小写的字母 E，后面是10的幂中的指数，该幂值将用来与前面的数相乘**。下面是一个使用 e 表示法表示数值的例子:

```
var floatNum = 3.125e7; // 等于31250000
```

**注意：** 0.1+0.2 !== 0.3

**浮点类型精度控制的两个方法 toFixed / toPrecision**

`toFixed`是Number原型上实现的一个方法，其作用是对一个浮点数进行四舍五入并保留固定小数位。 toFixed需要传递一个参数，其调用方式如下：

```
100.456001.toFixed(2); //100.47

100.456001.toFixed(3); //100.456

Number.prototype.toFixed.call(100.456001,2);  //100.47
```

`toPrecison`也是Number原型上实现的一个处理浮点数的方法，**和toFixed不同的是，它是对一个浮点数进行四舍五入并保留固定长度的有效数字，包括整数部分**

```
99.456001.toPrecision(5);  //99.456

100.456001.toPrecision(5); //100.46

Number.prototype.toPrecision.call(10.456001,5);  //10.456
```

## Infinity / -Infinity

由于内存的限制，ECMAScript 能够表示的最小数值为5e-324，能够表示的最大数值为1.7976931348623157e+308；

当超出这个范围时这个数值将被自动转换成特殊的 Infinity 值。具体来说，如果这个数值是负数，则会被转换成-Infinity(负无穷)，如果这个数值是正数，则会被转换成 Infinity(正无穷)

**通过 `isFinite()` 判断数值是否超出限制**
非数字类型会先转换成数值类型再做判别
未超出限制返回true，超出限制返回false
```
isFinite(NaN); // false
isFinite(Infinity); // false
isFinite(-Infinity); // false
isFinite(0); // true
isFinite('a'); // false
```

## NaN

NaN，即非数值(Not a Number)是一个特殊的数值，这个数值用于表示一个本来要返回数值的操作数未返回数值的情况(这样就不会抛出错误了)。

两个特点：
（1）**任何涉及 NaN 的操作(例如 NaN/10)都会返回 NaN**
（2）**NaN 与任何值都不相等，包括 NaN 本身**

**通过 `isNaN()` 判断变量是否为NaN**
非数字类型会先转换成数值类型再做判别
不是数值返回true，是数值返回false

```
alert(isNaN(NaN)); //true
alert(isNaN(10)); //false(10 是一个数值)
alert(isNaN("10")); //false(可以被转换成数值 10)
alert(isNaN("blue")); //true(不能转换成数值)
alert(isNaN(true)); //false(可以被转换成数值 1)
```


