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

`A instanceof B`, 自下往上查找A的原型是否等于`B.prototype`。

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
## new

### 如何让函数不能被new?

## 箭头函数

## 变量提升

## 立即执行函数
## 移动端的布局

## generater原理

## async和awiat原理

## map和object的区别

## WeakSet与Set的区别

- WeakSet, 只能存储对象类型的数据，不能存储普通的对象类型，不能遍历。WeakSet内部的对象是弱引用的，不会阻止垃圾回收机制回收WeakSet内部的对象。
- Set可以存储对象类型的数据，以及普通类型的数据。可以被遍历。
## WeakMap与Map的区别

- Map，是键值对的集合的。键可以任意的数据类型。Map可以被遍历。
- WeakMap，只接收对象作为键。WeakMap不会阻止垃圾回收机制回收。WeakMap不能遍历

## 😊 window.onload和DOMContentLoaded的区别

## 😊 target和currentTarget区别

- event.target，返回触发事件的元素，可能不是绑定事件的元素
- event.currentTarget，返回绑定事件的元素
## 😊 let，const，var的区别

- `var`变量的作用域是它当前的执行上下文，`var`会使变量提升，这意味着变量可以在声明之前使用。`var`会直接在栈内存预分配内存空间，实际代码执行的时候再进行变量存储，如果传入的是应引用数据类型，则会在堆内存中开辟一个内存空间存储数据，栈内存存储的是数据的引用，指向堆内存地址。
- `let`变量的作用域是块级作用域，存在暂存性死区，`let`不会使变量提升。`let`则不会预分配内存空间，而且在栈内存分配变量时，做一个检查，如果已经有相同变量名存在就会报错。
- `const`变量的作用域是块级作用域，存在暂存性死区，`const`不会使变量提升。`const`与`let`的内容分配一致。

## 😊 cookie，localStorage，sessionStorage区别


## 😊 多个页面之间如何进行通信

## 😊 bind, call, apply的区别
