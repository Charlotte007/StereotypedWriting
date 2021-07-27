## 😊 babel是什么？

Babel是一个js编译器。babel接收输入的js代码，经过内部处理流程，最终输出修改后的js代码。babel没有任何额外能力。通过plugin对外提供介入babel-core的能力。多个plugin组合在一起形成的集合，被称为preset。

![babel.png](https://i.loli.net/2021/07/27/b2c8OGtKA4FpjPM.png)

## 😊 说一说babel的原理，编译过程？

![babel-core.png](https://i.loli.net/2021/07/27/S7loDijyZR4gcf6.png)

1. 将输入的解析为AST（抽象语法树）,这一步称为`parsing`
2. 编辑AST，这一步称为`transforming`
3. 将编辑后的AST输出为代码，这一步称为`printing`

- babel-traverse，用于遍历和更新ast节点
- babel-type，工具库，含了构造、验证以及变换 AST 节点的方法。

## 😊 喜马拉雅面试题: webpack可以打包es模块吗？

不可以。组件库常规的作法是使用gulp打包es模块。

## 😊 喜马拉雅面试题: 为什么需要打包es模块？

通过`module`和`nomodule`的特性，提供不同版本的js文件，根据不同浏览器的版本，可以加载不同的js文件。以达到优化js大小的目的。

```html
<!-- 直接加载es模块 -->
<script type="module" src="main.mjs"></script>
<!-- 老版本浏览器加载es5模块 -->
<script nomodule src="main.es5.js"></script>
```

## 😊 什么是polyfill？

polyfill是什么？高级语法的降级实现

polyfill中常用的是`@babel/polyfill`和`@babel/preset-env`。他们的实现依赖于`core-js`.

## 😊 什么是core-js？

core-js是一套模块化的js标准库，包括：

- 一直到es2021的polyfill
- promise、symbols、iterators等一些特性的实现
- ES提案中的特性实现
- 跨平台的WHATWG / W3C特性，比如URL

## 😊 什么是regenerator-runtime？

regenerator-runtime是generator以及async/await的运行时依赖
## 😊 @babel/polyfill与core-js关系

> 从babelv7.4.0开始，@babel/polyfill被废弃了，可以直接引用core-js与regenerator-runtime替代

@babel/polyfill可以看作是，core-js和regenerator-runtime的集合。单独使用@babel/polyfill会将core-js全量导入，造成项目打包体积过大。

## 什么是@babel/preset-env？

## 什么是babel-runtime？

## 😊 @babel/runtime与@babel/plugin-transform-runtime之间的关系

- @babel/plugin-transform-runtime, 作为开发时的依赖。用来转换代码。
- @babel/runtime，作为生产时的依赖。转换后的代码需要依赖运行时本身所以，所以需要将@babel/runtime作为生产的依赖（需要添加到dependencies）。

还需注意的是transform-runtime的核心版本是可选的，不同的核心转换的API的范围是不同的，可以在`@babel/plugin-transform-runtime`的选项目中配置。

- corejs: false, (默认值)
- corejs: 2, 会对全局Promise变量，或者静态属性Array.from做替换。
- corejs: 3, 除了全局之外，也会对实例属性比如includes做替换。

当我们更改了transform-runtime的corejs的配置，相对应的runtime运行时的版本也需要修改

- corejs: false，对应@babel/runtime
- corejs: 2, 对应@babel/runtime-corejs2
- corejs: 3, 对应@babel/runtime-corejs3


## Babel对于typescript的支持有哪些限制？

## babel插件加载的顺序

## useBuiltIns与@babel/runtime的最佳实践

> https://stackoverflow.com/questions/63231564/what-is-best-practice-for-babel-preset-env-usebuiltins-babel-runtime


## 在使用webpack打包js库时，webpack的配置和babel的配置冲突时js到底会打包成什么模块？
