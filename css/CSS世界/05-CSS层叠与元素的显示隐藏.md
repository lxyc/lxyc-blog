## 写在前面

《CSS世界》这本书还剩六章，但是这本书的精华部分主要是前面的内容，这里仅把后面章节相对重要的内容以博客展示，想着了解更多的小伙伴还是去阅读原文的好，毕竟三百多页的一本书并不是小小几篇博客能完全说清楚的。

这个也算是《CSS世界》读书笔记系列博客的终结篇了，虽说是“二进宫”，收获依然满满，毕竟好书每次读都能读出新东西。这里还是要吐槽几句，前端岗位很多同学（肯定不包括帅气的你啦！）都不太重视CSS，不就是布局吗，记几条属性也就能够实现一个网页布局，但是正是这些同学，往往在做适配和页面调整时抱怨不断，或者在调整时几乎就是重构，CSS代码丝毫没有健壮性可言，熟不知合理使用CSS属性和健壮布局能为你省很多事，甚至能够为你省去很多js代码。这里似乎写出了“CSS重要性”的论断，哈哈！

## 一、CSS世界的层叠规则

**声明：CSS中并不只是z-index能够决定元素的z轴顺序。**

### 1.1 元素层叠顺序\(stacking level\)

![](/css/assets/stacking_level.jpg)

补充说明：  
1. 位于最下面的 background/border 特指**层叠上下文元素**（后面会详解）的边框和背景色。**每一个层叠顺序规则仅适用于当前层叠上下文元素的小世界；**  
2. inline水平盒子指的是包括inline/inline-block/inline-table元素的“层 叠顺序”，它们都是同等级别的；  
3. 单纯从层叠水平上看，实际上 z-index:0 和 z-index:auto 是可以看成是一样的，实际上，两者在层叠上下文领域有着根本性的差异

记忆要诀：**由于网页是图文展示为主，inline会高于标准流盒子和浮动盒子**

### 1.2 层叠上下文\(stacking context\)

**如何创建层叠上下文**  
（1）**天生派**：页面根元素天生具有层叠上下文，称为根层叠上下文

（2）**正统派**：z-index 值为数值的定位元素的传统“层叠上下文”

`对于position值为relative/absolute以及Firefox/IE(不包括Chrome)下含有position:fixed声明的定位元素，当其z-index值不是auto的时候，会创建层叠上下文`

（3）**扩招派**：CSS3属性创建

1. **元素为flex布局元素\(父元素display:flex \| inline-flex\)，同时z-index值不是auto**
2. **元素的opacity值不是1**
3. **元素的transform值不是none**
4. 元素mix-blend-mode值不是normal
5. 元素的filter值不是none
6. 元素的isolation值是isolate
7. 元素的will-change属性值为上面2~6的任意一个\(如will-change: opacity、will-chang:transform等\)
8. 元素的-webkit-overflow-scrolling设为touch

**层叠黄金准则（重点）**

（1）**谁大谁上**：当具有明显的层叠水平标识的时候，如生效的 z-index 属性值，在**同一个层叠上下文领域**，层叠水平值大的那一个覆盖小的那一个

（2）**后来居上**：当元素的层叠水平一致、层叠顺序相同的时候，在 DOM 流中处于后面的元素会覆盖前面的元素

下面有两个案例，请问两种情况的层叠顺序：
```html
<div style="position:relative; z-index:auto;">
    <img src="1.jpg" style="position:absolute; z-index:2;">  
</div>
<div style="position:relative; z-index:auto;">
    <img src="2.jpg" style="position:relative; z-index:1;">  
</div>
```

```html
<div style="position:relative; z-index:0;">
    <img src="1.jpg" style="position:absolute; z-index:2;">  
</div>
<div style="position:relative; z-index:0;">
    <img src="2.jpg" style="position:relative; z-index:1;">  
</div>
```

答案：（1）1在上2在下；（2）1在下2在上

分析：`z-index: auto`所在的`<div>`元素是一个普通定位元素，于是，里面的两个`<img>`元素的层叠比较就不受父级的影响，两者直接套用“谁大谁上”的准则;而`z-index`一旦变成数值，哪怕是 0，就会创建一个层叠上下文。此时遵循“层叠黄金准则”的另外一个准则“后来居上”，根据在DOM文档流中的位置决定谁在上面