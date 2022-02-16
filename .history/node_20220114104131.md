## 事件循环

> Node10及以前，事件循环和浏览器有所差异。Node11之后Node的事件循环和浏览器中一致

```js
  ┌───────────────────────────┐
┌─>│           timers          │
│  └─────────────┬─────────────┘
│  ┌─────────────┴─────────────┐
│  │     pending callbacks     │
│  └─────────────┬─────────────┘
│  ┌─────────────┴─────────────┐
│  │       idle, prepare       │
│  └─────────────┬─────────────┘      ┌───────────────┐
│  ┌─────────────┴─────────────┐      │   incoming:   │
│  │           poll            │<─────┤  connections, │
│  └─────────────┬─────────────┘      │   data, etc.  │
│  ┌─────────────┴─────────────┐      └───────────────┘
│  │           check           │
│  └─────────────┬─────────────┘
│  ┌─────────────┴─────────────┐
└──┤      close callbacks      │
   └───────────────────────────┘
```

1. timers: 执行setTimeout()和setInterval()的回调
2. pending callbacks: 执行推迟到下一次循环迭代的I/O回调。
3. idle, prepare: node内部使用
4. poll: 执行IO相关的回调
5. check: 执行setImmediate的回调
6. close callbacks: 执行关闭事件的回调，比如：`socket.on('close', ...)`

### timers

定时器指定了一个阀值，阀值到了之后，会执行定时器的回调。但是node可能不是特别准确，只能说定时器回调，将在指定的时间过后尽可能早地运行。

timers阶段，timers队列有几个回调都会依次执行。并不像浏览器端，每执行一个宏任务后就去执行一个微任务。先清空timer队列，然后清空微任务队列。

```js
// 打印的顺序是不一定的
setTimeout(() => {
  console.log('timeout')
}, 0)

setImmediate(() => {
  console.log('immediate')
})
```

但是如果将他们两个放到IO的回调之中，setImmediate先执行，因为`check`阶段在`timers`阶段的前面

```js
// setImmediate先执行
const fs = require('fs')

fs.readFile(__filename, () => {
    setTimeout(() => {
        console.log('timeout');
    }, 0)
    setImmediate(() => {
        console.log('immediate')
    })
})
```
### poll

处理poll队列的事件。（IO相关的回调）

even loop将同步执行poll队列里的回调，直到队列为空。接下来even loop会去检查有没有setImmediate()，如果有setImmediate(), event loop将结束poll阶段进入check阶段，并执行check阶段的任务队列。

如果没有setImmediate()，event loop将阻塞在该阶段等待。同时event loop会有一个检查机制，检查timer队列是否为空，如果timer队列非空，会再次进入timer阶段。

### check

setImmediate()的回调会被加入check队列中, event loop处理check队列

### setTimeout 和 setImmediate

- setImmediate 设计在poll阶段完成时执行，即check阶段；
- setTimeout 设计在poll阶段为空闲时，且设定时间到达后执行，但它在timer阶段执行

### process.nextTick

这个函数其实是独立于 Event Loop 之外的，它有一个自己的队列，当每个阶段完成后，如果存在 nextTick 队列，就会清空队列中的所有回调函数，并且优先于其他 microtask 执行。

```js
// node
// poll开始，然后清空process.nextTick，接下来是timer队列，接下来是微任务
setTimeout(() => {
 console.log('timer1')
 Promise.resolve().then(function() {
   console.log('promise1')
 })
}, 0)

process.nextTick(() => {
 console.log('nextTick')
 process.nextTick(() => {
   console.log('nextTick')
   process.nextTick(() => {
     console.log('nextTick')
     process.nextTick(() => {
       console.log('nextTick')
     })
   })
 })
})
```
## Node事件循环和浏览器端的差异

![浏览器事件循环.png](https://i.loli.net/2021/08/04/BPMuCn3FLcGmrX2.png)

浏览器环境下，microtask（微任务）的任务队列是每个macrotask（宏任务）执行完之后执行。

![node事件循环.png](https://i.loli.net/2021/08/04/XcJ1y25BW6kMTRY.png)

node中，microtask（微任务）会在事件循环的各个阶段之间执行，也就是一个阶段执行完毕，就会去执行清空microtask队列的任务。

- node中，setTimeout、setInterval、 setImmediate、script（整体代码）、 I/O 操作，是宏任务
- new Promise().then(回调) 是微任务。（process.nextTick优先级大于微任务，当每个阶段完成后，就会清空process.nextTick）

```js
// 浏览器端结果：1，2，3，4
setTimeout(()=>{
    console.log('1')
    Promise.resolve().then(function() {
        console.log('2')
    })
}, 0)

setTimeout(()=>{
    console.log('3')
    Promise.resolve().then(function() {
        console.log('4')
    })
}, 0)

// node端：1 3 2 4
// 两个timerout，添加到timer队列中
// 清空timer队列，然后清空微任务队列
setTimeout(()=>{
    console.log('1')
    Promise.resolve().then(function() {
        console.log('2')
    })
}, 0)

setTimeout(()=>{
    console.log('3')
    Promise.resolve().then(function() {
        console.log('4')
    })
}, 0)
```



## 😊 进程和线程的区别

### 进程

- 进程是程序的实体
- 每一个进程相互独立
- 线程是进程的基本执行单位
- 操作系统可以执行多个进程。但是CPU一次只能执行一个进程，CPU通过快速切换执行进程实现多个进程的执行。（多核CPU可以真正的实现多个进程的执行）
- 一个进程可以创建其他进程，这些进程被称为子进程
- 进程不与其他进程之间共享内存
- 进程的切换销毁的资源更多

### 线程

- 同一个进程下的线程会共享内存，不同的进程不会共享内存
- 同一个进程中，可能会有多个线程
- 线程切换销毁的资源更少

## 什么是守护进程

## 中间件的原理

## require原理

## node流机制

## node异常处理


## node如何允许跨域通信

## node如何创建子进程？
## node进程通信
