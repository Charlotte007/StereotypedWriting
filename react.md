## React基础API回顾

## 😊 说一说react ssr

### 说一说ssr和csr的区别

CSR渲染过程:
  1. 浏览器请求HTML
  2. 浏览器请求JS
  3. React初始化，监听事件，Ajax请求
  4. 页面可见并可用

SSR渲染过程:
  1. 浏览器请求HTML
  2. 页面可见（但此时页面不可用）
  3. 浏览器请求JS, 进行水合操作(添加事件监听，以及数据的脱水和注水)
  4. 页面可用
### ssr一定比csr快吗?

在首屏渲染上SSR比CSR要快。主要体现在http请求数量上，ssr只需要请求一个html文件就能展现出页面，虽然在服务器上会调取接口，但服务器之间的通信要远比客户端快，甚至是同一台服务器上的本地接口调取。第二屏页面由于CSR预先加载了脚本，而SSR则需要执行一次完整的请求，因此CSR更快。因此通用的做法是在首屏使用SSR，之后的页面渲染采用CSR。
### ssr有什么缺点?

1. 服务端压力较大（html渲染以及数据的请求）
2. 学习成本相对较高，项目复杂
3. SSR减小了FCP时间，但是如果TTI时间过长，用户可能认为页面无响应。

### ssr和预渲染的区别?

1. 预渲染:服务端直接返回预先生成的HTML给浏览器。
2. SSR: 服务端即时渲染HTML返回浏览器。

### react ssr的具体流程梳理

1. `node`服务器监听任意`url`的请求。使用`react-router-config`的`matchRoutes`方法对`url`进行匹配。
2. `matchRoutes`方法会以数组的形式返回匹配的组件。我们需要遍历匹配的组件，并调用它们暴露的初始化页面数据的方法，数据会存储到`redux`的`store`中。需要注意的是，`node`服务器是一个长期运行的进程，为了避免多个请求共享同一个`store`, 我们需要为每一次的请求生成一个`store`。
3. 如果`url`地址不被匹配或者需要重定向，我们需要使用`node`服务器**显示**的发送重定向到客户端。
4. 如果`url`被匹配，我们需要调用`react`的`renderToString`方法，结合之前获取页面的初始数据。生成`html`并返回。`html`中需要插入用于水合的`script`标签。以及在`window`上挂载页面初始化的数据，数据注入。
5. `html`返回浏览器后，`js`文件加载后，会对页面进行水合，绑定事件。以及数据脱水将之前`window`上挂载的数据同步到客户端的`store`中。
6. 对于`css`我们使用`isomorphic-style-loader`插件处理，将首屏的`css`注入到`html`模版中。这样首屏的html包含了css文件，如果单纯的由客户端添加会产生样式的闪烁。

## 😊 renderToString, renderToStaticMarkup的区别

- `renderToString`, 将`React Component`转化为`HTML`字符串，生成的`HTML`的`DOM`会带有额外属性：各个 DOM会有`data-react-id`属性，第一个`DOM`会有`data-checksum`属性。
- `renderToStaticMarkup`, 同样是将`React Component`转化为`HTML`字符串，但是生成`HTML`的`DOM`不会有额外属性。在客户端`hydrate`完成后, 由于`HTML`字符串不携带`data-reactid`属性，前端的水合文件会使用`innerHTML`重新覆盖`react-target`中的内容。页面会闪烁一下。

## 😊 render，hydrate的区别

- 调用`hydrate`, 如果已经具有此服务器渲染标记的节点(`renderToStaticMarkup`返回的不具有标记)，React将保留它并仅附加事件处理程序。
- 当首次调用`render`时，容器节点里的所有`DOM`元素都会被替换。后续的调用则会使用`diff`算法进行高效的更新。使用`render`方法对服务端渲染容器进行水合操作的方式已经被废弃。
## 😊 react的优化的方法

1. 颗粒化可控组件。例如，一个表单的`state`由一个庞大父组件的`state`控制，表单的更新可能会导致其他子组件不必要的更新。将表单单独抽象为一个组件，做到`state`隔离。
2. 使用`React.PureComponent`, `React.memo`, `shouldComponentUpdate`
  - `React.PureComponent`对`props`和`state`进行浅层比较。
  - `React.memo`默认会对`props`进行浅层比较。`React.memo`的第二个参数可以自定义`props`比较, 返回`true`不更新, 和`shouldComponentUpdate`相反。
  - 使用`shouldComponentUpdate`判断`state`和`props`是否更新。返回`true`更新，返回`false`不更新。
