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

flex-grow，默认值为0, 即如果存在剩余空间，也不放大。如果所有项目的flex-grow属性都为1，则它们将等分剩余空间（如果有的话，如果已经填满了是不会放大的）。如果一个项目的flex-grow属性为2，其他项目都为1，则前者占据的剩余空间将比其他项多一倍。**注意，即便设置了固定宽度，也会放大**

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

1. HTML中的根元素`<html></html>`本身j就具有层叠上下文，称为“根层叠上下文”。
2. 普通元素设置position属性为非static值, 并设置z-index属性为具体数值(auto不可以)，产生层叠上下文。
3. CSS3中的新属性也可以产生层叠上下文。（元素的transform不为none；元素的opacity属性值不是1；父元素的display属性值为flex|inline-flex，子元素z-index属性值不为auto的时候，子元素为层叠上下文元素；元素的transform属性值不是none；等等。。。）

一旦普通元素具有了层叠上下文，其层叠顺序就会改变。
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

parent在上面，虽然.parent设置了z-index属性值，但是没有设置position属性，z-index无效，所以parent没有产生层叠上下文，parent还是普通的块级元素。此时，在层叠顺序规则中，z-index值小于0的.child会被普通的block块级元素.parent覆盖。


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

child在上面。由于父级添加了`display: flex`, parent产生了新的层叠上下文。根据层叠顺序的规则。层叠上下文元素的background/border的层叠等级小于z-index值小于0的元素的层叠等级，所以z-index值为-1的.child在.parent上面。(只是background,border在下面，但是parent的内容还是在child的上面)
## 😊 清除浮动的几种方式

1. 浮动的父级元素添加clearfix

```css
/* 浮动的父级元素添加clearfix */
.clearfix:after {
  content: '.';
  height: 0;
  display: block;
  clear: both;
}
```

2. 父级设置overflow: hidden
## 😊 z-index: auto 和 z-index: 0 的区别？

z-index:0实际上和z-index:auto单纯从层叠水平上看，是可以看成是一样的。注意这里的措辞——“单纯从层叠水平上看”，实际上，两者在层叠上下文领域有着根本性的差异。

比如当父元素设置`display: flex`时，如果`z-index: auto`不会产生层叠上下文，设置`z-index: 0`才会产生层叠上下文。

比如position属性为非static值, 并设置z-index属性为具体数值，产生层叠上下文。但是设置auto不可以
## 😊 如何水平垂直居中？

1. absolute（left: 50%, top: 50%） + margin-left(-width / 2) + margin-left(-height / 2)
2. display: flex + justify-content: center + align-items: center
3. absolute（left: 50%, top: 50%） + transform: translate(-50%, -50%);
4. line-height: 父级的高度 + margin: 0 auto;

### 为什么推荐transform而不是margin-left,right

transform是创建了新的层叠上下文，margin 则会导致重绘回流。

## 😊 CSS预处理带来的好处？

1. 语法强大，可以实现变量，嵌套，循环。
2. 可以将部分常用的less代码封装成Mixins进行复用。
3. 可以将常用样式的值设置为变量，规范化项目的样式
4. less内置了封装的函数，if(), saturate()等
5. 可以使用less循环自动生成一些类
### less有那些优点？

```less
// 声明变量
@link-color: #428bca; // sea blue

// Mixins
.ellipsis() {
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
}
// 使用Mixins
.div {
  .ellipsis()
}

// Mixins也可以使用参数
.border-radius(@radius: 5px) {
  -webkit-border-radius: @radius;
     -moz-border-radius: @radius;
          border-radius: @radius;
}
```

```less
.xkd(@n, @i: 1) when (@i =< @n) {
  .grid@{i} {
    width: (@i * 100% / @n);
  }
  /* 递归调用 */
  .xkd(@n, (@i + 1));
}

.xkd(5);


.grid1 {
  width: 20%;
}
.grid2 {
  width: 40%;
}
.grid3 {
  width: 60%;
}
.grid4 {
  width: 80%;
}
.grid5 {
  width: 100%;
}
```

## 😊 CSS选择器有那些？

