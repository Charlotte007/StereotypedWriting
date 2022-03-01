## 😊 json如何很大怎么办？（得物面试题）

基于开源的[Oboe.js](http://oboejs.com/examples)库，通过的流的形式的处理JSON
## 😊 visibility: hidden 和 opacity: 0 的区别

- visibility: hidden，事件不能被触发，会占据空间
- opacity: 0，事件可以被触发，同样会占据空间

## 😊 一道执行题目

```js
const a = { b: () => { alert('Hello') } }
const b = a.b = 3
// error
b()
```

## 😊 hasOwnProperty 是做什么的?

hasOwnProperty() 方法会返回一个布尔值，判断对象`自身属性`中是否具有指定的属性 ( TIPS: 原型上的属性也会返回false )

## 😊 new Object 和 object.create 的区别

- object.create 可以创建一个指定__porto__的实例对象
- new Object 创建的实例对象，__proto___指向 Object.prototype

## 😊 await 后面如果是数字或者其他内容会怎么样？

```js

async function bar () {
  const a = await 1
}
```

async的内部会对1使用Promise.resolve进行包装

## 😊 一道爱奇艺的异步流程题（可能不完全一致）

```js
async function async1 () {
  console.log(1)

  // 【堵塞】，
  await console.log('await')
  console.log(2)
}

setTimeout(() => {
  console.log(3)
}, 0)

console.log(4)

async1()

new Promise((resolve) => {
  console.log(5)
  resolve()
  console.log(6)
}).then(() => { // 微任务队列补充
  console.log(7)
}).then(() => {
  console.log(8)
}).then(() => {
  console.log(9)
})

console.log(10)


// 第一次宏任务: 4 1 await 5 6 10 
// 第一次微任务 2 7 8 9
// 第二次宏任务 3
```

## 😊 私有属性的es5实现 private

```js
// 使用闭包实现私有属性和私有方法
// name是私有属性
// bar是私有方法
var Person = (function() {

  // 【显式绑定 this】
  function bar(baz) {
    return this.snaf = baz;
  }

  function Person(name) {
    this.getName = function() {
      return name;
    };
  }

  
  Person.prototype.foo = function (baz) {
    bar.call(this, baz);
  }
  return Person;

}());

const person = new Person('Hello')

```
### babel 转换使用的方法使用WeakMap

```js
// 实现私有属性
var Person = (function() {
    var private = new WeakMap();

    function Person(name) {
        var privateProperties = {
            name: name
        };
        private.set(this, privateProperties);
    }

    Person.prototype.getName = function() {
        return private.get(this).name;
    };

    return Person;
}());
```
## 😊 setInterval 和 setTimeout

setInterval, setTimeout 的 `等待时间`⌛️，不会受到主线程阻塞的影响。

- 同步任务会堵塞主线程，异步任务也是需要等待同步任务执行完成后，再开始执行；但是`不会影响等待时间`!!!

```js

// 堵塞主线程 [1s]
function sleep () {
  let flag = true
  const start = Date.now()
  while (flag) {
    console.log('sleep', Date.now() - start)
    if (Date.now() - start >= 1000) {
      flag = false
    }
  }
}

function main () {
  const start = Date.now()

  setInterval(() => {
    console.log(1, Date.now() - start)
  }, 900)

  sleep()

  setTimeout(() => {
    console.log(2, Date.now() - start)
  }, 0)

  console.log(3, Date.now() - start)
}

main()
// TEST： 测试环节
// 14 VM239:5 sleep 0
// 48 VM239:5 sleep 1
// 35 VM239:5 sleep 2
// 37 VM239:5 sleep 3
// ...
// 58 VM239:5 sleep 999

// [ sleep 堵塞结束 ]

// 同步任务： 3 1000
// 宏任务 interval 900ms： 1 1000 (TIPS: interval并没有需要继续延时 900ms, 而是再最后的同步任务执行完成后，立即输出的)
// 2 1001 （TIPS：宏任务队列排队，第二）
// 1 1801
```

```js
// 补充：很多人提过，setTimeout 默认会有 4ms 的延迟，实测并没有那么久；测试结果如下（2022年2月16日）：
function main () {
  const start = Date.now()

  setTimeout(() => {
    console.log(1, Date.now() - start)
  }, 0)
}

main()

// 不存在4ms的严格限制，各浏览器有差异
// chrome:  1, 1
// edge:    1, 2
// firefox：1, 0
```
## 😊 class语法的细节

```js
class P {
  constructor () {
    this.age = 20
    // S {age: 20}
    // 调用super就类似调用 P.call(this)
    console.log('this:', this)
  }
}

class S extends P {
  constructor () {
    super() // 调用super就类似调用 P.call(this)
    this.name = 1
  }
}

const s = new S()
```
### super

- super作为函数调用时，代表父类的构造函数。ES 要求，子类的构造函数必须执行一次super函数。
- super作为对象时，在普通方法中，指向父类的原型对象；在静态方法中，指向父类。

## 😊 for……of 循环 map是什么情况？

```js
// Map 作为构造函数的写法，[[key, val], ...]
const map = new Map([
  ['name', '张三'],
  ['title', 'Author']
]);

// 具有 Iterator 接口
for (let value of map) {
  // ["name", "张三"]
  // ["title", "Author"]
  console.log(value)
}

```

## 😊 不使用临时变量交换两个变量的值

```js
 // *//, +--, aba
a = a * b;
b = a / b;
a = a / b;

a = a + b;
b = a - b;
a = a - b;

[a, b] = [b, a];
```

## 😊 canvas和svg

1. canvas是位图，svg是矢量图
2. 矢量图不依赖分辨率，canvas放大会失真, 所以高清屏文件canvas导出会有模糊的情况，需要更加dpr适配大小
3. canvas是使用js程序绘图(动态生成)，svg是使用xml文档描述来绘图。
## 😊 JS的数据类型

Boolean、Null、Undefined、Number、`BigInt`、String、`Symbol` 和 Object （常见的 function， array 也是 object）

## 😊 Javascript是静态作用域(词法作用域)与动态作用域?

Javascript是静态作用域, 函数的作用域在`函数定义的时候`就确定了。动态作用域是在`函数调用的时候`才确立。

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

- `形参`, 形参会作为AO对象的属性，如果传参了属性会被赋值，否则是undefined。
- `函数声明`，函数名和函数的内容作为变量对象的key和value（
  - `函数声明的优先级会高于变量声明`，
  - 如果变量声明与函数声明有`相同属性`，函数声明会替代变量声明，函数声明在进入上下文的阶段就会被赋值, 所以在函数声明前调用它不会报错，在代码`执行阶段函数声明不会重复的赋值`）
- `变量声明`，变量的声明优先级在形参和函数声明之后。

##### 阶段2：执行上下文中的代码

接下来进入代码执行阶段，此时AO/VO对象都已经有了属性，并且属性值还是undefined，`代码执行阶段VO属性会被更新赋值`。

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


// 1, 2, 3, Hello , 4, 5, 6, 7, 8

setTimeout(() => {
  console.log(1)
  new Promise((resolve) => {
    resolve()
  }).then(() => {
    console.log(2)
    new Promise((resolve) => {
      resolve()
    }).then(() => {
      console.log(3)
    })
    new Promise((resolve) => {
      resolve()
    }).then(() => {
      console.log('Hello')
    })
  }).then(() => {
    console.log(4)
  })
}, 0)

setTimeout(() => {
  console.log(5)
  new Promise((resolve) => {
    resolve()
  }).then(() => {
    console.log(6)
    new Promise((resolve) => {
      resolve()
    }).then(() => {
      console.log(7)
    })
  }).then(() => {
    console.log(8)
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

> super关键字指代父类的实例，即父类的this对象。在子类构造函数中，调用super后，才可使用this关键字，否则报错。

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

async函数的实现原理，就是将 generator 函数和自动执行器，包装在一个函数里。

```js
async function fn(args) {
  // ...
}

// 等同于
// spawn是generator函数的自执行器
function fn(args) {
  // spawn会自动执行generator函数
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

1. cookie如果不设置有效期是临时存储。如果是设置有效期，cookie存储在本地。
2. sessionStorage仅在当前会话下有效。sessionStorage在同源的窗口中始终存在的数据。只要这个浏览器窗口没有关闭，即使刷新页面或者进入同源另一个页面，数据依然存在。
3. localStorage是永久保存的，除非手动删除。
4. cookie会自动发送给服务端，localStorage，sessionStorage不会。cookie可以设置Secure只在https时发送cookie。
5. cookie，与localStorage，sessionStorage内容大小限制不同。
6. sessionStorage，localStorage可以监听变化。
7. localstorage, cookie在所有同源窗口中共享的。sessionStorage在同一个浏览器窗口是共享的。
8. cookie可以通过服务端设置，cookie可以设置httpOnly，浏览器无法获取设置为httpOnly的cookie。

## 😊 cookie和session

session是一种服务器机制，是存储在服务器上的信息。存储方式多种多样，可以是服务器的内存中，或者是mongo数据库，redis内存数据库中（可以通过`express-session`指定数据库）。而session是基于cookie实现的(服务器会生成sessionID)通过set-cookie的方式写入到客户端的cookie中。每一次的请求都会携带服务器写入的sessionID发送给服务端，通过解析sessionID与服务器端保存的session，来判断用户是否登录。

鉴权步骤如下：

1. 客户端发起登录请求，服务器端创建session，并通过set-cookie将生成的sessionID写入的客户端的cookie中。
2. 在发起其他需要权限的接口的时候，客户端的请求体的Header部分会携带sessionID发送给服务端。然后根据这个sessionId去找服务器端保存的该客户端的session，然后判断该请求是否合法。

### 如何生成sessionId

一般通过用户信息生成sessionId

## 😊 session和jwt （对比）

1. 浏览器发起登录请求
2. 请求通过后，服务器会向浏览器返回token
3. 浏览器接收到token后需要讲token保存到本地（比如localStorage）
4. 浏览器在下一次请求的时候会携带token信息（需要ajax手动添加）
5. 服务器收到请求，去验证token（是否正确，是否过期）验证成功后会返回信息。

乍一看，token是类似sessionID的存在。其实token和sessionID还是有一定的不同的。sessionID是基于cookie实现的，而token不需要基于cookie。这就导致了sessionID只能用在浏览器上，对于原生的应用无法实现。原生的应用是不具备cookie的特性的。另外sessionID可以实现服务端注销会话，而token不能(当然你可以把用户登陆的token存入到redis中，但是不推荐token入库)
- 无状态
  - sessionid 需要借助查找对应用户信息，需要借助数据库存储查询
  - jwt不依赖认证，直接解析即可使用，不存入数据库
- 安全 （不一定更安全）
  - session 需要借助cookie，有 CSRF（跨站请求伪造）风险
  - jwt有一部分是base64加密，属于明文信息，依赖与私钥的保密性
- 跨平台
  - sessionID 依赖 cookie 的保存，以及浏览器器 携带 cookie信息
  - jwt 没有平台限制

## JWT（Json web token）
>JWT：Json Web Token，是基于Json的一个公开规范，这个规范允许我们使用JWT在用户和服务器之间传递安全可靠的信息，他的两大使用场景是：认证和数据交换, 使用起来就是，由`服务端根据规范生成一个令牌（token）`，并且发放给客户端。此时客户端请求服务端的时候就可以携带者令牌，以令牌来证明自己的身份信息。

### JWT规则详解
JWT实际上就是一个字符串，它由三部分组成：头部、载荷与签名 header payload signature
- header: {'typ': 'JWT','alg': 'HS256'} 声明类型是 JWT， 声明加密算法，通常使用 HMAC SHA256， 然后将头部进行base64加密
- playload： 有三部分组成JSON，最后进行base64加密，注意，因为base64加密关系，意味着为明文信息。避免存放敏感信息，但要保证每个用户唯一，建议加上时间戳
  - 标准中注册的声明
  - 公共的声明
  - 私有的声明
- signature
  - header （base64加密后字符串）
  - payload （base64加密后字符串）
  - secret ：拼接base64加密后的 header + payload， 再用header中声明的 加密方式进行加密
```js
// 以node服务为例
var encodedString = base64UrlEncode(header) + '.' + base64UrlEncode(payload);
var signature = HMACSHA256(encodedString, 'secret_key'); // TJVA95OrM7E2cBab30RMHrHDcEfxjoYZgeFONFh7HgQ
```
- 完整的token：base64(header) + base64(playload) + HS256(base64(header) + base64(playload), '私钥')


### JWT令牌的优点：

- jwt基于json，非常方便解析
- 可以再令牌中自定义丰富的内容，易扩展（payload可以扩展）
- 通过签名，让JWT防止被篡改，安全性高
- 资源服务使用JWT可不依赖认证服务即可完成授权
### JWT安全相关
- 保证服务端加密私钥的安全性；
- 避免在playload中放置用户敏感信息
- token设置过期时间
- 使用https

### 如何生成token

一般根据用户id，和一个存储在后端的随机字符串`secret`，利用`jsonwebtoken`生成token，token可以指定过期时间


## 😊 Unicode和UTF-8

- Unicode是字符集
- UTF-8是字符编码

计算机是以二进制形式存储和表示数据，二进制是 0 和 1 的集合。例如：0100，1010。比如，要存储数字 13 计算机需要将数字转换为 1101。

但是，数字不是我们唯一需要存储处理的数据，我们还需要处理字符串，图片，视频。以字符串为例，那么计算机怎么存储字符串呢？例如，我们要存储字符串 "S" ，计算机首先会将 "S", 转换为数字'S'.charCodeAt() === 83, 那么计算机是如何知道83表示"S"的？
### 字符集

字符集是已经定义好的规则，每一个字符都有一个确切的数字表示。字符集有不同规则的定义，例如："Unicode", "ASCII"。 `浏览器中使用"Unicode"字符集`。正是"Unicode"字符集，定义了83表示"S"。


那么接下来，计算机会直接将83转为二进制吗？并不是，我们还需要使用"字符编码"。

### 字符编码

字符集定义了使用特定的数字表示字符（汉字也是同样的）。而字符编码定义了，如何将数字转换为特定长度的二进制数据。常见的utf-8字符编码，规定了字符最多由4个字节进行编码（一个字节由8个，0或者1表示）。

`h e l l o =Unicode字符集==> 104 101 108 108 111 =utf-8字符编码==> 1101000 1100101 1101100 1101100 1101111` 

## 😊 WeakSet与Set的区别

- Set可以存储对象类型的数据，以及普通类型的数据。可以被遍历。
- WeakSet, `只能存储对象类型的数据`，不能存储普通的对象类型，不能遍历。WeakSet内部的对象是`弱引用`的，不会阻止垃圾回收机制回收WeakSet内部的对象。
## 😊 WeakMap与Map的区别

- Map，是键值对的集合的。键可以任意的数据类型。Map可以被遍历 for ...of, for in。
- WeakMap，`只接收对象作为键`。WeakMap不会阻止垃圾回收机制回收。WeakMap不能遍历

## 😊 window.onload 和 DOMContentLoaded的区别

- window.onload, dom, css, js, 图片，加载完成后触发
- DOMContentLoaded, dom加载完成后触发
## 😊 target和currentTarget区别

- event.target，返回触发事件的元素，可能不是绑定事件的元素
- event.currentTarget，返回绑定事件的元素
## 😊 let，const，var的区别

- `var`变量的作用域是它当前的执行上下文，`var`会使变量提升，这意味着变量可以在声明之前使用。`var`会直接在栈内存预分配内存空间，实际代码执行的时候再进行变量存储，如果传入的是应引用数据类型，则会在堆内存中开辟一个内存空间存储数据，栈内存存储的是数据的引用，指向堆内存地址。
- `let`变量的作用域是块级作用域，存在暂存性死区，`let`不会使变量提升。`let`则不会预分配内存空间，而且在栈内存分配变量时，做一个检查，如果已经有相同变量名存在就会报错。
- `const`变量的作用域是块级作用域，存在暂存性死区，`const`不会使变量提升。`const`与`let`的内容分配一致。

## 😊 多个页面之间如何进行通信

1. WebSocket
2. 监听`storage`事件，监听`localStorage`的变化
3. Worker线程，postMessage发送给Worker，Worker再推送给其他页面

## 😊 script标签的defer，async

现代的浏览器中无论是否添加defer，async属性js文件都是**并行下载**。如果没有添加defer，async属性，js是并行下载，按照顺序执行。但是在过去的IE，FireFox和Chrome的早期版本中，script标签是同步加载和执行的。

- 普通的script标签, 阻塞时间HTML解析时间等于 = 下载时间 + JS执行时间，
- async, 阻塞时间HTML的时间等于 = JS执行时间，async的script标签，那一个下载完成就执行哪一个JS，只适用于外链。
- defer, 不会阻塞HTML解析，defer的script标签，会等待HTML解析完成后按照顺序执行

![image.png](https://i.loli.net/2021/07/20/rHe6ljbYI4yvLCN.png)

## 😊 for……in 和 for……of 的区别

- for……in，遍历的是key，for……in遍历可枚举属性（包括它的`原型链上的可枚举属性`）
- for……of，遍历的是value，需要对象实现`iterator`（`Symbol.iterator`属性）接口才可以使用，否则会报错。for……of遍历可迭代对象定义要迭代的数据。

## 😊 怎么判断一个对象是不是可迭代的？

判断对象是否实现了`Symbol.iterator`属性的迭代器对象

```ts
let o = {};
typeof o[Symbol.iterator] === "function";
```

## 😊 DOM事件模型

DOM事件模型分为捕获和冒泡

1. 捕获阶段：事件从window对象自上而下向目标节点传播的阶段
2. 目标阶段：真正的目标节点正在处理事件的阶段
3. 冒泡阶段：事件从目标节点自下而上向window对象传播的阶段

![event.png](https://i.loli.net/2021/07/21/EYPRnHqsiZNdTml.png)

### 没有冒泡阶段的事件（蚂蚁一面有问过这题）

1. scroll事件
2. blur & focus 事件
3. onload 事件
4. error事件

## 😊 Object.defineProperty有哪几个参数各自都有什么作用？

```js
Object.defineProperties(`需要修改的对象obj`, {
  'obj的属性名': {
    configurable: boolean, // 该属性描述符是否可以被修改，是否可以被删除。默认false
    enumerable: boolean, // 是否被枚举。默认false
    value: any, // 属性值。默认 undefined
    writable: boolean, // 是否只读，默认false
    get () {},  // getter
    set () {}, // setter
  }
})
```

## 😊 Object.defineProperty和ES6的Proxy有什么区别？

### Reflect基础

1. Reflect 是一个内置的对象，它提供拦截 JavaScript 操作的方法。
2. Reflect并非一个构造函数，所以不能通过new运算符对其进行调用
3. Reflect的所有属性和方法都是静态的（就像Math对象）。
4. 将Object对象的一些明显属于语言内部的方法，放到了Reflect对象上部署。比如：Object.defineProperty 。
5. Object.defineProperty在设置不能设置的属性时，会抛出错误。Reflect.defineProperty会返回false 。
6. 让Object操作都变成函数行为，`name in obj` => `Reflect.has(obj, name)` 。
7. Reflect对象的方法与Proxy对象的方法一一对应。这就让Proxy对象可以方便地调用对应的 Reflect方法，完成默认行为 。

```js

Reflect.apply(target, thisArgument, argumentsList) // 对一个函数进行调用操作，同时可以传入一个数组作为调用参数。和 Function.prototype.apply() 功能类似。
Reflect.construct(target, argumentsList[, newTarget]) // 对构造函数进行 new 操作，相当于执行 new target(...args)。

Reflect.defineProperty(target, propertyKey, attributes) // 和 Object.defineProperty() 类似。如果设置成功就会返回 true
Reflect.deleteProperty(target, propertyKey) // 作为函数的delete操作符，相当于执行 delete target[name]。

Reflect.get(target, propertyKey[, receiver]) // 获取对象身上某个属性的值，类似于 target[name]。
Reflect.getOwnPropertyDescriptor(target, propertyKey) //类似于 Object.getOwnPropertyDescriptor()。如果对象中存在该属性，则返回对应的属性描述符,  否则返回 undefined.
Reflect.getPrototypeOf(target) // 类似于 Object.getPrototypeOf()。

Reflect.has(target, propertyKey) // 判断一个对象是否存在某个属性，和 in 运算符 的功能完全相同。


Reflect.set(target, propertyKey, value[, receiver]) // 将值分配给属性的函数。返回一个Boolean，如果更新成功，则返回true。
Reflect.setPrototypeOf(target, prototype) // 设置对象原型的函数. 返回一个 Boolean， 如果更新成功，则返回true。



Object.defineProperty(obj, prop, descriptor) // 返回值: 被传递给函数的对象 obj

```

### Proxy基础

> 好久没有使用忘了proxy的语法了😂

Proxy可以对对象的一些操作进行一层拦截。外界对对象的访问都需要通过这层代理。Proxy可以拦截13种操作：get（读取属性），set(设置属性)，has（拦截`in`）, deleteProperty(拦截delete)，ownKeys（拦截Object.getOwnPropertyNames(proxy)、Object.getOwnPropertySymbols(proxy)、Object.keys(proxy)）....等等

```js
const obj = new Proxy(target, {
  get: function (target, propKey, receiver) {
    // 使用默认行为
    return Reflect.get(target, propKey, receiver);
  },
  set: function (target, propKey, value, receiver) {
    // 
    return Reflect.set(target, propKey, value, receiver);
  }
});
```
### proxy和Object.defineProperty区别

- Object.defineProperty不能监听数组的`一些变化`，vue之所以可以修改数组响应，是Vue对数组进行了重写
  - 比如push()，pop()，shift()，unshift()，splice()，sort()，reverse() ，`defineProperty不能响应 修改原数组方法`，vue对数组原型上的方法进行了重构
  - vm.items[1] = 2; 可以通过 $set , 或者 splice(1, 0, 2)解决（vue已重构）
  - vm.items.length = 10
- 如果需要对对象做代理，必须对每一个属性进行设置，需要配合Object.keys
- 对于属性值也是对象的属性，避免逐层遍历，对每一个对象调用 Object.defineProperty。

- Proxy支持对数组操作的拦截
- Proxy支持对整个对象进行拦截，不在需要为每一个属性进行设置
- Proxy支持对嵌套对象的拦截


## 😊 如何处理浏览器中表单项的密码自动填充问题？

1. 浏览器遇到type="text"与type="password"的\<input\/>标签紧邻时触发自动填充行为，则将两个\<input\/>隔开。中间添加隐藏的\<input\/>。
2. password输入框设置readonly属性，焦点在该表单输入的时候再将可读属性移除。


## import() 的原理
