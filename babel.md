## 😊 babel是什么？

Babel是一个js编译器。babel接收输入的js代码，经过内部处理流程，最终输出修改后的js代码。

![babel.png](https://i.loli.net/2021/07/27/b2c8OGtKA4FpjPM.png)

## 说一说babel的原理，编译过程？


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

## 什么是polyfill？

## 什么是core-js？

## 什么是@babel/polyfill？
## 什么是regenerator-runtime？

## @babel/polyfill与core-js关系

## 什么是babel-runtime？

## @babel/runtime与babel/plugin-transform-runtime之间的关系

## Babel对于typescript的支持有哪些限制？

## babel插件加载的顺序

## useBuiltIns与@babel/runtime的最佳实践

> https://stackoverflow.com/questions/63231564/what-is-best-practice-for-babel-preset-env-usebuiltins-babel-runtime


## 在使用webpack打包js库时，webpack的配置和babel的配置冲突时js到底会打包成什么模块？
