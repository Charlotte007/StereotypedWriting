## 😊 babel是什么？

Babel是一个js编译器。babel接收输入的js代码，经过内部处理流程，最终输出修改后的js代码。babel没有任何额外能力。通过plugin对外提供介入babel-core的能力。多个plugin组合在一起形成的集合，被称为preset。

![babel.png](https://i.loli.net/2021/07/27/b2c8OGtKA4FpjPM.png)
## 😊 说一说babel的原理，编译过程？

![babel-core.png](https://i.loli.net/2021/07/27/S7loDijyZR4gcf6.png)

1. 将输入的解析为AST（抽象语法树）,这一步称为`parsing`
2. 编辑AST，这一步称为`transforming`
3. 将编辑后的AST输出为代码，这一步称为`printing`

- babel-parser，用于将代码转为ast
- babel-traverse，用于遍历和更新ast节点
- babel-type，工具库，含了构造、验证以及变换 AST 节点的方法。
- babel-generator, 将ast变为代码

## 😊 babel插件的原理

Babel对代码进行转换，会将JS代码转换为AST抽象语法树(解析)，对树进行静态分析(转换)，然后再将语法树转换为JS代码(生成)。每一层树被称为节点。每一层节点都会有type属性，用来描述节点的类型。其他属性用来进一步描述节点的类型。

Visitors访问者本身就是一个对象，对象上不同的属性, 对应着不同的AST节点类型。例如，AST拥有BinaryExpression(二元表达式)类型的节点, 如果在访问者上定义BinaryExpression属性名的方法, 则这个方法在遇到BinaryExpression类型的节点, 就会执行, BinaryExpression方法的参数则是该节点的路径。注意对每一个节点的遍历会执行两次, 进入节点一次, 退出节点一次


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

## 😊 什么是@babel/preset-env？

> [babel-preset-env useBuiltins: 'usage' fails to polyfill all usages for given target](https://github.com/babel/babel/issues/9625)

@babel/preset-env，可以按需（指定core-js的版本或者指定浏览器的版本）将core-js中的特性打包（之前@babel/polyfill是全量引入的），这样可以显著减少最终打包的体积。

useBuiltIns配置了@babel/preset-env如何处理polyfill。

useBuiltIns的配置分为3个选项false（不使用垫片），entry，usage

- entry，设置entry时，需要在代码的入口文件处手动引入"core-js"和"regenerator-runtime/runtime", 根据不同环境（browsers声明的需要兼容的浏览器，如果是只需要支持最新的浏览器将不会进行转换），babel会将core-js转换不同的内容。
- usage，在每一个文件使用到垫片时，都会自动进行导入垫片的操作，只打包我们使用过的特性。（**但是如果babel-loader你设置了不处理node_modules中内容同时useBuiltIns设置为usage，可能会出现问题，node_modules中内容无法被转译**，所以对于应用程序应该使用`entry`避免这种问题）。

@babel/preset-env可以指定corejs的版本，较老的版本可能不包含最新功能的polyfill。

## 😊 什么是babel-runtime？

@babel/runtime是一个包含"Babel modular runtime helpers"(babel运行时助手)和regenerator-runtime的库。

假设一个App多个文件使用了class这个es6特性。那么每个文件打包后的module，都将包含_classCallCheck这个垫片。为了减少打包体积，应该从同一个地方引用，而不是自己维护一份。@babel-runtime目的就是让这些默认从同一个地方引用。
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

## 😊 @babel/preset-env与@babel/runtime的最佳实践

> [What is best practice for `@babel/preset-env` + `useBuiltIns` + `@babel/runtime` + `browserslistrc`](https://stackoverflow.com/questions/63231564/what-is-best-practice-for-babel-preset-env-usebuiltins-babel-runtime)
### 对于应用程序的最佳实践

> 应该使用`@babel/preset-env` + `@babel/runtime`的组合

`@babel/preset-env`的useBuiltIns设置为entry，入口文件的顶部添加` import 'core-js'`。并且引入@babel/plugin-transform-runtime和@babel/runtime。因为useBuiltIns设置为entry, 所以babel-loader不需要处理node_modules也是ok的。
### 对于库

> 只使用`@babel/runtime`

只使用@babel/plugin-transform-runtime和@babel/runtime。并将@babel/preset-env的useBuiltIns设置为false。@babel/runtime不会污染全局环境。
## 😊 在使用webpack打包js库时，webpack的配置和babel的配置冲突时js到底会打包成什么模块？

```js
// webpack的配置，打包目标是umd模块
output: {
    path: resolve(__dirname, '../dist'),
    filename: '[name].js',
    library: 'library',
    libraryTarget: 'umd',
 }

// babel的配置
[
  "@babel/preset-env",
  {
    modules: 'cjs'
  }
]   
```

经过测试，如果使用webpack打包会无视@babel/preset-env的modules配置。会打包成umd模块
## 😊 babel插件加载的顺序

- plugins：从头到尾的顺序运行
- presets：从尾到头的逆序运行
- plugins先，presets后

```js
return {
    presets: [
      "@babel/preset-env",
      [
        "@babel/preset-react",
        {
          development: isDevelopment
        }
      ]
    ],
    plugins: [
      "@babel/plugin-proposal-class-properties",
      "@babel/plugin-proposal-export-default-from",
      "@babel/plugin-proposal-export-namespace-from",
      "@babel/plugin-proposal-optional-chaining",
      "@babel/plugin-transform-runtime"
    ],
}
```

## babel对于typescript的支持有哪些限制？
