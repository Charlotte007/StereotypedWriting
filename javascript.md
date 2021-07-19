## 😊 JS的数据类型

Boolean、Null、Undefined、Number、BigInt、String、Symbol 和 Object

## 😊 Javascript是静态作用域(词法作用域)与动态作用域?

Javascript是静态作用域, 函数的作用域在函数定义的时候就确定了。动态作用域是在函数调用的时候才确立。

```js
var value = 1;

function foo() { console.log(value); }

function bar() { var value = 2; foo(); }

// 如果是静态作用域，打印1
// 如果是动态作用域，打印2
bar();
```

## 😊 说一说执行上下文 

活动的执行上下文会组成一个上下文堆栈。堆栈的底部是全局上下文，顶部就是当前活动的执行上下文。

进入函数时上下文，函数的上下文被推入堆栈(但是不包含函数中嵌套函数的上下文)。当函数`return`时，函数上下文弹出堆栈。当所有代码执行完成后，ECStack中只会存在一个全局执行上下文。

### 变量对象(VO)/活动对象(AO)

变量对象VO, 是一个与执行上下文相关的特殊属性，它存储着上下文中以下内容：**变量**, **函数的声明（不包含函数表达式，但是包含保存函数表达式的变量）**, **函数的形参**，它们作为VO对象的属性。

全局对象是在进入任何上下文之前就已经创建的对象，全局对象只存在一份。全局对象在创建阶段，会将`Math`、`String`、`Date`、`parseInt`作为自身属性进行初始化。其他的全局变量也会当作全局对象的属性。全局上下文的VO，就是全局对象自己 （`globalContext.VO === global`）
#### 函数上下文的变量对象

函数执行上下文中VO无法直接访问。在函数的执行上下文中使用活动对象AO替代变量对象VO的功能。AO是在进入函数执行上下文时被创建的。
#### 处理上下文之中代码的两个阶段
##### 阶段1：进入执行上下文

在代码进入执行上下文时，还没有开始执行时，VO/AO就已经包含了下面的属性

- 形参, 形参会作为AO对象的属性，如果传参了属性会被赋值，否则是undefined。
- 函数声明，函数名和函数的内容作为变量对象的key和value（函数声明的优先级会高于变量声明，如果变量声明与函数声明有相同属性，函数声明会替代变量声明，函数声明在进入上下文的阶段就会被赋值, 所以在函数声明前调用它不会报错，在代码执行阶段函数声明不会重复的赋值）
- 变量声明，变量的声明优先级在形参和函数声明之后。

##### 阶段2：执行上下文中的代码

接下来进入代码执行阶段，此时AO/VO对象都已经有了属性，并且属性值还是undefined，代码执行阶段VO属性会被更新赋值。

为什么js中变量的默认值是undefined的？因为在进入执行上下文的阶段，VO/AO对象的属性值就被赋予了undefined，所以js变量值默认是被赋予了undefined，而不是天生就是undefined。

即使永远走不到的分支代码，也会赋值为undefined，这是因为在进入执行上下文的阶段放入VO中被赋值为undefined。

## 😊 说一说作用域

在JS中允许嵌套函数，每一个函数都有自身的变量对象VO，由于函数无法直接访问VO，对于函数来说就是AO活动对象。而作用域链则是AO的列表。作用域链作用是用于变量的查询。函数的执行上下文，在函数调用时创建。包含了VO/AO属性，以及[[scope]]属性。而作用域链Scope属性，等于VO + `[[scope]]`属性

### [[scope]]属性

`[[scope]]`是当前执行上下文的属性，`[[scope]]`属性包含所有父变量对象（VO）的层级链。`[[scope]]`在函数创建时被创建并存储，不可变。直到函数被销毁，也就是说作用域链在定义函数时就已经确定的，即使函数不被调用，它的的`[[scope]]`属性也已经被写入了。
### 函数激活