- div p : 所有div 下的所有的 p 标签
- div > p : 所有 div 下第一层所有的 p 标签
- div + p : 所有 div 的兄弟 p 标签 (必须位于 div 同级别的后方)
- div ~ p: : 所有 div 的兄弟 p 标签 (必须位于 div 同级别的前方)
- [title~=flower] : 选择 html的title 属性包含单词 "flower" 的所有元素。必须是字符快
  - title="tulip flower ab" 里面的三个都行，但是 lower 就不行
- [lang|=en] : 选择 lang 属性值以 "en" 开头的所有元素。
- a[href^="https"] : 选择 href 属性值以 "https" 开头的所有 a 元素。
- a[href$=".pdf"] : 选择 href 属性值以 "pdf" 结尾的所有 a 元素。
- a[href*="abc"]: 选择其 href 属性值中包含 "abc" 子串的每个 a 元素。
- .a.b: 同时包含a类和b类的元素
- .a .b: a类元素内部的b类元素
- :nth-child(n), 匹配其父元素的第n个子元素，第一个编号为1
- :nth-last-child(n), 匹配其父元素的倒数第n个子元素，第一个编号为1
- :nth-of-type(n), 与:nth-child()作用类似，但是仅匹配使用同种标签的元素
- :nth-last-of-type(n), 与:nth-last-child() 作用类似，但是仅匹配使用同种标签的元素
- :last-child, 匹配父元素的最后一个子元素，等同于:nth-last-child(1)
- :first-child，匹配父元素的第一个子元素

```css
// 奇数
p:nth-child(odd) { color:#f00; }
// 偶数
p:nth-child(even) { color:#f00; }
```

## 😊 CSS选择器权重

- 行内样式 +1000
- id 选择器 +100
- 属性选择器、class 或者伪类 +10
- 元素选择器，或者伪元素 +1
- 通配符 +0

## 😊 inline 的元素能设置宽高、margin 属性吗

`inline`元素不能设置宽高，外边距(margin)只能设置左,右，不能设置上下。
## 😊 CSS3性能优化

1. 内联首屏关键CSS（Critical CSS）
2. CSS会阻塞渲染，在CSS文件请求、下载、解析完成之前，浏览器将不会渲染任何已处理的内容, 所以在已经内联首屏css的情况下，可以异步加载非首屏的CSS
3. CSS压缩
4. 正确的使用选择器
  - 不要使用嵌套过多过于复杂的选择器
  - 通配符和属性选择器效率最低，需要匹配的元素最多，尽量避免使用
5. 优化重排与重绘(更多细节查看浏览器章节)
6. 使用transform时，开启硬件加速

```js
// 异步加载CSS
// 创建link标签
const myCSS = document.createElement( "link" );
myCSS.rel = "stylesheet";
myCSS.href = "mystyles.css";
// 插入到header的最后位置
document.head.insertBefore( myCSS, document.head.childNodes[ document.head.childNodes.length - 1 ].nextSibling );
```
## 😊 如何实现宽度不固定的正方形？

当padding的值为百分比时，是按照widht百分比的

```less
.square {
  width: 100%;
  height: 0;
  padding-top: 100%;
  background: yellow;
}
```
## 😊 移动端1px边框如何处理？
### 伪元素 + transform

> 如果需要四条边框都是1px时，需要额外套一层。因为一层只能有两个伪元素，before和after

```js
@media (-webkit-min-device-pixel-ratio:2),(min-device-pixel-ratio:2){
  .border-bt-1px{
    position: relative;
    /* 底部的1px边框 */
    :before{
      content: '';
      position: absolute;
      left:0;           
      bottom: 0;
      width: 100%;
      height: 1px;
      background: #ee2c2c;
      transform: scaleY(0.5);
    }
  }
}
```
## 😊 通过link引入的css会阻塞页面渲染吗？

css是不是阻塞资源？css是阻塞渲染的资源。css的下载解析会导致浏览器将不会渲染任何已处理的内容，直至cssom构建完毕。但是css是不会阻塞html的解析（会阻塞渲染）。因为渲染需要DOM树和CSSOM树合并后形成渲染树。其实html也是阻塞渲染的资源，但是没有html我们也没有需要渲染的内容了。

css文件也是并行下载的，按声明顺序解析的。

## 😊 多行文本显示省略号？

```less
/* 单行文本 */
div {
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
}

/* 多行文本 */
div {
  display: -webkit-box;
  -webkit-box-orient: vertical;
  overflow: hidden;
  -webkit-line-clamp: 2;
}
```