3. 绑定事件尽量不要使用箭头函数。对于组件直接绑定箭头函数，每次`props`都会更新。对于`dom`元素，会重新声明一个新事件。
4. 循环中正确的使用`key`，可以方便复用节点。
5. 在函数组件之中使用`useCallback`或者`useMemo`避免在更新时变量，方法的重复声明。
6. 代码分割
  - 基于路由的分割
  - 使用`Suspense`和`lazy`实现组件懒加载
7. 批量更新，无论是在`class`组件还是`fc`组件。更新都会合并。但是在`setTimeout`或者`Promise`等异步代码中批量更新会失效。可以使用`react-dom`提供的`unstable_batchedUpdates`手动批量更新。
8. 使用`useMemo`, `React.memo`隔离组件避免重复渲染。(👇见下面的示例代码)。
9. 对于在`jsx`中没有使用的状态, `class`组件可以直接使用实例的属性保存，对于`fc`组件可以使用`useRef`。
10. 时间分片, 使用`requestAnimationFrame`，或者使用`setTimeout`分割渲染任务，比如从一次性渲染`100000`个列表，使用`requestAnimationFrame`分割成多次渲染。因为`requestAnimationFrame`会在每一次渲染之前执行，使用`requestAnimationFrame`可以分割成多次渲染，每一次渲染`10000`条。
11. 超长列表可以使用虚拟列表技术。实际只渲染部分列表

```jsx
import { useState, useEffect } from 'react';
// 在fc组件中使用useMemo隔离
// 在class组件中使用React.memo隔离
```

## 😊 useEffect对应的生命周期

- componentDidMount
- componentDidUpdate
- componentWillUnmount

## 函数组件和class组件的区别

## 😊 setState到底是异步还是同步
## setState如何获取更新后的值

## setState的原理

## React.lazy的原理

## React的最新特性

## 😊 说一说React Fiber

`React Fiber`架构主要有两个阶段, `reconciliation(协调)`和`commit(提交)`, 在协调阶段会发生: 更新state和props, 调用生命周期, diff, 更新DOM的操作。如果`React`同步遍历整个组件树，可能会造成页面卡顿。所以`React`需要一种可以随时中断，随时恢复遍历的数据结构。`React Fiber`本质是一个链表树，每一个`Fiber`节点上包含了`stateNode`, `type`, `alternate`, `nextEffect`, `child`, `sibling`, `return`等属性。`React`的`nextUnitOfWork`变量会保留对当前`Fiber`节点的引用。以便随时恢复遍历。
## 😊 说一说React事件机制

React的合成事件都挂载在`document`对象上。当真实`DOM`元素触发事件，会冒泡到`document`对象后，处理`React`合成事件。合成事件不能像原生事件一样使用`return flase`的形式阻止默认行为，必须使用`e.preventDefault`方法。

合成事件的机制的优势：

1. 浏览器兼容，实现更好的跨平台
2. 方便事件统一管理和事务机制
### Reactv17事件机制的改动？为什么这样改动?

为了更好的实现跨平台。`React v17`版本不在将事件绑定在`document`上。而不绑定在了`React.render`的目标节点上。

### React事件与原生事件执行顺序

先执行原生事件，然后处理React合成事件。

## 说一说React Diff
## 了解React Scheduler吗？

## 说一说对Time Slice的理解?

## useState缓存的原理

## react生命周期

## useState的闭包问题

## useReducer比redux好在哪里？

## React Route的原理（前端路由的原理）

## 虚拟DOM相比原生DOM的优劣

## React组件通信

### 父子组件

- 父组件给子组件添加props
### 子父组件

- 父组件通过props传递callback，子组件调用父组件传递的callback
### 跨级组件

- 状态管理工具
- 多层props传递
- context
### 平行组件

- EventBus（发布订阅）
- 状态提升
- 状态管理工具

## React页面如何优先渲染某一部分?


