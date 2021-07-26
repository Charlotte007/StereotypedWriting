## 😊 用过哪些plugin?

- `html-webpack-plugin`, 创建HTML文件，并将打包的JS导入HTML中
- `mini-css-extract-plugin`, 可以单独提取CSS文件。它为每个包含CSS的JS文件创建一个CSS文件。
- `clean-webpack-plugin`, 用于清除之前打包的目录
- `optimize-css-assets-webpack-plugin`，用于最小化css文件
- `terser-webpack-plugin`, 用于最小化js文件
- `webpack-bundle-analyzer`, 可视化打包的结果
- `webpack.DllPlugin`,  把复用性较高的第三方模块打包到动态链接库中(比如不经常升级的React, Vue之类的库)
- `webpack.DefinePlugin`, 创建全局变量
- `image-minimizer-webpack-plugin`, 优化图像

## 😊 用过那些loader?

- babel-loader, 用于编译JS代码
- css-loader, 用来处理导入的css模块
- less-loader, 处理导入的less文件
- style-loader, 用来创建style标签，插入由css-loader处理的样式
- postcss-loader, postcss-loader有很多插件，可以实现自动添加前缀，px->rem, px->vw
- ts-loader, 编译ts文件
- url-loader，处理非js文件，比如图片，音频，字体

## 😊 说一说css-loader与style-loader的区别？

- css-loader, 用来处理导入的css模块
- style-loader, 用来创建style标签，插入由css-loader处理的样式

## 😊 webpack中loader调用的顺序?

从右向左, 从下向上

```js
// less-loader -> postcss-loader -> css-loader -> MiniCssExtractPlugin.loader
{
  test: /\.less$/,
  use: [
    {
      loader: MiniCssExtractPlugin.loader
    },
    {
      loader: 'css-loader'
    },
    {
      loader: 'postcss-loader'
    },
    {
      loader: 'less-loader'
    },
  ],
},
```



## 😊 说说webpack配置项

- entry: 入口起点配置
- output: 输出文件配置
- module: 配置loader的位置 `module: {rules: []}`
- mode: mode 环境
- resolve: 模块解析配置(常用的就是配置alias别名的)
- optimization: 配置优化代码的，压缩，拆包的
- plugins: 插件配置
- devServer: 开发服务器
- devtool: 配置source map
- target: 构建目标
- watch: 监听任何已解析文件的更改（--watch可以配置，webpack-dev-server 和 webpack-dev-middleware 里 Watch 模式默认开启。）
- performance: 性能。例如，如果一个资源超过 250kb，webpack 会对此输出一个警告来通知你。

## 😊 说一说webpack到底做了什么?

webpack只是模块打包器。可以将不同模块的文件打包整合在一起，并且保证它们之间的引用正确。需要借助不同的loader以及plugin, 实现能力的扩展，编译js文件，css文件等功能。

## 😊 说一说什么是bundle, chunk, module的区别?

