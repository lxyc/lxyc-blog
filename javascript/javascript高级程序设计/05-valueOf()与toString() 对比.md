`MDN` 中这样描述 `valueOf()` 和 `toString()`:

```text
  JavaScript 调用 valueOf() 方法用来把对象转换成原始类型的值（数值、字符串和布尔值）。
你很少需要自己调用此函数；当遇到一种需要转换成一个原始值情况时候， JavaScript 会自动调
用此函数。

  默认情况下, valueOf() 会被每个对象Object继承。每一个内置对象都会覆盖这个方法为了返回
一个合理的值，如果对象没有原始值，valueOf() 就会返回对象自身；

  每个对象都有一个 toString() 方法，当对象被表示为文本值时或者当以期望字符串的方式引用
对象时，该方法被自动调用。默认情况下，toString() 方法被每个继承自Object的对象继承。如
果此方法在自定义对象中未被覆盖，toString() 返回 "[object type]",其中type是对象类型
```

简单的说：
**`valueOf()`函数用于返回指定对象的原始值；**
**`toString()`函数用于返回指定对象的字符串；**

| 对象         | valueOf() 返回值  | toString() 返回值 |
|:-------------|:-----------------|:------------------|
| Number       | 数字值(number)    | 数字值(string)    |
| String       | 字符串值          | 字符串值          |
| Boolean      | 布尔值            | 字符串值          |
| Object       | 对象本身          | 字符串值          |
| Function     | 函数本身          | 字符串值          |
| Date         | 以毫秒数存储的时间值(number)| 标准时间输出(string)|

```javascript
// Number
var num = 15.26540,
	num2 = new Number(15.26540);
console.log( 'valueOf', num.valueOf() === num ); // true number类型 
console.log( 'toString', num.toString() === num );// false string类型
console.log( 'valueOf', num2.valueOf() === num2 ); // false number类型 
console.log( 'toString', num2.toString() === num2 );// false string类型
console.log( num.valueOf() === num.toString());// false
console.log( num2.valueOf() === num2.toString());// false

// String
var str = "abcd",
	str2 = new String("abcd");
console.log( 'valueOf', str.valueOf() === str );// true string类型
console.log( 'toString', str.toString() === str );// true string类型
console.log( 'valueOf', str2.valueOf() === str2 );// false string类型
console.log( 'toString', str2.toString() === str2 );// false string类型
console.log( str.valueOf() === str.toString());// true
console.log( str2.valueOf() === str2.toString());// true

// Boolean
var bool = true,
	bool2 = new Boolean(true);
console.log( 'valueOf', bool.valueOf() === bool );// true string类型
console.log( 'toString', bool.toString() === bool );// false string类型
console.log( 'valueOf', bool2.valueOf() === bool2 );// false boolean类型
console.log( 'toString', bool2.toString() === bool2 );// false string类型
console.log( bool.valueOf() === bool.toString());// false
console.log( bool2.valueOf() === bool2.toString());// false

// Object
var obj = {name: "张三", age: 18};
console.log( 'valueOf', obj.valueOf() === obj );// true object类型
console.log( 'toString', obj.toString() === obj );// false string类型
console.log( obj.valueOf() === obj.toString());// false

// Function
var fn = function(){},
	fn2 = new Function('x', 'y', 'return x+y')
console.log( 'valueOf', fn.valueOf() === fn );// true object类型
console.log( 'toString', fn.toString() === fn );// false string类型
console.log( 'valueOf', fn2.valueOf() === fn2 );// true object类型
console.log( 'toString', fn2.toString() === fn2 );// false string类型
console.log( fn.valueOf() === fn.toString());// false
console.log( fn2.valueOf() === fn2.toString());// false

// Date
var date = new Date();
console.log( 'valueOf', date.valueOf() );// 1501830320383 number类型
console.log( 'toString', date.toString() );// Fri Aug 04 2017 15:05:45 GMT+0800 (中国标准时间) string类型
console.log( date.valueOf() === date.toString());// false
```

移步：[valueOf() / toString()深入分析对比][1]


  [1]: http://www.cnblogs.com/rubylouvre/archive/2010/10/01/1839748.html