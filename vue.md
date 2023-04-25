## Vue 如何监测数组？

### 为什么 vue2.x 有些数组的数据变更不能被 Vue 监测到（两条）？

- vm.items[1] = 10; 直接修改，无法触发响应；
- vm.items.length = 10; 直接修改数组长度，无法触发视图响应
- 使用 push()，pop()，shift()，unshift()，splice()，sort()，reverse()， 可以触发视图响应；（因为 vue 中对于数组原型中的上述方法进行重写，上述方法的特点就是 可修改原数组数据）

### vue2.x 现有监控数组的两条限制：索引赋值，修改长度，是因为 defineProperty 方法不支持吗？

- 实际上， defineProperty 是`可以监听索引赋值!!!`, (区别)，前提是对arr[i]进行绑定
- 修改数组 length，的确不能触发 setter， （兼容问题）
  - Object.defineProperty(arr, 'length', {}) 的兼容问题，可能会抛出异常
  - 可以参考 [mdn Object.defineProperty() 兼容问题](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/defineProperty#重定义数组_array_对象的_length_属性)
- 使用 修改原数组的数组方法，如 pop，push 等，也是无法触发 setter
- 【综上所述】：对数组`已有元素`进行监听，而没有对`数组本身`的变化进行监听（如 length，push 等）

```js
const arr = [1, 2, 3];
arr.forEach((val, index) => {
  Object.defineProperty(arr, index, {
    get() {
      return val;
    },
    set(newVal) {
      console.log("setter [old, new]:", [val, newVal]);
      val = newVal;
    },
  });
});

// 直接 索引赋值
// + 已有值，重新赋值， 【触发setter】
arr[0] = 4; // setter [old, new]: (2) [1, 4]

// + 没有值，通过index 赋予新的值，【不能触发setter！！！】
arr[3] = 5; // 并没有触发 setter !!!

// + 使用 修改原数组的方法 【push】
arr.push(6);

// + 使用 【unshift：会触发多次 seter，是因为 unshift 导致数组顺序的重排列，触发setter】
// TIPS：也是vue2.x 没有实现 索引赋值 监听的原因；单单就 索引赋值没有问题，但会因为 数组方法，导致多次触发；
arr.unshift(7);
// setter [old, new]: [3, 2]
// setter [old, new]: [2, 4]
// setter [old, new]: [4, 7]

// + 修改length 【扩充实际长度： 不能触发setter！！！】
arr.length = 10;

// + 修改length 【缩短实际长度： 不能触发setter！！！】
arr.length = 1;
```

### Object.defineProperty 明明可以监控 数据值 的变化，而 vue2.x （vm.items[1]=4）却没有实现呢？
+ TIPS: 注意区分，案例中是对 arr进行遍历添加 Object.defineProperty；如果仅仅只监听数组 如 Object.defineProperty(obj, 'arr', {...}) 是无法监听到 vm.items[1]= 4 的； 

- 主要是基于性能的考虑；循环给`每个元素`添加监听，实际上性能消耗很大；
  - 数组是有序数据，如 unshift，shift，reverse，splice，sort，一个方法，会导致数组数据重排，会触发多次，一个修改，多次触发，消耗较大

### 对于 vue2.x 实现的监测对象变化有什么需要注意的

- 数据需要响应，需要在 dataInit 绑定 观察者 observer，定义属性，即 vue 组件预设 data 属性，当然可以 通过 $set, $delete 动态修改
- Object.defineProperty 是对 对象的属性做操作，而不是 obj 对象本身，obj 重新赋值，不会触发 setter；（针对与内部 setter）
- this.obj = Object.assign({}, this.obj, {a:1,b:2}); 因为重新绑定了`新对象`, 会 对新对象属性重新遍历添加监听

### vue3.x 使用 proxy 实现数据响应式

> Proxy 对象用于创建一个对象的代理，从而实现基本操作的拦截和自定义（如属性查找、赋值、枚举、函数调用等）。
```js
/*
  @target 代理虚拟化的对象。
  @handler {get: Function(target, key), set: Function(target, key, newVal)}
  @return proxy 添加代理后的对象
*/
let p = new Proxy(target, handler);

```

- Proxy 支持对数组操作的拦截
- Proxy 支持对整个对象进行拦截，不在需要为每一个属性进行设置，无需遍历添加监听；
- Proxy 支持对嵌套对象的拦截，vue2.x 需要递归添加

### Object.defineProperty() 与 new Proxy() 的区别
|     | Object.defineProperty  | new Proxy |
|  ----  | ----  | ---- |
| 使用方式  | target[key] 逐个添加 | 传入target即可 |
| 额外属性  | 不支持响应，需针对绑定 | 支持target 额外属性添加，修改；监听的target，而非属性|
| 属性为对象（嵌套） | 不支持， 需要递归添加，逐个属性添加 | 不支持，需要为属性为对象，添加target |
| 数组监听 | 对数组本身不支持，可通过arr[i]逐个添加，但如pop，unshift 会大批量触发，性能较差 | 可直接监听数组本身，响应数组内部修改 |

## vue2 中属性的初始化顺序
用途：知道初始化顺序，避免在属性中，调用其他属性值错误；ip木得错误
- inject
- props： initProps
- setup: initSetup(vm) -- vue3
- methods： initMethods
- data： initData
- computed： initComputed
- watch： initWatch

# 基础
+ flex
+ css常见布局
+ 手机端适配方案与常见适配细节
+ ES6中常见新特性考察
+ 节流、防抖
+ this指向
+ 原型，原型链
+ 深拷贝与浅拷贝，以及如何实现
+ 浏览器渲染，与重绘(回流)/重排
+ http状态码，三次握手四次挥手
	+ 状态码
	+ http协议
+ EventLoop
+ promise
	+ 链式
	+ 优缺点以及应用场景
	+ 如果只new promise不reject/resove，会造成内存泄漏吗
+ 跨域
# Vue
+ 生命周期
+ watch computed
+ 组件通信方式，优缺点以及应用场景
  + 祖孙通信
    + 派发
    + 广播
    + provide
+ vue响应式原理
  + 数组实现以及原因
+ key的作用
+ v-model
+ keep-alive的使用
+ vuex，vue-router使用熟练度考察
+ Vue 组件中 data 为什么必须是函数
+ 大批量数据渲染
+ 性能优化

# vue-router
+ 路由的模式，以及实现原理
+ 路由跳转与301
+ 路由鉴权的方式有哪几种
+ 如何重置（清空）路由,有哪几种方式

# render / createElement

# 函数组件

# ** 如何封装一个通用组件，保证其适配性，扩展性 ！！（组件封装的能力）

# vuex
  + vuex中state，修改对象类型的数据，会触发响应吗
```js
// 1、如果存在 dictionaryMap.DC_ZY 属性，可以响应；不存在则不能响应；同data中 object类型的属性；data中需要依赖 $set(); vuex 中只能通过重新赋值对象
state.dictionaryMap.DC_ZY = val.DC_ZY  
Object.assign(state.dictionaryMap, val)

// 2、重新赋值，无论存不存在，都可以响应
state.dictionaryMap = {
    ...state.dictionaryMap,
    ...val
}
```

# webpack
+ 基础配置/vue-cli中基础配置
+ 性能优化，打包优化
  + 

# 项目经验考察
	+ 

# 基础编程题
+ 数组去重（基础）
+ 数组中（对象其中一个属性【比如ID变动的，但不能相同】）去重（加强）
+ 将二维数组转为一维数组
+ 字符串反转

# 团队开发
+ 开发规范，命名，文件夹，代码管理
+ 项目管理

# 项目经验
+ 如何实现保存列表页数据，当进入详情页返回列表页的时候
+ 如何实现不同域之间的cooke共享？

# 项目实现细节
+ watch在监听对象时，如何知道那个属性发生变化；如果是监听数组呢，有什么区别为什么会有这样的差异
  + 实测，对象监听会完全相等 === // true, 需要建立缓存对比；
  + 数组需要区分说明
    + 一维数组长度，长度发生变化时候，会不同
      + 但是如果里面arr[i]仅仅是对象发生变化,那是监听不出来的；=== // true
+ 如何适配多来源的接口，即接口地址不同，如果适配
  + 闭包
+ 为什么 this.resData = {}, 在请求返回后重新赋值后this.resData中的数据就是响应的，而不需要$set?
  + 原因是 resData是响应的，在对resData赋值时，会触发setter，进而通过observe方法去判断这个新set的value是否为对象，是否进行响应式初始化；
	如果没有会重新进行初始化操作
+ 多层对象如何结构？如： var obj = {  name:"张三",data:{ list : [ 1,2,3,4,5,6] }}
  + const { name: rename1,  data : { list } } = obj; // name起别名，list 二级属性结构
+ 无状态组件与有状态组件
  + 无状态组件： 没有data, 接收Props，渲染DOM，而不关注其他逻辑，抽象的说：就是对于给定的输入，会返回相同的输出; 比如渲染用户头像，角色，信息等；也就是展示组件
+ 写一个图片懒加载组件
+ 如何通过浏览器 `Performance` 分析性能? ['盆盆前端','web调试优化']
+ Vue2.x 独立（运行）构建（standalone）和运行时构建（runtime-only）有什么区别？
  + standalone = 编译器（template编译为render函数，js包大概占用16kb左右） + 运行时（调用render函数渲染）
  + runtime-only = （vue-loader + vueify 预编译）+ 运行时（调用render函数渲染） 
  + standalone = runtime-only + compiler（编译器）