进入上下文，创建AO/VO对象后，AO/VO对象会添加Scope属性的第一位，这对于标示符解析很重要，因为变量名的查找是从最深处开始的，由于当前的AO/VO对象会放到第一位，所以局部的变量优先级在查找时会高于父作用域的变量。
Scope属性 = [当前执行上下文的AO/VO, [[scope]]]。
### 修改作用域链

with和catch会修改作用域链，被修改的作用域链 `Scope = [with的参数, AO|VO, [[Scope]]]`。

## 😊 说一说闭包

> MDN对闭包的定义为：**闭包是指那些能够访问自由变量的函数。**

> 那什么是自由变量呢？自由变量是指在函数中使用的，但既不是函数参数也不是函数的局部变量的变量。

> 由此，我们可以看出闭包共有两部分组成：闭包 = 函数 + 函数能够访问的自由变量

**闭包是代码块和创建该代码块的上下文中数据的结合。**函数的父级上下文数据保存在函数的内部属性`[[scope]]`中，由于js是使用静态词法作用域的语言，包含了父级上下文的数据的`[[scope]]`属性，在函数创建时就已经保存了，不管函数有没有调用。因为作用域链，在js中所有函数都是闭包，因为它们在创建的时候，都保存了上层上下文的作用域链。在同一个上下文中创建的闭包，共用一个`[[scope]]`属性，对`[[scope]]`的修改，会影响到其他闭包。也就是说同一个上下文创建的闭包，共享同一个父作用域。

## 😊 说一说事件循环

### 什么是事件循环？

JS的执行，浏览器的渲染，DOM存储都在**主线程**上进行。浏览器会衍生出一些其他线程用来处理网络请求，监控输入设备等。一旦衍生线程，需要页面响应，它们会通知主线程，这就是**事件循环**做的事情。**监听衍生线程，并通知主线程**。

### 任务队列

对于`setTimeout`, `setTimeout`会向任务队列中添加任务。任务队列在主线程之外，并行等待，等待完成后，会回到主线程当中。当任务回到主线程后，主线程会去执行添加的任务。
### 浏览器渲染

浏览器渲染的过程包含了CSS样式计算, 构建渲染树, 计算元素的位置像素, 然后绘制。浏览器渲染和执行任务分别**主线程的两端**。
主线程必须执行任务后才会进行浏览器渲染的操作。但是遇到了`while(true)`, 相当于遇到了一个永远无法执行完成的任务，所以浏览器永远不会进行渲染操作，导致浏览器卡死。主线程的工作流程可以简单概括为，`执行任务 --> 浏览器渲染 --> 执行任务 --> 浏览器渲染`。但是需要注意的是，**执行任务后，浏览器是不能保证一定会重新渲染的，可能我们多次执行任务之后，浏览器才会渲染一次**。

### requestAnimationFrame

requestAnimationFrame只会在浏览器渲染前执行，和宏任务，微任务无关。

### 微任务

微任务到底会在什么时候执行，会在宏任务结束后执行吗？这种说法只能说部分正确。正确的说法应该是宏任务堆栈为空时执行。
### 事件循环的整体顺序

1. 执行宏任务
2. 宏任务执行栈为空，查询是否有微任务需要执行
3. 清空微任务栈
4. 可能会执行requestAnimationFrame，然后渲染浏览器
5. 下一轮事件循环
### 事件循环的题目

```js
// 1, 2, 3, 4, 5, 6
setTimeout(() => {
  console.log(1)
  new Promise((resolve, reject) => {
    console.log(2)
    resolve()
  }).then(() => {
    console.log(3)
  })
}, 0)

setTimeout(() => {
  new Promise((resolve) => {
    resolve()
  }).then(() => {
    console.log(4)
    new Promise((resolve) => {
      resolve()
    }).then(() => {
      console.log(5)
    })
  }).then(() => {
    console.log(6)
  })
}, 0)
```

## 😊 说一说原型链

