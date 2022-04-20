## Vue 如何监测数组？

### 为什么vue2.x 有些数组的数据变更不能被 Vue 监测到（两条）？
- vm.items[1] = 10; 直接修改，无法触发响应；
- vm.items.length = 10; 直接修改数组长度，无法触发视图响应
- 使用 push()，pop()，shift()，unshift()，splice()，sort()，reverse()， 可以触发视图响应；（因为vue中对于数组原型中的上述方法进行重写，上述方法的特点就是 可修改原数组数据）


### vue2.x现有监控数组的两条限制：索引赋值，修改长度，是因为 defineProperty 方法不支持吗？
- 实际上， defineProperty 是`可以监听索引赋值!!!`, (区别)
- 修改数组length，的确不能触发 setter， （兼容问题）
  - Object.defineProperty(arr, 'length', {}) 的兼容问题，可能会抛出异常
  - 可以参考 [mdn Object.defineProperty() 兼容问题](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/defineProperty#重定义数组_array_对象的_length_属性)
- 使用 修改原数组的数组方法，如 pop，push 等，也是无法触发setter
- 【综上所述】：对数组`已有元素`进行监听，而没有对`数组本身`的变化进行监听（如length，push等）

```js
const arr = [1,2,3]
arr.forEach((val ,index) =>{
    Object.defineProperty(arr, index, {
        get(){
            return val
        },
        set(newVal){
            console.log('setter [old, new]:', [val, newVal])
            val = newVal
        }
    })
})

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
### Object.defineProperty 明明可以监控 数据值 的变化，而vue2.x （vm.items[1]=4）却没有实现呢？
- 主要是基于性能的考虑；循环给`每个元素`添加监听，实际上性能消化很大； 
  - 数组是有序数据，如unshift，shift，reverse，splice，sort，一个方法，会导致数组数据重排，会触发多次，一个修改，多次触发，消耗较大

### 对于vue2.x实现的监测对象变化有什么需要注意的
- 数据需要响应，需要在 dataInit绑定 观察者 observer，定义属性，即 vue组件预设data属性，当然可以 通过 $set, $delete 动态修改
- Object.defineProperty 是对 对象的属性做操作，而不是 obj 对象本身，obj 重新赋值，不会触发 setter；（针对与内部setter）
- this.obj = Object.assign({}, this.obj, {a:1,b:2}); 因为重新绑定了`新对象`, 会 对新对象属性重新遍历添加监听

### vue3.x 使用proxy实现数据响应式
> Proxy 对象用于创建一个对象的代理，从而实现基本操作的拦截和自定义（如属性查找、赋值、枚举、函数调用等）。

- Proxy支持对数组操作的拦截
- Proxy支持对整个对象进行拦截，不在需要为每一个属性进行设置，无需遍历添加监听
- Proxy支持对嵌套对象的拦截，vue2.x 需要递归添加