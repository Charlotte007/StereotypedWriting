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
### poll

处理poll队列的事件。（IO相关的回调）

even loop将同步执行poll队列里的回调，直到队列为空。接下来even loop会去检查有没有setImmediate()，如果有setImmediate(), event loop将结束poll阶段进入check阶段，并执行check阶段的任务队列。

如果没有setImmediate()，event loop将阻塞在该阶段等待。同时event loop会有一个检查机制，检查timer队列是否为空，如果timer队列非空，会再次进入timer阶段。

### check

setImmediate()的回调会被加入check队列中, event loop处理check队列
## Node事件循环和浏览器端的差异

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