![image.png](https://i.loli.net/2021/05/07/RXic8pqhtP6uoJj.png)

- 我们写的代码模块被称为module
- webpack内部打包时根据引用关系生成chunk文件
- webpack会把chunk输出为bundle文件，bundle中包含源代码和编译的代码。

一般来说一个chunk对应一个bundle。module，chunk 和bundle其实就是同一份逻辑代码在不同转换场景下的取了三个名字。

## 😊 说一说hash、chunkhash、contenthash的区别？

- hash，hash和项目的构建相关，文件名的hash和项目的构建hash一样。只要项目中有一个文件更改，构建的hash就会更改。（无论任何文件修改，所有hash都会, hash与整个项目有关）
- chunkhash，根据不同的入口文件(进行依赖文件解析, 构建对应的 chunk，对应生成对应的哈希值。（计算与同一chunk内容相关）
- contenthash，contenthash和module内容相关，如果文件内容发生变化contenthash才会发生变化
## 😊 说一说filename和chunkFilename的区别？

- filename，打包后输出的文件的名称。列在程序入口的文件。
- chunkFilename，未列在程序入口的文件，但又需要打包的文件。一般指需要懒加载的文件名称
## 😊 webpack与rollup的区别

> 我自己没有使用过rollup

1. 一般应用（业务代码）使用webpack，对于类库使用 rollup
2. webpack目前无法打包es模块的代码（组件库使用gulp），rollup可以打包出es模块的代码
3. webpack可以实现代码的拆分，rollup一次性加载全部资源

## 说一说Loader和Plugin的区别?

![image.png](https://i.loli.net/2021/03/30/PkRF3SUhJ5EcjWY.png)
### Loader

`Loader本质是一个函数。`Loader的职责是单一的，只需要关心输入和输出。`Loader`在生成`bundle`的期间和之前工作，在单个文件的级别上工作。一个文件可以链式的，经过多个`Loader`转换。`Loader`不依赖与事件。`Loader`在`module.rules`中配置。

### Plugin

`Plugin`在`bundle`和`chunk`级别上工作，通常在`bundle`生成的末尾工作。`Plugin`比`Loader`具有更强大的控制能力。`Plugin`依赖事件，`Plugin`会在特定的时刻（监听特定的事件）加入打包的过程，改变输出的结果。`Plugin`在`plugins`中配置。

## 说一说webpack打包的流程（构建的原理）

## 什么是sourceMap? 如何配置sourceMap? sourceMap文件的格式你了解吗?

### 什么是sourceMap?

打包压缩后的代码没有可读性，无法进行debug。而sourceMap是从已转换的代码映射到原始源的文件，使浏览器能够重构原始源并在调试器中显示重建的原始源。如果静态目录中包含sourceMap，并且浏览器打开了开发者工具，浏览器才会加载sourceMap。

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

## 如何编写Loader吗?编写过Loader吗?

一个`Loader`的职责是单一的，只需要完成一种转换。 如果一个源文件需要经历多步转换才能正常使用，就通过多个 `Loader`去转换。 在调用多个`Loader`去转换一个文件时，每个`Loader`会链式的顺序执行， 第一个`Loader`将会拿到需处理的原内容，上一个`Loader`处理后的结果会传给下一个接着处理，最后的`Loader`将处理后的最终结果返回给`Webpack`。

由于`Webpack`是运行在`Node.js`之上的，一个`Loader`其实就是一个`Node.js`模块，这个模块需要导出一个函数。 这个导出的函数的工作就是获得处理前的原内容，对原内容执行处理后，返回处理后的内容。

一个最简单的`Loader`源码如下：

```js
module.exports = function(source) {
  // source为compiler传递给 Loader 的一个文件的原内容
  // 该函数需要返回处理后的内容，这里简单起见，直接把原内容返回了，相当于该 Loader 没有做任何转换
  return source;
};
```

`Loader`中的`this`上下文由`Webpack`提供，可以通过`this`对象提供的相关属性，获取当前`Loader`需要的各种信息数据。

```js
module.exports = function(source) {
    const content = doSomeThing2JsString(source);
    
    // 如果 loader 配置了 options 对象，那么this.query将指向 options
    const options = this.query;
    
    /*
     * this.callback 参数：
     * error：Error | null，当无法转换原内容时，给 Webpack 返回一个 Error
     * content：String | Buffer，原内容转换后的内容
     * sourceMap：用于把转换后的内容得出原内容的 Source Map，方便调试
     * abstractSyntaxTree: 如果本次转换为原内容生成了 AST 语法树，可以把这个 AST 返回, 以方便之后需要 AST 的 Loader 复用该 AST，以避免重复生成 AST，提升性能
     */
    this.callback(null, content);
}
```

- this.query, 可以获取`Loader`的`options`对象。
- this.callback, 可以返回除了内容之外的东西。
- this.context，当前处理文件的所在目录，假如当前 Loader 处理的文件是 /src/main.js，则 this.context 就等于 /src

等等...
### 异步的Loader

有些场景下转换的步骤只能是异步完成的，例如你需要通过网络请求才能得出结果，如果采用同步的方式网络请求就会阻塞整个构建，导致构建非常缓慢。

```js
module.exports = function(source) {
    // 告诉 Webpack 本次转换是异步的，Loader 会在 callback 中回调结果
    var callback = this.async();
    someAsyncOperation(source, function(err, result, sourceMaps, ast) {
        // 通过 callback 返回异步执行后的结果
        callback(err, result, sourceMaps, ast);
    });
};
```
## 如何编写Plugin吗?编写过Plugin吗?

`Webpack`基于发布订阅模式，和`Node.js`中的`EventEmitter`相似。`Webpack`在运行过程中会广播事件，插件通过监听这些事件，就可以在特定的阶段执行自己的插件任务，从而实现自己想要的功能。`Compiler`和`Compilation`是`Webpack`两个非常核心的对象，其中`Compiler`暴露了和`Webpack`整个生命周期相关的钩子（compiler-hooks），而`Compilation`则暴露了与模块和依赖有关的粒度更小的事件钩子（Compilation Hooks）。

一个最简单的`Plugin`源码如下：

```js
class BasicPlugin{
  // 在构造函数中获取用户给该插件传入的配置
  constructor(options){
  }

  // Webpack 会调用 BasicPlugin 实例的 apply 方法给插件实例传入 compiler 对象
  apply(compiler){
    compiler.plugin('compilation',function(compilation) {
    })
  }
}

// 导出 Plugin
module.exports = BasicPlugin;
```

`Webpack`启动后，在读取配置的过程中会先初始化一个`BasicPlugin`的实例。在初始化`Compiler`对象后，再调用`basicPlugin.apply(compiler)`给插件实例传入`Compiler`对象。 插件实例在获取到`Compiler`对象后，就可以通过`compiler.plugin(事件名称, 回调函数)`监听到`Webpack`广播出来的事件。 并且可以通过`Compiler`对象去操作`Webpack`。

`Compiler`和`Compilation`都继承自`Tapable`，可以直接在`Compiler`和`Compilation`对象上广播和监听事件。`compilation.apply`和`compilation.plugin`同理。

```js
// 广播事件
compiler.apply('event-name',params);

// 监听事件
compiler.plugin('event-name',function(params) {});
```

- 插件必须是一个函数或者是一个包含`apply`方法的对象，这样才能访问`Compiler`实例。
- 传给每个插件的`Compiler`和`Compilation`对象都是同一个引用，若在一个插件中修改了它们身上的属性，会影响后面的插件;
- 异步的事件需要在插件处理完任务时调用回调函数(异步的事件会附带两个参数，第二个参数为回调函数，在插件处理完任务时需要调用回调函数)通知`Webpack`进入下一个流程，不然会卡住。
## 说一说Compile对象

`Compiler`对象包含了`Webpack`环境所有的的配置信息，包含`options`，`loaders`，`plugins`这些信息，这个对象在`Webpack`启动时候被实例化，它是全局唯一的，可以简单地把它理解为`Webpack`实例。
## 说一说Compilation对象

`Compilation`对象包含了当前的模块资源、编译生成资源、变化的文件等。当`Webpack`以开发模式运行时，每当检测到一个文件变化，一次新的`Compilation`将被创建。`Compilation`对象也提供了很多事件回调供插件做扩展。通过`Compilation`也能读取到`Compiler`对象。

`Compiler`和`Compilation`的区别在于：`Compiler`代表了整个`Webpack`从启动到关闭的生命周期，而 `Compilation`只是代表了一次新的编译。
## 了解Tree-shaking吗?Tree-shaking的原理说一说?

`JavaScript`绝大多数情况需要通过网络进行加载，然后执行，加载的文件大小越小，整体执行时间更短，所以通过`Tree-shaking`将没有使用的模块删除, 去除无用代码以减少文件体积，对`JavaScript`来说很有意义。
### Tree-shaking的原理

首先了解下`Dead Code`的概念。`Dead Code`指那些代码不会被执行，不可到达；代码执行的结果不会被用到；代码只会影响死变量（只写不读）的代码。

1. 对于`Dead Code`，`Tree-shaking`会基于AST分析，以删除无用的代码。
2. 对于无用的模块代码。`Tree-shaking`**依赖于ES6的模块特性**。由于ES6模块依赖关系是确定的，和运行时的状态无关，可以进行可靠的静态分析，这是`Tree-shaking`的基础。所以必须使用ES6模块的语法才能进行对无用模块代码的`Tree-shaking`。

## 说一说热更新的原理?

![image.png](https://i.loli.net/2021/03/31/QVpyIaE1PioUOXA.png)

`Webpack`的热更新又称热替换（Hot Module Replacement），缩写为`HMR`。 这个机制可以做到不用刷新浏览器而将新变更的模块替换掉旧的模块。

第一步, 在`Webpack`的`watch`模式下，文件系统中某一个文件发生修改，`Webpack`监听到文件变化，会重新打包，并将打包后的代码通过简单的`JavaScript`对象保存在内存中。不生成文件的原因就在于访问内存中的代码比访问文件系统中的文件更快，也减少了代码写入文件的开销。`webpack-dev-middleware`会使用`memory-fs`将`Webpack`原本的`outputFileSystem`替换成了`MemoryFileSystem`实例。这样`bundle.js`的代码就会通过简单`Javascript`对象保存在了内存中。当浏览器请求`bundle.js`文件会从内存中直接读取代码。而不是从文件系统中读取代码。

```js
// 替换fs模块, 实现从写入文件变为写入内存
if(isMemoryFs) {
    fs = compiler.outputFileSystem;
} else {
    fs = compiler.outputFileSystem = new MemoryFileSystem();
}
```

第二步, 启动`WebpackdevServer`后, 会使用`sockjs`在服务器端和浏览器端建立一个`WebSocket`， 通过`WebSocket`将`Webpack`编译和打包的各个阶段状态告知浏览器。`webpack-dev-server`会监听`compile`的`done`事件，当`compile`完成后，`webpack-dev-server`通过`_sendStatus`方法将编译打包后的新模块`hash`值发送到浏览器端。

```js
compiler.plugin('done', (stats) => {
  // 调用_sendStatus，发送新模块的hash值给浏览器
  this._sendStats(this.sockets, stats.toJson(clientStats));
  this._stats = stats;
});

Server.prototype._sendStats = function (sockets, stats, force) {
  if (!force && stats &&
  (!stats.errors || stats.errors.length === 0) && stats.assets &&
  stats.assets.every(asset => !asset.emitted)
  ) { return this.sockWrite(sockets, 'still-ok'); }
  this.sockWrite(sockets, 'hash', stats.hash);
  if (stats.errors.length > 0) { this.sockWrite(sockets, 'errors', stats.errors); } 
  else if (stats.warnings.length > 0) { this.sockWrite(sockets, 'warnings', stats.warnings); }      else { this.sockWrite(sockets, 'ok'); }
};
```

第三步, `wbpack-dev-server`修改了`webpack`配置中的`entry`属性对应的文件，在里面添加了`webpack-dev-client`的代码，这样打包后`bundle.js`文件中就会有接收`WebSocket`消息的代码了。`webpack-dev-server/client`当接收到`type`为 `hash`消息后会将`hash`值暂存起来，当接收到`type`为`ok`的消息后对应用执行`reload`操作。在`reload`操作中，`webpack-dev-server/client`会根据`hot`配置决定是刷新浏览器还是对代码进行热更新（HMR）。

在接收到`hash`消息后，将`hash`保存到`currentHash`变量中，当接收到`ok`消息后, 进行`reload`操作。如果配置了模块热更新，就调用`webpack/hot/emitter`将最新`hash`值发送给`webpack`，然后将控制权交给`webpack`客户端代码。如果没有配置模块热更新，就直接调用`location.reload`方法刷新页面。

```js
// hash消息的处理
function msgHash(hash) {
  currentHash = hash;
}
// ok消息出炉
function msgOk() {
  // 执行reload操作
  reloadApp();
}

function reloadApp() {
  // ...
  if (hot) {
    log.info('[WDS] App hot update...');
    const hotEmitter = require('webpack/hot/emitter');
    hotEmitter.emit('webpackHotUpdate', currentHash);
    // ...
  } else {
    log.info('[WDS] App updated. Reloading...');
    self.location.reload();
  }
}
```

第四步, `webpack/hot/dev-server`会监听第三步`webpack-dev-server/client`发送的`webpackHotUpdate`的消息。调用 `webpack/lib/HotModuleReplacement.runtime`（简称 HMR runtime）中的`check`方法，检测是否有新的更新，在`check` 过程中会利用`webpack/lib/JsonpMainTemplate.runtime`（简称 jsonp runtime）中的两个方法 `hotDownloadUpdateChunk`和`hotDownloadManifest`。

- `hotDownloadManifest`是调用`ajax`向服务端请求是否有更新的文件，如果有将发**更新的文件列表**返回浏览器端。
- `hotDownloadUpdateChunk`方法是通过`jsonp`请求最新的模块代码。之后将代码返回给`HMR runtime`，`HMR runtime`会根据返回的新模块代码做进一步处理，可能是刷新页面，也可能是对模块进行热更新。

两次请求的都是使用上一次的`hash`值拼接的请求文件名，`hotDownloadManifest`方法返回的是最新的`hash`值，`hotDownloadUpdateChunk`方法返回的就是最新`hash`值对应的代码块。然后将新的代码块返回给`HMR runtime`，进行模块热更新。


第五步, 模块热更新,发生在`HMR runtime`中的`hotApply`方法中。模块热替换主要分三个阶段。第一阶段，找出过期的模块和依赖关系。第二个阶段从缓存中删除过期的模块和依赖。第三个阶段是将新的模块添加到 modules 中，当下次调`__webpack_require__` (webpack 重写的 require 方法)方法的时候，就是获取到了新的模块代码了。模块热更新的错误处理，如果在热更新过程中出现错误，热更新将回退到刷新浏览器。

第六步, 当用新的模块代码替换老的模块后，但是我们的业务代码并不能知道代码已经发生变化，需要调用`HMR`的`accept`方法。

```js
if(module.hot) {
  module.hot.accept()
}
```
## 说一说如何配置长效缓存?

将`optimization`的`runtimeChunk`配置项设置为`true`, 会额外添加一个只含有`runtime`的额外`chunk`。这个`chunk`中包含了`chunk`的映射关系的列表。如果不额外提取出来，其他的模块的改动都会导致`app.js`的hash的改变。从而导致缓存失效。另外我们可以将这个额外的`chunk`的内容，使用`InlineSourcePlugin`插件直接添加到`html`中，减少了额外的`http`请求。
## 说一说如何优化webpack构建速度?

1. 使用`DllPlugin`将更改不频繁的代码进行单独编译。这将改善引用程序的编译速度，但是它增加了构建过程的复杂性。
2. 将`loaders`应用于最少数的必要模块中。（比如跳过`node_module`中的文件）。
3. 多线程，`thread-loader`可以将非常消耗资源的`loaders`转存到`worker pool`中。
4. 使用`cache-loader`,  启用持久化缓存。
5. 在`production`模式下, 如无必要可以关闭`Source Maps`。

## 说一说webpack如何做拆包?说一说为什么做拆包？

## 说一说Webpack5的新特性?