## 😊 样式隔离有什么办法？

1. 添加前缀
2. 使用css模块化，将样式名替换为hash值
3. vue中scoped中添加额外的[data-v-469af010]标记。

```html
<div class="example" data-v-4728474d8b>hi</div>
```

```css
.example[data-v-4728474d8b] {
 color: blue;
}
```
## 😊 rem, em

1. rem: 根元素的字体大小(html的字体大小)
2. em: 相对于父元素

## 😊 移动端的布局（移动端的自适应方案）有那些？如何做？

### rem适配

> 缺点，与js耦合，在响应式布局中，必须通过js来动态控制根元素font-size的大小。

安装postcss，配置`postcss-pxtorem`(自动将px转rem)，amfe-flexible（是配置可伸缩布局方案，主要是将1rem设为viewWidth/10，就是将html的font-size设置为viewWidth/10，自动根据屏幕大小转换html的font-size的值）
### vw适配

按照postcss，配置`postcss-px-to-viewport`插件



## ⚙️ 栅格布局的原理

## 😊 如何让 CSS 元素左侧自动溢出（... 溢出在左侧）？

添加`direction: rtl;` 和 `unicode-bidi: bidi-override;`

```css
.div {
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
  width: 300px;
  text-align: right;
  direction: rtl;
  unicode-bidi: bidi-override;
}
```
## 😊 如何实现一个上中下三行布局，顶部和底部最小高度是 100px，中间自适应?

- 顶部: flex: 0 0 100px;
- 中间: flex: 1;
- 底部: flex: 0 0 100px;

## 😊 如何判断一个元素 CSS 样式溢出，从而可以选择性的加 title 或者 Tooltip?

通过判断元素的scrollWidth，和clientWidth，如果scrollWidth > clientWidth 说明溢出了

## 😊 如何实现横向自适应滚动？

```html
<!doctype html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>横向滚动条</title>
    <style>
        html,body{
            height: 100%;
            margin: 0 0;
            padding: 0 0;
        }
        .container{
            width:100%;
            overflow-x: scroll;
            overflow-y: hidden;
            white-space:nowrap;
        }
        .container img{
            width: 300px;
            margin: 10px 10px;
        }
    </style>
</head>
<body>
<div class="container">
    <img src="http://lixuanqi.com/public/meizi/9d52c073gw1ea5gzohndtj20g40ndaf4.jpg" alt="">
    <img src="http://lixuanqi.com/public/meizi/9d52c073gw1ea5gzohndtj20g40ndaf4.jpg" alt="">
    <img src="http://lixuanqi.com/public/meizi/9d52c073gw1ea5gzohndtj20g40ndaf4.jpg" alt="">
    <img src="http://lixuanqi.com/public/meizi/9d52c073gw1ea5gzohndtj20g40ndaf4.jpg" alt="">
    <img src="http://lixuanqi.com/public/meizi/9d52c073gw1ea5gzohndtj20g40ndaf4.jpg" alt="">
    <img src="http://lixuanqi.com/public/meizi/9d52c073gw1ea5gzohndtj20g40ndaf4.jpg" alt="">
    <img src="http://lixuanqi.com/public/meizi/9d52c073gw1ea5gzohndtj20g40ndaf4.jpg" alt="">
    <img src="http://lixuanqi.com/public/meizi/9d52c073gw1ea5gzohndtj20g40ndaf4.jpg" alt="">
    <img src="http://lixuanqi.com/public/meizi/9d52c073gw1ea5gzohndtj20g40ndaf4.jpg" alt="">
    <img src="http://lixuanqi.com/public/meizi/9d52c073gw1ea5gzohndtj20g40ndaf4.jpg" alt="">
</div>
</body>
</html>
```

## 😊 为什么css选择器不能嵌套太深

CSS选择器的匹配是从右向左进行的，这一策略导致了不同种类的选择器之间的性能也存在差异。相比于#markdown-content-h3，显然使用#markdown .content h3时，浏览器生成渲染树（render-tree）所要花费的时间更多。因为后者需要先找到DOM中的所有h3元素，再过滤掉祖先元素不是.content的，最后过滤掉.content的祖先不是#markdown的。试想，如果嵌套的层级更多，页面中的元素更多，那么匹配所要花费的时间代价自然更高。
