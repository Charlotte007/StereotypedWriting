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

### 变量对象(VO)/活动对象(AO)

变量对象VO, 是一个与执行上下文相关的特殊属性，它存储着上下文中以下内容：变量, 函数的声明（不包含函数表达式，但是可以将函数表达式保存到变量中）, 函数的形参。在全局上下文的VO，可以通过VO的属性名称直接进行访问。全局对象自身就是VO。在其他上下文是无法直接访问VO对象的

```js
var a = 10;
function foo(x) {
  var b = 20;
};
foo(30);
```

```shell
# 上面的代码，上下文堆栈和VO对象
ECStack = [
  # foo的上下文
  functionContext: {
    VO: {
      x: 30,
      b: 20,
    }
  }
  # 全局上下文
  globalContext: {
    VO: {
      a: 10
      foo: <reference to function>
    }
  }
]
```

#### 全局上下文的变量对象VO

全局对象是在进入任何上下文之前就已经创建的对象，全局对象只存在一份。全局对象在创建阶段，会将`Math`、`String`、`Date`、`parseInt`作为自身属性，进行初始化。其他的全局变量也会当作全局对象的属性。全局上下文的VO，就是全局对象自己 `globalContext.VO === global`

#### 函数上下文的变量对象

函数执行上下文中VO无法直接访问。在函数的执行上下文中使用活动对象AO替代变量对象VO的功能。AO是在进入函数执行上下文时被创建的。

#### 处理上下文之中代码的两个阶段

##### 阶段：进入执行上下文

在代码进入执行上下文时，还没有开始执行时，VO/AO就已经包含了下面的属性

- 形参, 形参会作为AO对象的属性，如果传参了属性会被赋值，否则是undefined。
- 函数声明，函数名和函数的内容作为变量对象的key和value（函数声明的优先级会高于变量声明，如果变量声明与函数声明有相同属性，函数声明会替代变量声明，函数声明在进入上下文的阶段就会被赋值, 所以在函数声明前调用它不会报错）
- 变量声明，变量的声明优先级在形参和函数声明之后。

##### 阶段：执行上下文中的代码

接下来进入代码执行阶段，此时AO/VO对象都已经有了属性，但是属性值还是undefined，代码执行阶段VO属性会被更新赋值。为什么js中变量的默认值是undefined的？因为在进入执行上下文的阶段，VO/AO对象的属性值就被赋予了undefined，所以js变量值默认是被赋予了undefined，而不是天生就是undefined。即使永远走不到的分支代码，也会赋值为undefined，这是因为在进入执行上下文的阶段放入VO中被赋值为undefined。


## 😊 说一说作用域

在JS中允许嵌套函数，每一个函数都有自身的变量对象VO，由于函数无法直接访问VO，对于函数来说就是AO活动对象。而作用域链则是AO的列表。作用域链作用是用于变量的查询。函数的执行上下文，在函数调用时创建。包含了VO/AO属性，以及[[scope]]属性。而作用域链Scope属性等于VO + `[[scope]]`属性

### [[scope]]属性

`[[scope]]`是当前执行上下文的属性，`[[scope]]`属性包含所有父变量对象（VO）的层级链。`[[scope]]`在函数创建时被创建并存储，不可变。直到函数被销毁，也就是说作用域链在定义函数时就已经确定的，即使函数不被调用，它的的`[[scope]]`属性也已经被写入了。

```js
function foo () {}

// foo函数的`[[scope]]`属性
// 包含了父变量对象，比如全局对象的VO
fooContext.[[scope]] = [
  globalContext.VO
]
```

### 函数激活

进入上下文，创建AO/VO对象后，AO/VO对象会添加Scope属性的第一位，这对于标示符解析很重要，因为变量名的查找是从最深处开始的，由于当前的AO/VO对象会放到第一位，所以局部的变量优先级在查找时会高于父作用域的变量。
Scope属性 = [当前执行上下文的AO/VO, [[scope]]]。


### 修改作用域链

with和catch会修改作用域链

```js
var foo = {x: 10, y: 20};
 
with (foo) {
  alert(x); // 10
  alert(y); // 20
}

```

被修改的作用域链 `Scope = [foo, AO|VO, [[Scope]]]`

```js
var x = 10, y = 10, z = 10;
 
with ({x: 20, z: 20}) {
  // 这里修改的是with增加的x
  // 但是y修改的是外面的y(没有块级作用域)
  var x = 30;
  var y = 30;
  
  // 这里访问的with增强的x和z
  alert(x); // 30
  alert(y); // 30
  alert(z); // 20
}

alert(x); // 10
alert(y); // 30
alert(z); // 10
```

## 😊说一说闭包

## 说一说事件循环


## 说一说原型和原型链


## 说一说继承

## 说一说this

## typeof

## instanceof

## 移动端的布局

## generater原理

## async和awiat原理

## map和对象的区别


## 说出输出结果JS面试题
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
