## vue3

### Compositon API
#### 解决了什么问题？（优势 VS mixin）
+ data, methods, computed , watch 过于零散，一个功能需要分散到四个属性中查找，实现
+ mixin 存在命名冲突，来源不明，逻辑覆盖 等原因
#### setup
+ 执行顺序：setup（无this） ，beforeCreated（无props，data，methods），created（initProps， initMethods，initData 完成）
#### setup
+ 
#### setup


#### 
1.怎样复用有细微差别的组件,例如各种各样的表格，做到高可复用性
+ 组件的分类：
  + 页面组件，页面的功能展示，封装要求度低
  + 基础功能组件，如 日历，多选，穿梭框；侧重点是 API 的设计、兼容性、性能、以及复杂的功能；最好是独立，不依赖第三方如vuex，pina，BUS等，拿来即用；
  + 业务组件：复用的重复功能
+ 组件的三个API，三要素，对外暴露的接口：（从使用角度看）
  + prop入参
  + event，$emit, $on等，主要是通信
  + slot 应对扩展变动，内容分发
2.vue2项目平滑无bug的向vue3过度，包括各种组件库的升级
  + [vue2升级vue3的迁移指南-官方](https://v3-migration.vuejs.org/zh/)

  + vue2和vue3的差异（大框架下）
    + 破坏性的api变更，移除 $on, $off, $once, 事件总线通信被移除；（需要推翻重写）
    + 变更的api数量较多
    + ** 颠覆式的设计模式（开发模式）**：vue3采用composition-api （组合式API）的模式开发，偏向函数式开发，向react靠拢，开发更加自由；
      + vue2采用的是options-api，基于配置项开发；
      + 有点类似于angular1.x 升级到 angular2.x，但angular升级基本抛弃的原有的设计，属于是新框架
    + 生态系统的更新速度，目前主流框架，插件等已同步到vue3
    + 构建工具的差异，vue-cli -> vite
  + 如何应对差异，平滑迁移
    + 官方迁移指南中包含不少vue2/3 兼容包，引入后可以磨平部分api带来的差异；
    +  GoGoCode gogocode-plugin-vue 可以基于代码生成AST树，实现语法的转化；（减少工作量）
    +  @vue/compat (即“迁移构建版本”) 是一个 Vue 3 的构建版本，提供了可配置的兼容 Vue 2 的行为；（只是过渡方案，）
        时间充裕后还是需要重构为vue3版本，更为稳妥
    +  vue2的项目较大，迁移难度变高，可以使用vue2.7作为最后的方案，支持vue3.0大多数特性；（兜底方案）
    +  (迁移的细节-官方)[https://v3-migration.vuejs.org/zh/migration-build.html]

3.接口返回的数据怎么加密，不明文显示，防止数据爬取
    + AES 对称加密
    + RSA 非对称加密
  
4.开发环境下，app.js和chunk-vendors.js过大，提高加载速度，和工作效率
    + app.js 业务代码，main.js中引入的代码; main.js等非路由页面内容;
      + 路由懒加载，webpack注释魔法，可以将页面组件单独拆除，减小体积，按需加载；
        + chunk-487885f6.4f62eeab.js 这种属于页面的js
      + 开发环境可以使用 require() 代替 import；对于es6规范和commonjs规范来说，经过babel编译以后，都会转化成commonjs规范；（减少编译，提高速度）
        + AMD推崇依赖前置、提前执行（require），CMD推崇依赖就近、延迟执行（sea.js）
        + CommonJS用同步的方式加载模块 (NodeJS)
    + chunk-vendors.js 是node-modules中该项目依赖的包
      + UI组件库等，按需加载，系统使用的组件；
      + 使用cdn连接 + externals 提取项目依赖；提高速度，减少编译，打包；
      + splitChunk 代码分包，分批加载
      + 使用dll，打包必要的依赖，本地引用，减少每次编译，打包
      + 使用vite, 基于esmodle，直接整合后本地加载速度更快；
    + 额外优化（开发环境）：
      + 关闭sourceMap，或开启 `简易模式`
  

5.低代码平台搭建，通过拖拽生成数据报表页面
    + 低代码，（积木报表：Excel）
    + 可拖拽的物料库: 饼图，折线图，Excel等 （zky，门店装修等）
    + 拖拽搭建为模板
    + 为各个物料配置数据来源
    + 数据回显，下载为pdf，或提供打印；