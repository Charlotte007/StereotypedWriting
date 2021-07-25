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

## position有哪些值，作用分别是什么

## 为什么要使用 transform 而不是 margin-left,right

## 清除浮动的几种方式

## CSS选择器有哪些

## 什么是层叠上下文？

## rem, em

## CSS预处理带来的好处

## CSS 选择器

## CSS 权重

## inline 的元素能设置宽高、margin 属性吗

## CSS3的特性

## CSS3性能优化

## 层叠上下文

## 如何水平垂直居中

## 如何实现宽度不固定的正方形

## 如何让 CSS 元素左侧自动溢出（... 溢出在左侧）？

## 如何实现一个上中下三行布局，顶部和底部最小高度是 100px，中间自适应?

## 如何判断一个元素 CSS 样式溢出，从而可以选择性的加 title 或者 Tooltip?

## 移动端1px边框

## 通过 link 引入的 css 会阻塞页面渲染吗？

## 多行文本显示省略号

## css modoules原理


## 移动端的布局（移动端的自适应方案）