![原型链](https://i.loli.net/2021/07/18/uBlOcmVhWFSpvX2.png)

JavaScript 只有一种结构：对象。每个实例对象（object）都有一个私有属性（称之为 __proto__ ）指向它的构造函数的原型对象（prototype）。该原型对象也有一个自己的原型对象（__proto__），层层向上直到一个对象的原型对象为 null。根据定义，null 没有原型，并作为这个原型链中的最后一个环节。(MDN的解释)

## 😊 说一说继承
### ES5继承实现

![es5.png](https://i.loli.net/2021/04/12/iaXDQAPbq93sLl6.png)

```js
/**
 * 寄生组合式继承
 */
// 父类
function Father(name) {
  this.name = name
}
Father.prototype.sayName = function() {
  console.log(this.name)
}

// 子类
// 继承构造函数
function Son(name, age) {
  Father.call(this, name)
  this.age = age
}

// 修正原型链
Son.prototype = Object.create(Father.prototype)
Son.prototype.constructor = Son

Son.prototype.sayAge = function() {
  console.log(this)
}
```
### ES6继承实现

![es6继承.png](https://i.loli.net/2021/04/12/2CO5HRe4bda9GzK.png)

```js
class Father {
  constructor(name) { this.name = name }
  sayName() {
	  console.log(this.name)
  }
}

class Son extends Father {
  constructor(name, age) {
	  super(name)
	  this.age = age
  }
  sayAge(age) {
	  console.log(this.age)
  }
}
```
### ES5继承和ES6继承有什么区别?

- `ES5`是先创建子类实例对象的`this`，然后再将父类的方法添加到`this`上面（`Parent.apply(this)`）。
- `ES6`的继承机制完全不同，实质上是先创建父类的实例对象this（**所以必须先调用父类的super()方法**），然后再用子类的构造函数修改this。因为子类没有自己的this对象，而是继承了父类的this对象，然后对其进行加工，所以必须调用super。如果不调用super方法，子类得不到this对象。
- ES6继承中会有一条额外的继承链，在ES6继承中，子类的构造函数`__proto__`指向父类。ES5继承中子类构造函数没有这条继承链。
- ES5的继承通过原型，构造函数机制来实现。
- ES6使用class和extends关键字实现
## 😊 说一说this

this是执行上下文中的一个属性。在全局上下文中this，就是全局对象本身。在函数的上下文中，this是在进入上下文时确定的。（箭头函数是在定义时确定的）, 但是在上下文运行期间永久不变。**函数上下文的this取决于调用函数的方式。**调用函数的方式如何影响this？我们需要深入了解只存在在ECMAScript规范中的抽象类型Reference。

### Reference

Reference类型，简单的理解由两部分组成：

- base value，属性所在的对象或者就是 EnvironmentRecord
- referenced name，referenced name 就是属性的名称

举一个例子：

```js
var foo = 10;
// 对应的Reference是：
var fooReference = {
  base: EnvironmentRecord,
  propertyName: 'foo'
};
 
function bar() {}
// 对应的Reference是：
var barReference = {
  base: EnvironmentRecord,
  propertyName: 'bar'
};

var foo = {
  bar: function () {
    return this;
  }
};
// bar对应的Reference是：
var BarReference = {
  base: foo,
  propertyName: 'bar',
};
```

### GetValue

GetValue内部函数，可以从Reference类型获取对应的值。但是记住调用**GetValue**返回的不在是Reference对象

```js
GetValue(fooReference); // 10
GetValue(barReference); // function object "bar"
```

### this与Reference的关系

1.计算 MemberExpression 的结果赋值给 ref
2.判断 ref 是不是一个 Reference 类型
  - 2.1 如果 ref 是 Reference，并且 IsPropertyReference(ref) 是 true, 那么 this 的值为 GetBase(ref)
  - 2.2 如果 ref 是 Reference，并且 base value 值是 Environment Record, 那么this的值为 ImplicitThisValue(ref)
  - 2.3 如果 ref 不是 Reference，那么 this 的值为 undefined

- MemberExpression 可以简单理解为，`函数左边 ()`, 左边这部分内容
- IsPropertyReference，如果Reference的base是一个对象返回true
- GetBase, 返回Reference的base
- ImplicitThisValue，始终返回undefined

下面看几个例子：

```js
// 例子一
// fooReference = {
//   base: EnvironmentRecord,
//   propertyName: 'foo'
// };

// 1. ref是Reference类型
// 2. IsPropertyReference返回false
// 3. ImplicitThisValue返回undefined
// this等于undefined，非严格模式是全局对象
function foo() {
  return this;
}
 
foo(); // global


// 例子二
var foo = {
  bar: function () {
    return this;
  }
};
// fooBarReference = {
//   base: foo,
//   propertyName: 'bar'
// };
// 1. ref是Reference类型
// 2. IsPropertyReference返回true
// 3. GetBase(ref)foo
// this等于foo
foo.bar(); // foo


// 例子三
var test = foo.bar;
// testReference = {
//   base: EnvironmentRecord,
//   propertyName: 'test'
// };
// 1. ref是Reference类型
// 2. IsPropertyReference返回false
// 3. ImplicitThisValue返回undefined
// this等于undefined，非严格模式是全局对象
test(); // global


// 例子四
// barReference = {
//   base: AO,
//   propertyName: 'bar'
// };
// 1. ref是Reference类型
// 2. IsPropertyReference返回false
// 3. ImplicitThisValue返回undefined
// this等于undefined，非严格模式是全局对象
function foo() {
  function bar() {
    alert(this); 
  }
  bar(); 
}
```

### 非引用对象

调用括号()的左边是函数对象, 相应地，this值最终设为全局对象。

```js
(function () {
  alert(this); // global
})();
```

再看一个例子：

```js
var foo = {
  bar: function () {
    alert(this);
  }
};
 
foo.bar(); // foo，无需解释
(foo.bar)(); // foo，(foo.bar)没有调用GetValue，所以还是foo

// =，||, , 三种运算符调用了GetValue，所以返回的结果是函数对象
// 如果ref不是 Reference，那么this的值为 undefined
(foo.bar = foo.bar)(); // global，
(false || foo.bar)(); // global
(foo.bar, foo.bar)(); // global
```
### 构造函数中的this

构造函数中的this，指向新创建的实例对象。


## 😊 typeof

typeof 用于检测变量数据类型，由解释器内部实现。

不同的对象在底层都表示为二进制，在 Javascript 中二进制前（低）三位存储其类型信息。

- 000: 对象
- 010: 浮点数
- 100：字符串
- 110： 布尔
- 1： 整数
- null：所有机器码均为 0

所以`typeof null`会返回"objcect"

## 😊 instanceof

`A instanceof B`, 自下往上查找A的原型是否等于`B.prototype`, 直到向上查找到null

```js
function myInstanceOf(obj, target) {
  if (typeof obj !== 'object' || obj === null) {
    return false
  }
  const proto = Object.getPrototypeOf(obj)
  if (proto === null) {
    return false
  }
  if (proto === target.prototype) {
    return true
  } else {
    return myInstanceOf(proto, target)
  }
}
```
## 😊 new

1. 创建一个原型链为空的空对象`obj`
2. 使用apply或者call调用构造函数，并将this指向刚才创建的`obj`
3. 修改`obj`的`___proto___`, 指向构造函数的`prototype`
4. 如果调用构造函数有返回值则返回返回值，否则返回obj

```ts
const myNew = (constructor, ...args) => {
  const obj = Object.create({})
  const returnValue = constructor.call(obj, ...args)
  Object.setPrototypeOf(obj, constructor.prototype)
  return returnValue || obj
}
```
### 如何让函数不能被new?

```ts
class Shape {
  constructor() {
    if (new.target === Shape) {
      throw new Error("本类不能实例化");
    }
  }
}
```
## 😊 箭头函数

1. 箭头函数不能通过`new`实例化
2. 箭头函数没有自己的this, 箭头函数中的this，需要通过查找作用域链来确定this的值(换句话说在箭头函数定义时就确定了this)。也无法通过`call`, `apply`, `bind`改变this。
3. 没有arguments对象
4. 没有new.target
5. 没有原型 `var Foo = () => {}; console.log(Foo.prototype); // undefined`
6. 箭头函数默认会有返回值，更简洁

## 😊 立即执行函数(IIFE)


立即执行函数，主要用来创建一个新的函数执行上下文。函数函数上下文的变量，不会干扰到全局变量。直接在函数声明后使用`()`, 会产生`SyntaxError`。需要使用圆括号，圆括号中不能包含声明。当圆括号为了包裹函数碰上了function关键词，它便知道将它作为一个函数表达式去解析而不是函数声明。

```js
(function () { return 1 })()

// 也可以使用一元操作符
!function(){/* code */}();
~function(){/* code */}();
-function(){/* code */}();
+function(){/* code */}(); 
```

## 😊 map和object的区别，set和array的区别

1. map实现了迭代器，可以使用`for...of`遍历。object不可以
2. map具有有序性，object没有有序性
3. map可以展开为二维数组，object不可以
4. map可以使用对象作为key，object不可以

1. set是基于键的集合，不能通过直接通过索引访问。array可以直接通过索引访问
2. set不可以包含重复的元素。array可以。
3. 添加，删除元素的方法不同。

## 😊 bind, call, apply的区别

1. bind接收多个参数，第一个参数会修改this，之后的参数可以是函数的参数，并返回一个新的函数。返回的新函数，新函数不能再次修改this。函数的参数可以分多次传入，第一次修改this时传入，第二次调用时传入。
2. call接收多个参数，第一个参数会修改this，之后的参数可以是函数的参数。call会立即执行。
3. apply接收两个参数，第一个参数会修改this，第二个参数可以是函数的参数的数组。apply会立即执行。

```js
// 实现一个call方法
Function.prototype.mycall = function(thisArg, ...args) {
  if (thisArg === undefined || thisArg === null) {
    thisArg = window
  }
  if (typeof thisArg === 'string') {
    thisArg = new String(thisArg)
  }
  if (typeof thisArg === 'number') {
    thisArg = new Number(thisArg)
  }
  if (typeof thisArg === 'boolean') {
    thisArg = new Boolean(thisArg)
  }
  const key = Symbol()
  thisArg[key] = this
  const result = thisArg[key](...args)
  delete thisArg[key]
  return result
}

// 实现一个bind方法
Function.prototype.mybind = function (thisArg, ...initArgs) {
  if (thisArg === undefined || thisArg === null) {
    thisArg = window
  }
  if (typeof thisArg === 'string') {
    thisArg = new String(thisArg)
  }
  if (typeof thisArg === 'number') {
    thisArg = new Number(thisArg)
  }
  if (typeof thisArg === 'boolean') {
    thisArg = new Boolean(thisArg)
  }
  const that = this
  return function (...args) {
    const key = Symbol()
    thisArg[key] = that
    const result = thisArg[key](...initArgs, ...args)
    delete thisArg[key]
    return result
  }
}
```

## 😊 js浮点数精度问题

### 为什么会出现这个问题?

#### 十进制小数转二进制小数

10进制小数转2进制小数，使用"乘2取整，顺序排列"的方法，比如0.8125转二进制

```js
0.8125 * 2 = 1.6250 | 取1
0.6250 * 2 = 1.2500 | 取1
0.2500 * 2 = 0.5000 | 取0
0.5000 * 2 = 1.0000 | 取1
```

所以0.8125的二进制表示为0.1101。但是有些小数是循环的比如0.1的二进制

```js
0.1 * 2 = 0.2 | 取0
0.2 * 2 = 0.4 | 取0
0.4 * 2 = 0.8 | 取0
0.8 * 2 = 1.6 | 取1
0.6 * 2 = 1.2 | 取1
0.2 * 2 = 0.4 | 取0
...
```
不停的循环，所以0.1用二进制表示就是 0.00011001100110011……

#### 浮点数的存储

计算机在存储0.1的二进制时，存储结果为`0 01111111011 1001100110011001100110011001100110011001100110011010`, 也就是说0.1本身就存在精度丢失的问题。同理存储0.2也存在精度丢失的问题，二进制为`0 01111111100 1001100110011001100110011001100110011001100110011010`。

当使用0.1 + 0.2。存储时存在精度丢失，计算时也存在精度丢失所以导致了`0.1 + 0.2 !== 0.3`

### 如何解决这个问题

- 使用[bignumber.js](https://github.com/MikeMcl/bignumber.js/)库

```js
// 简单的处理方法
function precisionRound(number, precision) {
  const factor = Math.pow(10, precision)
  return Math.round(number * factor) / factor
}
```
## 😊 generater原理

### generater基础

> 😂忘了generater的语法, 所以简单复习一下generater的语法规则

1. generater函数调用后，会返回一个迭代器对象
2. 迭代器对象调用`next()`方法，内部指针就从函数头部或上一次停下来的地方开始执行，直到遇到下一个yield表达式（或return语句）为止。
3. `next()`方法会返回一个对象, `{value: yield表达式的值, done: 遍历是否结束}`
4. 如果是有`return`, 最后一个对象是`{value: return的值, done: 遍历是否结束}`。否则，`{value: undefined, done: 遍历是否结束}`。
5. `next()`方法可以添加一个参数。`next()`的参数会当作上一个`yield`的返回值。第一次就传入`next`参数没有用。
6. `for...of`可以自动遍历generater函数返回的迭代器对象。
7. 迭代器有一个`throw`方法, 使用迭代器`throw`的方法，可以在generater外抛出错误，在generater内部捕获。如果内部没有捕获会尝试外部捕获。
8. 迭代器有一个`return`方法可以提前结束遍历
9. `yield*`可以在generater函数内部执行另一个generater函数
10. 迭代器可以使用扩展运输符
11. generater函数不能使用`new`操作符

```js
const arr = [1, [[2, 3], 4], [5, 6]];

function* flat (arr) {
  for (let i = 0; i < arr.length; i += 1) {
    if (Array.isArray(arr[i])) {
      yield* flat(arr[i])
    } else {
      yield arr[i]
    }
  }
}

for (let value of flat(arr)) {
  console.log('value:', value)
}
```

### co模块

```js
function co(gen) {
  const ctx = this;

  function next(ret) {
    if (ret.done) {
      return resolve(ret.value)
    }
    // 确保返回值是Promise对象
    const value = toPromise.call(ctx, ret.value)
    if (value && isPromise(value)) {
      return value.then(onFulfilled, onRejected)
    }
    return onRejected(new TypeError(String(ret.value)));
  }

  return new Promise(function(resolve, reject) {
    // 返回迭代器
    gen = gen.call(ctx)
    function onFulfilled(res) {
      let ret
      try {
        ret = gen.next(res)
      } catch (e) {
        return reject(e)
      }
      next(ret)
    }
    onFulfilled()
  })
}
```

### js中generater的实现原理

C#中同样拥有yield关键词，C#中yield是一个语法糖。C#的编译器会将yield编译为`switch case`。编译器为我们生成了一个状态机。实现了yield关键字的特性。

[状态机.png](http://www.alloyteam.com/wp-content/uploads/2016/02/statemachine.png)

- Before 为迭代器初始状态
- Running 为调用 MoveNext 后进入这个状态。在这个状态，枚举数检测并设置下一项的位置。遇到 yield return、yield break - 或者迭代结束时，退出该状态
- Suspended 为状态机等待下次调用 MoveNext 的状态
- After 为迭代结束的状态


在js中与C#类似，yield表达式会在内部重写为`switch case`的状态机。

## 😊 async和awiat原理

async函数的实现原理，就是将generator函数和自动执行器，包装在一个函数里。

```js
async function fn(args) {
  // ...
}

// 等同于
// spawn是generator函数的自执行器
function fn(args) {
  return spawn(function* () {
    // ...
  });
}
```

具体实现

```js
function spawn(genF) {
  return new Promise(function(resolve, reject) {
    const gen = genF();

    function step(nextF) {
      let next;
      try {
        next = nextF();
      } catch(e) {
        return reject(e);
      }
      if(next.done) {
        return resolve(next.value);
      }
      Promise.resolve(next.value).then(function(v) {
        step(function() {
          // 返回yield的值
          return gen.next(v);
        });
      }, function(e) {
        step(function() {
          return gen.throw(e);
        });
      });
    }

    step(function() {
      return gen.next(undefined);
    });
  });
}
```
## promise的原理


## 😊 cookie，localStorage，sessionStorage区别

1. cookie如果不设置有效期是
2. sessionStorage仅在当前会话下有效

## 😊 Unicode和UTF-8

- Unicode是字符集
- UTF-8是字符编码

计算机是以二进制形式存储和表示数据，二进制是 0 和 1 的集合。例如：0100，1010。比如，要存储数字 13 计算机需要将数字转换为 1101。

但是，数字不是我们唯一需要存储处理的数据，我们还需要处理字符串，图片，视频。以字符串为例，那么计算机怎么存储字符串呢？例如，我们要存储字符串 "S" ，计算机首先会将 "S", 转换为数字'S'.charCodeAt() === 83, 那么计算机是如何知道83表示"S"的？
### 字符集

字符集是已经定义好的规则，每一个字符都有一个确切的数字表示。字符集有不同规则的定义，例如："Unicode", "ASCII"。浏览器中使用"Unicode"字符集。正是"Unicode"字符集，定义了83表示"S"。


那么接下来，计算机会直接将83转为二进制吗？并不是，我们还需要使用"字符编码"。

### 字符编码

字符集定义了使用特定的数字表示字符（汉字也是同样的）。而字符编码定义了，如何将数字转换为特定长度的二进制数据。常见的utf-8字符编码，规定了字符最多由4个字节进行编码（一个字节由8个，0或者1表示）。

`h e l l o =Unicode字符集==> 104 101 108 108 111 =utf-8字符编码==> 1101000 1100101 1101100 1101100 1101111` 

## 😊 WeakSet与Set的区别

- WeakSet, 只能存储对象类型的数据，不能存储普通的对象类型，不能遍历。WeakSet内部的对象是弱引用的，不会阻止垃圾回收机制回收WeakSet内部的对象。
- Set可以存储对象类型的数据，以及普通类型的数据。可以被遍历。
## 😊 WeakMap与Map的区别

- Map，是键值对的集合的。键可以任意的数据类型。Map可以被遍历。
- WeakMap，只接收对象作为键。WeakMap不会阻止垃圾回收机制回收。WeakMap不能遍历

## 😊 window.onload和DOMContentLoaded的区别

- dom, css, js, 图片，加载完成后触发
- dom加载完成后触发
## 😊 target和currentTarget区别

- event.target，返回触发事件的元素，可能不是绑定事件的元素
- event.currentTarget，返回绑定事件的元素
## 😊 let，const，var的区别

- `var`变量的作用域是它当前的执行上下文，`var`会使变量提升，这意味着变量可以在声明之前使用。`var`会直接在栈内存预分配内存空间，实际代码执行的时候再进行变量存储，如果传入的是应引用数据类型，则会在堆内存中开辟一个内存空间存储数据，栈内存存储的是数据的引用，指向堆内存地址。
- `let`变量的作用域是块级作用域，存在暂存性死区，`let`不会使变量提升。`let`则不会预分配内存空间，而且在栈内存分配变量时，做一个检查，如果已经有相同变量名存在就会报错。
- `const`变量的作用域是块级作用域，存在暂存性死区，`const`不会使变量提升。`const`与`let`的内容分配一致。

## 多个页面之间如何进行通信

## 移动端的布局

