## 说一说Babel的原理

## 简单描述一下 Babel 的编译过程？

## 喜马拉雅面试题: webpack可以打包es模块吗？

不可以。组件库常规的作法是使用gulp打包es模块。

## 喜马拉雅面试题: 为什么需要打包es模块？

提供不同版本的js文件，根据不同浏览器的版本，加载不同的js文件。以达到优化js大小的目的。

```html
<!-- 直接加载es模块 -->
<script type="module" src="main.mjs"></script>
<!-- 老版本浏览器加载es5模块 -->
<script nomodule src="main.es5.js"></script>
```

## @babel/polyfill 与 core-js, regenerator-runtime之间的关系

- regenerator-runtime
- core-js
- @babel/polyfill
## babel-runtime

## babel-polyfill

## Babel 对于 TypeScript 的支持有哪些限制？
