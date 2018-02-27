##《javascript高级程序设计》摘录：

> async:可选。表示应该立即下载脚本，但不应妨碍页面中的其他操作，比如下载其他资源或 等待加载其他脚本。**只对外部脚本文件有效**。

> charset:可选。表示通过 src 属性指定的代码的字符集。由于大多数浏览器会忽略它的值， 因此这个属性很少有人用。

> defer:可选。表示脚本可以延迟到文档完全被解析和显示之后再执行。**只对外部脚本文件有效**。IE7 及更早版本对嵌入脚本也支持这个属性。

> language:已废弃。原来用于表示编写代码使用的脚本语言(如 JavaScript、JavaScript1.2 或 VBScript)。大多数浏览器会忽略这个属性，因此也没有必要再用了。

> src:可选。表示包含要执行代码的外部文件。

> type:可选。可以看成是 language 的替代属性;表示编写代码使用的脚本语言的内容类型(也称为 **MIME 类型**)。这个属性并不是必需的，如果没有指定这个属性，则其默认值仍为 text/javascript。

## MIME类型

百度百科解释：
> MIME(Multipurpose Internet Mail Extensions)多用途互联网邮件扩展类型。是设定某种扩展名的文件用一种应用程序来打开的方式类型，当该扩展名文件被访问的时候，浏览器会自动使用指定应用程序来打开。多用于指定一些客户端自定义的文件名，以及一些媒体文件打开方式

通俗的将，MIME类型就是告诉浏览器用什么程序解析传递给他的文件，而前端涉及到MIME类型的也就是与后台交互时。http 协议中常见的MIME：参考[维基百科][1]
- text/plain（纯文本）
- text/html（超文本标记语言）
- image/png（PNG图像）【PHP中为：image/x-png】
- application/x-www-form-urlencoded（使用HTTP的POST方法提交的表单）

## DOM 文档的执行顺序

两个属性都是用来设定外部脚本执行的方式，在详细讲解两个属性的区别之前，先看一下DOM文档的加载顺序：
1. 解析HTML结构；
2. 加载外部脚本和样式表文件；
3. 解析并执行脚本代码；
4. DOM树构建完成；//DOMContentLoaded
5. 加载图片等外部文件；
6. 页面加载完毕；//load

由于浏览器采用至上而下的方式解析，所以会先解析html，直到遇到外部样式和外部脚本。这时会阻塞浏览器的解析，外部样式和外部脚本（在没有async、defer属性下）会并行加载，但是外部样式会阻塞外部脚本的执行（查看css和js的阻塞请[点击][2]）。
  即：html解析->外部样式、脚本加载->外部样式执行->外部脚本执行->html继续解析

## async 和 defer 对比

**async和defer属性决定了js脚本的执行方式，内嵌式的脚本会忽略这两个属性**

defer：脚本会被延迟到整个页面都解析完毕后再执行

书中的例子和解释：
```html
<!DOCTYPE html>
<head>
    <title>Example HTML Page</title>
    <script type="text/javascript" defer="defer" src="example1.js"></script>
    <script type="text/javascript" defer="defer" src="example2.js"></script>
</head>
<body>
    <!-- 这里放内容 -->
</body>
</html>
```
> 在这个例子中，虽然我们把`<script>`元素放在了文档的`<head>`元素中，但其中包含的脚本将延迟 到浏览器遇到`</html>`标签后再执行。HTML5 规范要求脚本按照它们出现的先后顺序执行，因此第一 个延迟脚本会先于第二个延迟脚本执行，而这两个脚本会先于 DOMContentLoaded 事件执行。在现实当中，延迟脚本并不一定会按照顺序执行，也不一定会在 DOMContentLoaded 事件触发 前执行，因此最好只包含一个延迟脚本

async：脚本相对于页面其他部分异步执行（同时执行）

书中的例子和解释：
```html
<!DOCTYPE html>
<head>
    <title>Example HTML Page</title>
    <script type="text/javascript" async src="example1.js"></script> 
    <script type="text/javascript" async src="example2.js"></script>
</head>
<body>
    <!-- 这里放内容 --> 
</body>
</html>
```
> 在以上代码中，第二个脚本文件可能会在第一个脚本文件之前执行。因此，确保两者之间互不依赖 非常重要。指定 async 属性的目的是不让页面等待两个脚本下载和执行，从而异步加载页面其他内容。为此，建议异步脚本不要在加载期间修改 DOM。异步脚本一定会在页面的 load 事件前执行，但可能会在 DOMContentLoaded 事件触发之前或之后执行

以上内容纯粹式学术的的探讨和老旧代码的维护，不介意做过多的纠结。因为我们都会**将css外链放在head元素内，而将js脚本放在文档的最后**


  [1]: https://zh.wikipedia.org/wiki/%E5%A4%9A%E7%94%A8%E9%80%94%E4%BA%92%E8%81%AF%E7%B6%B2%E9%83%B5%E4%BB%B6%E6%93%B4%E5%B1%95
  [2]: http://www.2cto.com/kf/201406/305852.html