## 说一说webpack做了什么?
## 说一说webpack打包的流程

## 😊 说一说Loader和Plugin的区别?

![image.png](https://i.loli.net/2021/03/30/PkRF3SUhJ5EcjWY.png)
### Loader

`Loader`在生成`bundle`的期间和之前工作，在单个文件的级别上工作。`Loader`不依赖`hook`。`Loader`在`module.rules`中配置。

### Plugin

`Plugin`在`bundle`和`chunk`级别上工作，通常在`bundle`生成的末尾工作。`Plugin`比`Loader`具有更强大的控制能力。`Plugin`依赖hook，`Plugin`会在特定的时刻加入打包的过程，改变输出的结果。`Plugin`在`plugins`中配置。

## 😊 什么是sourceMap? 如何配置sourceMap? sourceMap文件的格式你了解吗?

### 什么是sourceMap?

打包后的代码没有可读性，无法进行debug。而sourceMap是从已转换的代码映射到原始源的文件，使浏览器能够重构原始源并在调试器中显示重建的原始源。如果静态目录中包含sourceMap，并且浏览器打开了开发者工具，浏览器才会加载sourceMap。

### 如何配置sourceMap?

在配置文件的`devtool`属性中配置sourceMap。常见的配置项：

- `hidden-source-map`, 借助第三方错误监控平台Sentry使用
- `nosources-source-map`, 创建的sourcemap不包含sourcesContent(源代码内容)。它可以用来映射客户端上的堆栈跟踪，而无须暴露所有的源代码。你可以将sourcemap文件部署到web服务器。
- `sourcemap`,  整个sourceMap作为一个单独的文件生成。它为bundle添加了一个引用注释，以便开发工具知道在哪里可以找到它。

等等……

### sourceMap文件的格式你了解吗?

```js
{
  "version": 3, // SourceMap的版本
  "sources": [ // 转换前的文件来源
    "webpack:///webpack/universalModuleDefinition",
    "webpack:///webpack/bootstrap"
  ],
  "names": [], // 转换前的变量名
  "mappings": "AAAA,AACA;AACA", // 位置信息
  "file": "react-ui-components-library.js", // 转换后的文件名
  "sourceRoot": "" // 转换前的文件目录,
  "sourcesContent":[] // 原始文件内容
}
```

对于压缩后源代码，需要在末尾添加`//# sourceURL=/path/to/file.js.map`注释后，浏览器就会通过`sourceURL`去查找对应的`sourceMap`文件，通过解释器解析后，实现源码和混淆代码之间的映射。因此`sourceMap`其实也是一项需要浏览器支持的技术。

## 如何编写Loader吗?

一个`Loader`的职责是单一的，只需要完成一种转换。 如果一个源文件需要经历多步转换才能正常使用，就通过多个 `Loader`去转换。 在调用多个`Loader`去转换一个文件时，每个`Loader`会链式的顺序执行， 第一个`Loader`将会拿到需处理的原内容，上一个`Loader`处理后的结果会传给下一个接着处理，最后的`Loader`将处理后的最终结果返回给`Webpack`。

由于`Webpack`是运行在`Node.js`之上的，一个`Loader`其实就是一个`Node.js`模块，这个模块需要导出一个函数。 这个导出的函数的工作就是获得处理前的原内容，对原内容执行处理后，返回处理后的内容。

一个最简单的`Loader`源码如下：

```js
```

## 如何编写Plugin吗?

## 说一说如何配置长效缓存?

## 说一说热更新的原理?

## 说一说Webpack5的新特性?

## webpack构建速度优化

## webpack如何做拆包，为什么做拆包？