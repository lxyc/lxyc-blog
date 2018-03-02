## 字符串的不可变性

> ECMAScript 中的字符串是不可变的，也就是说，字符串一旦创建，它们的值就不能改变。要改变 某个变量保存的字符串，首先要销毁原来的字符串，然后再用另一个包含新值的字符串填充该变量

通俗的说，**操作字符串时并不会在原来的字符串上修改，而是重新开辟内存，生成新的字符串，把变量重新指向新的字符串，原来的字符串不会马上消失，要等待垃圾回收机制回收**。

## 实现字符串拼接的几种方式
**当拼接的字符串数目较少时，建议使用第一种**

（1）用连接符“+”把要连接的字符串连
```
str = "a";
str += "b";
```
毫无疑问，这种方法是最便捷快速的，如果只连接100个以下的字符串建议用这种方法最方便

（2）以数组作为中介用 join 连接字符串

```
var arr=new Array();
arr.push(a);
arr.push(b);
var str=arr.join("");
```

w3school 网站介绍说这种方法要比第一种消耗更少的资源，速度也更快

（3）利用对象属性来连接字符串

```
function stringConnect(){
  this._str_ = new Array();
}

stringConnect.prototype.append = function(a){
  this._str_.push(a);
}

stringConnect.prototype.toString = function(){
  return this._str_.join();
}

var mystr = new stringConnect;
mystr.append("a");
var str = mystr.toString();
```

## 字符串的转换

**快速转换：使用加号操作符把它与一个字符串("")加在一起 <==> String()**

（1）**toSting()**
**注意：undefined和null没有toString()这个方法**

```
var age = 11;
var ageAsString = age.toString(); // 字符串"11" 
var found = true;
var foundAsString = found.toString(); // 字符串"true"
```

**指定基数后能够返回指定进制的字符串**
```
var num = 10;
alert(num.toString()); // "10"
alert(num.toString(2)); // "1010"
alert(num.toString(8)); // "12"
alert(num.toString(10)); // "10"
alert(num.toString(16)); // "a"
```

(2) **String()**

这个函数能够将任何类型的值转换为字符串，String()函数遵循下列转换规则:
- 如果值有 toString()方法，则调用该方法(没有参数)并返回相应的结果; 
- 如果值是 null，则返回"null";
- 如果值是 undefined，则返回"undefined"