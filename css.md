## 😊 什么是BFC

格式化上下文。它是页面中的一块渲染区域，并且有一套渲染规则，它决定了其子元素将如何定位，以及和其他元素的关系和相互作用。具有BFC特性的元素可以看作是隔离了的独立容器，容器里面的元素不会在布局上影响到外面的元素，并且BFC具有普通容器所没有的一些特性。

**通俗一点来讲，可以把BFC理解为一个封闭的大箱子，箱子内部的元素无论如何翻江倒海，都不会影响到外部。**

### 触发BFC

- 设置浮动，除none以外的值。
- 设置绝对定位元素：position设置为absolute、fixed
- display为inline-block、table-cells、flex
- overflow除了visible以外的值 (hidden、auto、scroll)

### 触发BFC, 可以解决什么问题

1. margin重叠
2. 清浮动
## 😊 CSS盒模型

1. box-sizing: border-box; IE盒模型; width = padding + border + 内容的宽度
2. box-sizing: content-box; 标准盒模型; width = 内容的宽度
 
## 😊 `flex: 0 0 100px` 是什么意思

`flex: 0 0 100px`是`flex-grow`, `flex-shrink`, `flex-basis` 三个属性的简写

### flex-grow

![flex-grow.png](https://i.loli.net/2021/07/25/GtNyiBH2FToSaCX.png)

flex-grow，默认值为0, 即如果存在剩余空间，也不放大。如果所有项目的flex-grow属性都为1，则它们将等分剩余空间（如果有的话，如果以及填满了是不会放大的）。如果一个项目的flex-grow属性为2，其他项目都为1，则前者占据的剩余空间将比其他项多一倍。**注意，即便设置了固定宽度，也会放大**

### flex-shrink

![flex-shrink.png](https://i.loli.net/2021/07/25/Ri7QkcMgTvUYprF.png)

flex-shrink属性定义了项目的缩小比例，默认为1，即如果空间不足（有剩余空间则不会，或者空间刚刚好也不会），该项目将缩小。如果所有项目的flex-shrink属性都为1，当空间不足时，都将等比例缩小。如果一个项目的flex-shrink属性为0，其他项目都为1，则空间不足时，前者不缩小。

### flex-basis

flex-basis属性定义了在分配多余空间之前，项目占据的主轴空间（main size）。浏览器根据这个属性，计算主轴是否有多余空间。它的默认值为auto，即项目的本来大小。

flex-basis的权重要大于width

### flex: 0 0 100px

`flex: 0 0 100px`表示项目无论有多大空间，不放大，不缩小，始终保持 100px

## 😊 position有哪些值，作用分别是什么

- static(默认), 该关键字指定元素使用正常的布局行为，即元素在文档常规流中当前的布局位置。此时 top, right, bottom, left 和 z-index 属性无效。
- relative，元素先放置在未添加定位时的位置，再在不改变页面布局的前提下调整元素位置（因此会在此元素未添加定位时所在位置留下空白，relative不会脱离文档流）
- absolute，元素会被移出正常文档流，通过指定元素相对于最近的非static定位祖先元素的偏移，来确定元素位置。绝对定位的元素可以设置外边距（margins），且不会与其他边距合并。**如果没有非static定位的祖先，相对于body定位(存疑，我觉得是相对于视口🤔️)**
- fixed，元素会被移出正常文档流，并不为元素预留空间，而是通过指定元素相对于屏幕视口（viewport）的位置来指定元素位置。元素的位置在屏幕滚动时不会改变。fixed 属性会创建新的层叠上下文。当元素祖先的 transform, perspective 或 filter 属性非 none 时，容器定位由视口改为该祖先。
- sticky, 元素根据正常文档流进行定位，然后相对它的最近滚动祖先进行偏移。偏移值不会影响任何其他元素的位置。(适合做滚动到顶部然后固定的效果)

## 😊 什么是层叠上下文？

层叠上下文(stacking context)，是HTML中一个三维的概念。在CSS2.1规范中，每个盒模型的位置是三维的，分别是平面画布上的X轴，Y轴以及表示层叠的Z轴。一般情况下，元素在页面上沿X轴Y轴平铺，我们察觉不到它们在Z轴上的层叠关系。而一旦元素发生堆叠，这时就能发现某个元素可能覆盖了另一个元素或者被另一个元素覆盖。

### 什么是层叠等级?

层叠等级(stacking level，叫“层叠级别”/“层叠水平”也行)。在同一个层叠上下文中，它描述定义的是该层叠上下文中的层叠上下文元素在Z轴上的上下顺序。

层叠等级的比较只有在当前层叠上下文元素中才有意义。不同层叠上下文中比较层叠等级是没有意义的。（但是可以比较不同层叠上下文自身的等级，不同层叠上下文内部的内容互相比较是没有意义的）
### 如何产生层叠上下文？

1. HTML中的根元素`<html></html>`本身j就具有层叠上下文，称为“根层叠上下文”。(没有设置特殊的css属性时，元素都存在在根层叠上下文中)
2. 普通元素设置position属性为非static值, 并设置z-index属性为具体数值(auto不可以)，产生层叠上下文。
3. CSS3中的新属性也可以产生层叠上下文。（元素的transform不为none；元素的opacity属性值不是1；父元素的display属性值为flex|inline-flex，子元素z-index属性值不为auto的时候，子元素为层叠上下文元素；等等。。。）

### 什么是层叠顺序?

![层叠顺序.png](https://i.loli.net/2021/07/25/L1O649MCWXSmyJu.png)

> background/border"指的是层叠上下文元素的背景和边框。

“层叠顺序”(stacking order)表示元素发生层叠时按照特定的顺序规则在Z轴上垂直显示。由此可见，前面所说的“层叠上下文”和“层叠等级”是一种概念，而这里的“层叠顺序”是一种规则。当元素发生层叠时，层叠上下文的层叠顺讯遵循上图中的规则。当层叠上下文的层叠等级，相同的时，这种情况下，在DOM结构中后面的覆盖前面的。

### 🌰例子

parent在上面还是child在上面？

```html
<style>
  .box {
  }
  .parent {
    width: 200px;
    height: 100px;
    background: #168bf5;
    z-index: 1;
  }
  .child {
    width: 100px;
    height: 200px;
    background: #32d19c;
    position: relative;
    z-index: -1;
  }
</style>
</head>

<body>
  <div class="box">
    <div class="parent">
      parent
      <div class="child">child</div>
    </div>
  </div>
</body>
```

![image.png](https://i.loli.net/2021/07/25/wO1u65hd7b8THAU.png)

parent在上面，虽然.parent设置了z-index属性值，但是没有设置position属性，z-index无效，所以没有产生新的层叠上下文，.parent还是普通的块级元素。此时，在层叠顺序规则中，z-index值小于0的.child会被普通的block块级元素.parent覆盖。


```html
<style>
  .box {
    display: flex;
  }
  .parent {
    width: 200px;
    height: 100px;
    background: #168bf5;
    z-index: 1;
  }
  .child {
    width: 100px;
    height: 200px;
    background: #32d19c;
    position: relative;
    z-index: -1;
  }
</style>
</head>

<body>
  <div class="box">
    <div class="parent">
      parent
      <div class="child">child</div>
    </div>
  </div>
</body>
```

![image.png](https://i.loli.net/2021/07/25/BMke9Ob3v5mH8As.png)

child在上面。由于父级添加了`display: flex`, parent产生了新的层叠上下文。根据层叠顺序的规则。层叠上下文元素的background/border的层叠等级小于z-index值小于0的元素的层叠等级，所以z-index值为-1的.child在.parent上面。
## 😊 清除浮动的几种方式

## z-index: auto 和 z-index: 0 的区别？

## 如何水平垂直居中？

### 为什么要使用transform而不是margin-left,right

## CSS预处理带来的好处？

### less有那些优点？

## CSS选择器有那些？

## CSS选择器权重

## inline 的元素能设置宽高、margin 属性吗

## CSS3的特性

## CSS3性能优化
## 如何实现宽度不固定的正方形？

## 如何让 CSS 元素左侧自动溢出（... 溢出在左侧）？

## 如何实现一个上中下三行布局，顶部和底部最小高度是 100px，中间自适应?

## 如何判断一个元素 CSS 样式溢出，从而可以选择性的加 title 或者 Tooltip?

## 移动端1px边框如何处理？

## 通过link引入的css会阻塞页面渲染吗？

## 多行文本显示省略号？

## css modoules原理？

## rem, em

## 移动端的布局（移动端的自适应方案）有那些？如何做？