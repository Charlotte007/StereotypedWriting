## 输出结果类型的题目# 
### 😊 ['1', '2', '3'].map(parseInt)

parseInt(解析的值, 解析值的进制) 返回 10进制数

```js
parseInt('123', 5) // 将'123'看作5进制数进行解析，返回38
parseInt('2', 2) // '2', 不能转换为2进制所以返回NaN
parseInt('12', 2) // 1, 1可以转换为2进制返回1，'2', 不能转换为2进制所以忽略
```

```js
parseInt('1', 0) // 1
parseInt('2', 1) // NaN
parseInt('3', 2) // NaN
```

## Javascript是静态作用域与动态作用域?

## 😊 说一说执行上下文 

JS控制器转到可执行代码的时候就会进入到可执行上下文(EC)。EC是抽象的概念，需要和可执行代码（executable code）概念进行区分。活动的执行上下文会组成一个堆栈。堆栈的底部是全局上下文，顶部就是当前活动的执行上下文。进入上下文，上下文被推入堆栈。离开上下文，上下文弹出堆栈。

### 全局上下文

初始的上下文堆栈, 全局上下文不包含任何function代码。

```shell
# 执行上下文堆栈
ECStack = [
  globalContext,
]
```

### 函数上下文

当进入function的时候，ECStack都会被推入新的元素(函数的执行上下文)，但是不包括还没有执行的嵌套函数的上下文。return的时候或者异常没有catch的时候，ECStack顶部的执行上下文会被弹出。当所有代码执行完成后，ECStack中只会存在一个全局执行上下文。

```js
function foo (trigger) {
  if (trigger) {
    return
  }
  // 递归调用
  foo(true);
}

foo()
```

对应的上下文堆栈的变化过程如下

```shell
# 第一次调用foo函数
ECStack = [
  <foo>functionContext,
  globalContext,
]
# 递归第二次调用foo函数
ECStack = [
  <foo>functionContext - recursion,
  <foo>functionContext,
  globalContext,
]
# 递归的函数return后
ECStack.pop()
# foo函数执行完成后
ECStack.pop()
# 最后的ECStack中只有全局上下文
ECStack = [
  globalContext,
]
```

## 说一说事件循环


## 说一说原型和原型链
## 说一说作用域

## 说一说闭包


## 说一说this

## typeof

## instanceof

## 移动端的布局
