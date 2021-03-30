## 说一说webpack做了什么?
## 说一说webpack打包的流程

## 说一说Loader和Plugin的区别?

![image.png](https://i.loli.net/2021/03/30/PkRF3SUhJ5EcjWY.png)
### Loader

`Loader`在生成`bundle`的期间和之前工作，在单个文件的级别上工作。`Loader`不依赖`hook`。`Loader`在`module.rules`中配置。

### Plugin

`Plugin`在`bundle`和`chunk`级别上工作，通常在`bundle`生成的末尾工作。`Plugin`比`Loader`具有更强大的控制能力。`Plugin`依赖hook，`Plugin`会在特定的时刻加入打包的过程，改变输出的结果。`Plugin`在`plugins`中配置。

## 什么是sourceMap?

## 如何编写Loader吗?

## 如何编写Plugin吗?


## 说一说Webpack5的新特性?

## 说一说热更新的原理?

## 长效缓存如何配置?

## webpack构建速度优化

## webpack如何做拆包，为什么做拆包？