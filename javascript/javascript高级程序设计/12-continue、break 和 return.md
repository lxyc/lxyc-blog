## return 语句

return语句的几个特点：
（1）return语句应用范围只能出现在函数体内，出现在代码中的其他任何地方都会造成语法错误
（2）return语句就是用于指定函数返回的值
（3）return false 能够阻止提交表单或者继续执行下面的代码，通俗的来说就是阻止执行默认的行为

return的例子1：
```
function returnFn() {
    for(var i = 0; i < 10; i++) {
        if(i == 5) {
            return i;
        }
        console.log(i);
    }
}
console.log("===", returnFn());
// 0 1 2 3 4 === 5
```

return 的例子2:
```
<a href="http://www.baidu.com" onclick=" return fn()">点击</a>

<script type="text/javascript">
function fn(){
    location.href="http://www.sina.com.cn";
    return false;
}
</script>
```
单击超链接后会跳转到新浪而不会跳转到百度，如果没有renturn false 则会跳转到百度。因为`return false;`阻止了默认行为

return 的几个常见用法：
（1）取消默认行为（参照上例）
（2）链式编程
```
var a = {
    b: function(bb) {
        console.log(bb)
        return this;
    },
    c: function(cc) {
        console.log(cc)
        return this;
    },
    d: function(dd) {
        console.log(dd)
        return this;
    }
}
a.b(1).c(2).d(3);
// 1
// 2
// 3
// Object {}
```
（3）闭包（之后章节详细讲解）

## continue 和 break 语句

> break 和 continue 语句用于在循环中精确地控制代码的执行。其中，break 语句会立即退出循环，强制继续执行循环后面的语句。而 continue 语句虽然也是立即退出循环，但退出循环后会从循环的顶部继续执行

break的例子：

```
function breakFn() {
    for(var i = 0; i < 10; i++) {
        if(i == 5) {
            break;
        }
        console.log(i);
    }
}
breakFn();
// 0 1 2 3 4
```

break: 直接跳出 当前 的循环，从当前循环外面开始执行,忽略循环体中任何其他语句和循环条件测试。它只能跳出一层循环，如果你的循环是嵌套循环，那么你需要按照你嵌套的层次，逐步使用break来跳出。

continue的例子：

```
function continueFn() {
    for(var i = 0; i < 10; i++) {
        if(i == 5) {
            continue;
        }
        console.log(i);
    }
}
continueFn();
// 0 1 2 3 4 6 7 8 9
```

continue: 终止当前的一次循环过程，其不跳出循环,而是继续往下判断循环条件执行语句。只能结束循环中的一次过程,但不能终止循环继续进行。

## continue break 与 label
break 和 continue 语句都可以与 label 语句联合使用，从而返回代码中特定的位置。这种联合 使用的情况多发生在循环嵌套的情况下

break + label 的例子：
```
var num = 0;
outermost:
for (var i=0; i < 10; i++) {
     for (var j=0; j < 10; j++) {
        if (i == 5 && j == 5) {
            break outermost;
        }
num++; }
}
alert(num);    //55
```
在这个例子中，outermost 标签表示外部的 for 语句。如果每个循环正常执行 10 次，则 num++ 语句就会正常执行 100 次。换句话说，如果两个循环都自然结束，num 的值应该是 100。但内部循环中 的 break 语句带了一个参数:要返回到的标签。添加这个标签的结果将导致 break 语句不仅会退出内 部的 for 语句(即使用变量 j 的循环)，而且也会退出外部的 for 语句(即使用变量 i 的循环)。为此， 当变量 i 和 j 都等于 5 时，num 的值正好是 55

continue + label 的例子：
```
var num = 0;
outermost:
for (var i=0; i < 10; i++) {
    for (var j=0; j < 10; j++) { 
        if (i == 5 && j == 5) { 
            continue outermost;
        }
        num++; 
    }
}
alert(num);    //95
```
在这种情况下，continue 语句会强制继续执行循环——退出内部循环，执行外部循环。当 j 是 5 时，continue 语句执行，而这也就意味着内部循环少执行了 5 次，因此 num 的结果是 95

