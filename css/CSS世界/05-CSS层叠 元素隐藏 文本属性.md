## 写在前面

《CSS世界》这本书还剩六章，但是这本书的精华部分主要是前面的内容，这里仅把后面章节相对重要的内容以博客展示，想着了解更多的小伙伴还是去阅读原文的好，毕竟三百多页的一本书并不是小小几篇博客能完全说清楚的。

这个也算是《CSS世界》读书笔记系列博客的终结篇了，虽说是“二进宫”，收获依然满满，毕竟好书每次读都能读出新东西。这里还是要吐槽几句，前端岗位很多同学（肯定不包括帅气的你啦！）都不太重视CSS，不就是布局吗，记几条属性也就能够实现一个网页布局，但是正是这些同学，往往在做适配和页面调整时抱怨不断，或者在调整时几乎就是重构，CSS代码丝毫没有健壮性可言，熟不知合理使用CSS属性和健壮布局能为你省很多事，甚至能够为你省去很多js代码。这里似乎写出了“CSS重要性”的论断，哈哈！

## 一、CSS世界的层叠规则

**声明：CSS中并不只是z-index能够决定元素的z轴顺序。**

### 1.1 元素层叠顺序(stacking level)

![](/css/assets/stacking_level.jpg)

补充说明：  
1. 位于最下面的 background/border 特指**层叠上下文元素**（后面会详解）的边框和背景色。**每一个层叠顺序规则仅适用于当前层叠上下文元素的小世界；**  
2. inline水平盒子指的是包括inline/inline-block/inline-table元素的“层 叠顺序”，它们都是同等级别的；  
3. 单纯从层叠水平上看，实际上 z-index:0 和 z-index:auto 是可以看成是一样的，实际上，两者在层叠上下文领域有着根本性的差异

记忆要诀：
1. **由于网页是图文展示为主，inline会高于标准流盒子和浮动盒子**
2. **元素一旦成为定位元素，其 z-index 就是默认的 auto**，根据上面的层叠顺序表，就会覆盖 inline 或 block 或 float 元素

### 1.2 层叠上下文(stacking context)

> 层叠上下文是一个概念，跟“块状格式化上下文” (BFC)类似。然而，概念这个东西是比较虚、比较抽象的，要想轻松理解，我们需要将其具象化。

> 怎么个具象化法呢?我们可以把层叠上下文理解为一种“层叠结界”，自成一个小世界。界中可能有其他的“层叠结界”，而自身也可能处于其他“层叠结界”中。

**如何创建层叠上下文**

（1）**天生派**：页面根元素天生具有层叠上下文，称为根层叠上下文

（2）**正统派**：z-index 值为数值的定位元素的传统“层叠上下文”

`对于position值为relative/absolute以及Firefox/IE(不包括Chrome)下含有position:fixed声明的定位元素，当其z-index值不是auto的时候，会创建层叠上下文`

（3）**扩招派**：CSS3属性创建

1. **元素为flex布局元素(父元素display:flex | inline-flex\)，同时z-index值不是auto**
2. **元素的opacity值不是1**
3. **元素的transform值不是none**
4. 元素mix-blend-mode值不是normal
5. 元素的filter值不是none
6. 元素的isolation值是isolate
7. 元素的will-change属性值为上面2~6的任意一个(如will-change: opacity、will-chang:transform等)
8. 元素的-webkit-overflow-scrolling设为touch

[CSS3创建层叠上下文引证](https://codepen.io/lxyc/pen/xJmjOV)：
```html
<!-- 2在1之上 -->
<img src="1.jpg" style="position:relative">
<img src="2.jpg" style="transform:scale(1);">

<!-- 1在2之上 -->
<img src="2.jpg" style="transform:scale(1);">
<img src="1.jpg" style="position:relative">
```

由于层叠问题引发的bug：

**描述**：有一个绝对定位的黑色半透明层覆盖在图片上。但是，一旦图片开始走 fadeIn 淡出的 CSS3 动画，文字就跑到图片后面去了，因为文字一直是 100%透明的纯白色，文字变淡是因为跑到图片后面，而图片半透明，文字穿透显示而已

**原因**：fadeIn 动画本质是 opacity 透明度的变化，“元素的opacity值不是1”时会创建层叠上下文，为1时为普通元素。导致层级发生变化时引发bug

![](/css/assets/stacking_fadeIn.gif)

### 1.3 层叠黄金准则（重点）

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

思考：在不居中层级错乱时，可以找找父级元素的层级与目标元素(或父级)层级比较

### 1.4 zIndex可以为负值

对照着上面的层叠顺序图，我们可以知道zIndex为负值时是介于背景和普通流盒子之间的，直接看一个[例子，请问下面的层叠情况](https://codepen.io/lxyc/pen/YjdLYE)

```css
/* 情况一 */
<div class="father">
  <div class="son">son</div>
</div>

/* 情况二 */
<div class="father transform">
  <div class="son">son</div>
</div>

.father {
  width: 200px;
  height: 200px;
  background-color: rgba(0, 0, 255, .7);
}
.son {
  position: relative;
  z-index: -1;
  width: 200px;
  height: 100px;
  background-color: rgba(255, 0, 0, .7);
}

.transform {
  margin-top: 20px;
  transform: scale(1);
}
```

![](/css/assets/stacking_zIndex.jpg)

分析：

情况一：当`.father`是普通元素时，`.son`元素所在的层叠上下文元素一是`.father`的某个祖先元素；`.son`是 z-index 负值元素，`.father`是 block 元素，也就是`.son`应该在`.father`元素的后面显示，因此，`.son`会被`.father`元素的蓝色背景覆盖；

情况二：当`.father`是层叠上下文元素时，z-index 负值元素在层叠上下文元素的背景上，因此，`.son`在`.father`元素的蓝色背景上；

### 二、元素的显示与隐藏

1. 如果希望元素不可见，同时不占据空间，辅助设备无法访问，但资源有加载，DOM 可访问，则可以直接使用 display:none 隐藏
```css
  .dn { display: none; }
```

2. 如果希望元素不可见，同时不占据空间，辅助设备无法访问，但显隐的时候可以有transition 淡入淡出效果
```css
.hidden {
      position: absolute;
      visibility: hidden;
}
```

3. 如果希望元素不可见，不能点击，辅助设备无法访问，但占据空间保留，则可以使用 visibility:hidden 隐藏
```css
.vh { visibility: hidden; }
```

4. 如果希望元素不可见，不能点击，不占据空间，但键盘可访问，则可以使用 clip 剪裁隐藏
```css
.clip {
      position: absolute;
      clip: rect(0 0 0 0);
}
```

5. 如果希望元素不可见，不能点击，但占据空间，且键盘可访问，则可以试试 relative 隐藏。例如，如果条件允许，也就是和层叠上下文之间存在设置了背景色的父元素， 则也可以使用更友好的 z-index 负值隐藏
```css
.lower {
      position: relative;
      z-index: -1;
}
```

6. 如果希望元素不可见，但可以点击，而且不占据空间，则可以使用透明度
```css
.opacity {
      position: absolute;
      opacity: 0;
      filter: Alpha(opacity=0);
}
```

7. 如果单纯希望元素看不见，但位置保留，依然可以点可以选，则直接让透明度为 0
```css
.opacity {
      opacity: 0;
      filter: Alpha(opacity=0);
}
```

