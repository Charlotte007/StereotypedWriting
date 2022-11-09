## 自定义组件
### 基础文件配置
+ js
  + 使用Component构造器，配置，属性，数据，方法，
  + 父组件 bind: myEvent, 本组件 this.triggerEvent('myEvent', myEventDetail, myEventOption)
+ json 
``` json
{
  "component": true,
  "usingComponents": {}
}
```
+ wxml 
  + 内置组件
  + slot
+ wxss

### behaviors，类似 mixin
+ [官方实现的computed/watch](https://github.com/wechat-miniprogram/computed)

### 数据监听 observers，类似 watch
+ 

  
### 小程序的状态管理
+ globalData 
  + 全局管理： App({... , globalData: xxx}) + behaviors + setData
  + (https://developers.weixin.qq.com/community/develop/article/doc/00088ebe4e0500035999338cb56813)
+ [Tencent westore](https://github.com/Tencent/westore)

## 分包的种类 **（重要）**
+ 普通分包
+ 独立分包，"independent": true

## 分包的加载方式
+ 分包预加载
+ 分包异步化

### 占位组件 Component  placeholder （分包异步化 或 用时注入等特性时使用）
``` json
{
  "usingComponents": {
    "comp-a": "../comp/compA",
    "comp-b": "../comp/compB",

    "comp-c": "../comp/compC" // 占位
  },
  /* 占位组件 */
  "componentPlaceholder": {
    "comp-a": "view",  // 内置
    "comp-b": "comp-c" // 自定义占位组件，必须是立即可用的，禁止 分包异步化、用时未注入
  }
}
```


## 性能优化
>[官方性能优化指南](https://developers.weixin.qq.com/miniprogram/dev/framework/performance/)
`可做为性能优化-前端方向的模板`
### 1、启动性能（加载性能）
+ 熟悉启动流程，优化细节 **（重要）**
+ 代码包体积优化
  + 分包优化
  + 避免非必要的全局自定义组件和插件
  + 控制代码包内的资源文件
    + 压缩
    + 外链引入图片
    + CDN
    + 移除无用资源（小程序的按需加载能力？）
+ 代码注入优化
  + 按需注入，用时注入（见下文 `内存优化`）
  + 减少启动过程中（App.onLaunch, App.onShow, Page.onLoad, Page.onShow），同步API的调用 （getSystemInfoSync， setStorageSync 等）
  +  getSystemInfo/getSystemInfoSync 建议启动时调用一次，缓存数据使用，避免反复获取（涉及内容较多，耗时较长）
  +  getStorageSync/setStorageSync 推荐作为数据持久化使用，避免作为数据通信，或者全局状态管理使用 （读写耗时）
+ 首屏渲染优化 （重合内容太多）
  + 资源加载层面，（按需，合并，预加载，压缩，cdn加速，缓存，等）
    + 数据预拉取 （小程序独有）
    + 周期性更新（小程序独有）
  + 渲染层面 （见 渲染性能优化）
  + 骨架屏

### 2、运行时性能 （代码层面）
+ 合理使用 setData
  + `data中只放置和渲染相关的数据`
  + 控制 `setData 的频率` 
  + `组件化`，选择合适的 setData 范围；减少更新的颗粒度
+ 渲染性能优化
  + 高频事件
  + 动画
  + 节点的数量和层级
  + 控制自定义数据，数量，以及层级
+ `页面切换优化`
  + 前一个页面，避免在 onHide/onUnload 执行耗时操作；
    + 前一个页面退出（onHide/onUnload） -> 初始化新页面数据 -> 新页面渲染
  + 首屏渲染优化 （同启动性能-首屏渲染优化）
  + 提前发起数据请求（预加载）
    + 通过 EventChannel 提前处理 新页面数据；尤其是分包时，需要加载资源的情况
    + 前一个页面，wx.navigateTo({..., success(ev){ ev.emit(xxx) }})
    + 新页面，const ev = this.getOpenerEventChannel()； ev.on(xxx)
+ 控制预加载下个页面的时机
  + handleWebviewPreload: static, 默认 200ms 开始预加载下一个页面，可能影响 页面更新（setData）卡顿
  + 可以手动触发，配置{"handleWebviewPreload": "manual"}，页面更新后手动 wx.preloadWebview(); （uni-app 中无此配置）
+ 资源加载优化（）
  + 图片等资源大小，
    + iOS 系统内存紧张时，会主动回收掉一部分 WebView，可能会导致小程序白屏，严重时会触发微信强制关闭小程序
  + 图片尽量设置固定尺寸，避免重排（DOM，渲染相关）
+ 内存优化
  + 按需注入， (原生小程序优势？ uniapp可在manifest.json 中配置)
    + app.json `{"lazyCodeLoading": "requiredComponents"}`， 未访问的页面、当前页面未声明的自定义组件不会被加载和初始化
    + 避免非必要的、复用性低的全局组件
    + 插件包和扩展库目前暂不支持按需注入，可以考虑使用分包异步化的形式引入
  + 用时注入 （前提： 开启了按需注入）
    + 使用占位组件占位
    + 第一次渲染时，真实组件不会注入，先用占位组件渲染替代
    + 整个初始化渲染结束后，占位组件会替换回真实组件，注入渲染