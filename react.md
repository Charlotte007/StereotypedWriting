## react ssr的原理和流程

## renderToString, renderToStaticMarkup的区别

- `renderToString`, 将`React Component`转化为`HTML`字符串，生成的`HTML`的`DOM`会带有额外属性：各个 DOM会有`data-react-id`属性，第一个`DOM`会有`data-checksum`属性。
- `renderToStaticMarkup`, 同样是将`React Component`转化为`HTML`字符串，但是生成`HTML`的`DOM`不会有额外属性。在客户端`hydrate`完成后, 由于`HTML`字符串不携带`data-reactid`属性，前端的水合文件会使用`innerHTML`重新覆盖`react-target`中的内容。页面会闪烁一下。

## render，hydrate的区别

- 调用`hydrate`, 如果已经具有此服务器渲染标记的节点(`renderToStaticMarkup`返回的不具有标记)，React将保留它并仅附加事件处理程序。
- 当首次调用`render`时，容器节点里的所有`DOM`元素都会被替换。后续的调用则会使用`diff`算法进行高效的更新。使用`render`方法对服务端渲染容器进行水合操作的方式已经被废弃。
## react的优化的方法

1. 颗粒化可控组件。例如，一个表单的`state`由一个庞大的组件的`state`控制，表单的更新可能会导致其他子组件不必要的更新。
2. 使用`React.PureComponent`, `React.memo`, `shouldComponentUpdate`
  - `React.PureComponent`对`props`和`state`进行浅层比较。
  - `React.memo`默认会对`props`进行浅层比较。`React.memo`的第二个参数可以自定义`props`比较, 返回`true`不更新, 和`shouldComponentUpdate`相反。
  - 使用`shouldComponentUpdate`判断`state`和`props`是否更新。返回`true`更新，返回`false`不更新。
3. 绑定事件尽量不要使用箭头函数。对于组件直接绑定箭头函数，每次`props`都会更新。对于`dom`元素，会重新声明一个新事件。
4. 循环中正确的使用`key`，可以复用节点。
5. 在函数组件之中使用`useCallback`或者`useMemo`避免在更新时变量，方法的重复声明。
6. 代码分割
  - 基于路由的分割
  - 使用`Suspense`和`lazy`实现组件懒加载
7. 批量更新，在`setTimeout`或者`Promise`之中，批量更新失效。使用`unstable_batchedUpdates`, 手动批量更新。
8. 合并`state`
9. 没有必要的`state`
10. 时间分片
11. 虚拟列表
12. 隔离单元
## useEffect对应的生命周期

## 函数组件和class组件的区别

## React页面如何优先渲染某一部分?

## setState如何获取更新后的值

## React.lazy的原理

## React的最新特性

## 说一说React Diff

## 说一说React fiber

## useState缓存的原理

## react生命周期

## useState的闭包问题

## useReducer比redux好在哪里？

## React事件机制

## 了解react scheduler吗？

