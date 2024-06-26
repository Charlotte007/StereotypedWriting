## 😊 自己写过一些声明文件吗？

下面是一个组件的例子

```ts
// d.ts
import * as React from "react";
export interface AlertProps {
  type?: "success" | "warning" | "info" | "error";
  delete?: boolean;
}
declare const Alert: React.FC<AlertProps>;
export default Alert;
```

## 😊 declare 关键字是做什么的？

declare 可以声明一个类型，一个变量，一个模块

```ts
declare const Alert: React.FC<AlertProps>;

declare type Asd {
    name: string;
}

declare module '*.css';
declare module '*.less';
```

## 😊 ts 基础知识复习

https://juejin.cn/post/6844903981227966471#heading-79

## 😊 ts 中的访问修饰符

- public，任何地方
- private，只能在类的内部访问
- protected，能在类的内部访问和子类中访问
- readonly，属性设置为只读

## 😊 const 和 readonly 的区别

1. const 用于变量，readonly 用于属性
2. const 在运行时检查，readonly 在编译时检查
3. 使用 const 变量保存的数组，可以使用 push，pop 等方法。但是如果使用`ReadonlyArray<number>`声明的数组不能使用 push，pop 等方法。

## 😊 枚举和常量枚举（const 枚举）的区别

1. 枚举会被编译时会编译成一个对象，可以被当作对象使用
2. const 枚举会在 ts 编译期间被删除，避免额外的性能开销

```ts
// 普通枚举
enum Witcher {
  Ciri = "Queen",
  Geralt = "Geralt of Rivia",
}
function getGeraltMessage(arg: { [key: string]: string }): string {
  return arg.Geralt;
}
getGeraltMessage(Witcher); // Geralt of Rivia
```

```ts
// const枚举
const enum Witcher {
  Ciri = "Queen",
  Geralt = "Geralt of Rivia",
}
const witchers: Witcher[] = [Witcher.Ciri, Witcher.Geralt];
// 编译后
// const witchers = ['Queen', 'Geralt of Rivia'
```

## 😊 ts 中 interface 可以给 Function/Array/Class 做声明吗？

```ts
// 函数类型
interface SearchFunc {
  (source: string, subString: string): boolean;
}
let mySearch: SearchFunc;
mySearch = function (source: string, subString: string) {
  let result = source.search(subString);
  return result > -1;
};
```

```ts
// Array
interface StringArray {
  [index: number]: string;
}

let myArray: StringArray;
myArray = ["Bob", "Fred"];
```

```ts
// Class, constructor存在于类的静态部分，所以不会检查
interface ClockInterface {
  currentTime: Date;
  setTime(d: Date);
}

class Clock implements ClockInterface {
  currentTime: Date;
  setTime(d: Date) {
    this.currentTime = d;
  }
  constructor(h: number, m: number) {}
}
```

## ts 中的 this 和 js 中的 this 有什么差异？

不了解

## 😊 ts 中如何枚举联合类型的 key?

```ts
type Name = { name: string };
type Age = { age: number };
type Union = Name | Age;

type UnionKey<P> = P extends infer P ? keyof P : never;

type T = UnionKey<Union>;
```

## 😊 ts 中 ?.、??、!.、\_、\*\* 等符号的含义？

- ?. 可选链
- ?? ?? 类似与短路或，??避免了一些意外情况 0，NaN 以及"",false 被视为 false 值。只有 undefind,null 被视为 false 值。
- !. 在变量名后添加!，可以断言排除 undefined 和 null 类型
- \_ , 声明该函数将被传递一个参数，但您并不关心它
- \*\* 求幂
- !:，待会分配这个变量，ts 不要担心

```ts
// ??
let x = foo ?? bar();
// 等价于
let x = foo !== null && foo !== undefined ? foo : bar();

// !.
let a: string | null | undefined;
a.length; // error
a!.length; // ok
```

## 😊 什么是抗变、双变、协变和逆变？

- Covariant 协变，TS 对象兼容性是协变，父类 <= 子类，是可以的。子类 <= 父类，错误。
- Contravariant 逆变，禁用`strictFunctionTypes`编译，函数参数类型是逆变的，父类 <= 子类，是错误。子类 <= 父类，是可以的。
- Bivariant 双向协变，函数参数的类型默认是双向协变的。父类 <= 子类，是可以的。子类 <= 父类，是可以的。

## 😊 ts 中同名的 interface 或者同名的 interface 和 class 可以合并吗？

1. interface 会合并
2. class 不可以合并

## 😊 如何使 ts 项目引入并识别编译为 js 的 npm 库包？

1. `npm install @types/xxxx`
2. 自己添加描述文件

## 😊 ts 如何自动生成库包的声明文件？

可以配置`tsconfig.json`文件中的`declaration`和`outDir`

1. declaration: true, 将会自动生成声明文件
2. outDir: '', 指定目录

## 😊 什么是泛型

泛型用来来创建可重用的组件，一个组件可以支持多种类型的数据。这样用户就可以以自己的数据类型来使用组件。**简单的说，“泛型就是把类型当成参数”。**

### 定义泛型的步骤，(练习题 type-challenges)[https://github.com/type-challenges/type-challenges/blob/main/README.zh-CN.md]

- 提取可变的`类型参数` （提取变量）
- `类型参数` 添加继承 （泛型约束，输入类型参数的类型，key, value 的类型）
  - 如何定义 a | b | c，输入类型
- 使用 工具，推导出泛型
  - 基本工具
    - extends
    - typeof：在类型上下文中获取`变量或者属性`的类型；typeof 实例，返回该实例的所对应添加的 TS 类型；
    - keyof: 获取类型的所有键值，类似 Object.keys
    - in: 遍历，类似 for in
    - infer: 声明类型变量
    - ?
    - -
    - -
    - Readonly
  - 泛型工具
    - Pick<T, K>
    - Record<T, K> 将 T 中所有类型，都转化为 T
    - ReturnType<T> 获取函数类型的返回值类型
    - Exclude<T, U> 从 T 中删除 U，返回剩余的类型，如 [1,2,3,4]中删除[3,4],得到 [1,2]
    - Extract<T, U> 从 T 中提取 U，返回存在的类型，如 [1,2,3,4]中提取[3,4],得到 [3,4]
    -

```ts
// ## DEMO

// 元组长度
type Length<T extends readonly any[]> = T["length"];

// 排除，找合集
type Exclude<T, U> = T extends U ? never : T; // 需要理解extends

// 判断，包含关系；TIPS：排除 undefined情况
type Includes<T extends readonly any[], U> = {
  [P in T[number]]: true;
}[U] extends true
  ? true
  : false;

// 选择 需要的属性；
type Pick<T, K extends keyof T> = {
  [P in K]: T[P];
};

// 获取Promise类型，返回值类型；TIPS：递归，避免pormise执行后返回promise; U表示`输入`的泛型的参数，如例，为string
type MyAwaited<T extends Promise<unknown>> = T extends Promise<infer U>
  ? U extends Promise<unknown>
    ? MyAwaited<U>
    : U
  : never;
// USE: type ExampleType = Promise<string>; type Result = MyAwaited<ExampleType> // string

// 元组中添加新类型，TIPS：避免类型重复，减少检测次数，区别于js中push，仅仅用于扩展数组
type Push<T extends any[], U> = [U] extends [T[number]] ? T : [...T, U];

// 获取函数返回类型，TIPS： => 返回值类型；infter 定义返回值
type ReturnType<T> = T extends (...args: any) => infer R ? R : never;
// USE
const fn = (v: boolean) => {
  if (v) return 1;
  else return 2;
};

// typof fn 结果为 (v: boolean) => "1 | 2"
type a = ReturnType<typeof fn>; // 应推导出 "1 | 2"
```

### 定义泛型的经验

- 获取数组 index ,value
  - index:
  - value: T[number]
- 判断类型
  - T extends XX? A : B

## 😊 -?，-readonly 是什么含义

用于删除修饰符

```ts
type A = {
  a: string;
  b: number;
};

type B = {
  [K in keyof A]?: A[K];
};

type C = {
  [K in keyof B]-?: B[K];
};

type D = {
  readonly [K in keyof A]: A[K];
};

type E = {
  -readonly [K in keyof A]: A[K];
};
```

## 😊 TS 是基于结构类型兼容

typescript 的类型兼容是基于结构的，不是基于名义的。下面的代码在 ts 中是完全可以的，但在 java 等基于名义的语言则会抛错。

```ts
interface Named {
  name: string;
}
class Person {
  name: string;
}
let p: Named;
// ok
p = new Person();
```

## 😊 const 断言

const 断言，typescript 会为变量添加一个自身的字面量类型

1. 对象字面量 的属性，获得 readonly 的属性，成为只读属性
2. 数组字面量 成为 readonly tuple 只读元组
3. 字面量类型 不能被扩展（比如从 hello 类型到 string 类型）

```ts
// type '"hello"'
let x = "hello" as const;
// type 'readonly [10, 20]'
let y = [10, 20] as const;
// type '{ readonly text: "hello" }'
let z = { text: "hello" } as const;
```

## 😊 type 和 interface 的区别

1. 类型别名可以为任何类型引入名称。例如基本类型，联合类型等
2. 类型别名不支持继承
3. 类型别名不会创建一个真正的名字
4. 类型别名无法被实现(implements)，而接口可以被派生类实现
5. 类型别名重名时编译器会抛出错误，接口重名时会产生合并

## extends 理解

- 1、继承（复制和扩展类型成员）
  - 接口继承
    - 单类型继承，多类型继承； T3 extends T1,T2
  - 类继承
    - 类与类之间的继承，
    - （类与接口其实也可以相互继承）
      - 接口继承类时，可以把类看作一个接口，但类中的静态方法（static fn）是不会继承过来的
      - 类继承接口使用的关键字变成了 implements
- 2、三元表达式条件判断 （常规使用与泛型使用的区别）
  - 常规的三元表达式 A extends B ? true : false；（指类型 A 可以分配给类型 B）
    - A B 为同一种类型， A extends B // true
    - A 是 B 的子类型（子类型更丰富） A extends B // true
    - B 类型兼容类型 A （如 class 属性，方法有重合，function 入参有重合，B 类型更丰富） A extends B // true
  - `带有泛型的三元表达式条件判断` (泛型时，会将`联合类型分拆出来`，分别代入对比，最后把结果`再联合`)
    - `分配条件类型`：第一，参数是`泛型类型`，第二，代入参数的是`联合类型`
      - 泛型：type P<T> = T extends 'x' ? string : number; type A3 = P<'x' | 'y'> // A3 的类型是 string | number
        - P<'x' | 'y'> 相当于 `P<'x'> | P<'y'>`
      - 特殊的 never，空的联合类型； type P<T> = T extends 'x' ? string : number; type A2 = P<never> // never
        - P<never> 空的联合类型，也就是说，没有联合项的联合类型；没有联合项可以分配，所以不会执行，没有类型返回就是 never
      - `防止分配`：只需要给泛型加一个`[]`, 就可以放泛型分配变为普通三元表达式，联合类型时不再分配
        - type P<T> = [T] extends ['x'] ? string : number; type A2 = P<never> // string
    - TIPS： 泛型的返回值的思考，实现内置的 Exclude <T, U>类型，
      - type MyExclude<T, U> = T extends U ? never : T; type Result = MyExclude<'a' | 'b' | 'c', 'a'> // 'b' | 'c'
      - 等价于：(MyExclude<'a', 'a'> => never) | (MyExclude<'b', 'a'> => 'b') | (MyExclude<'c', 'a'> => 'c') ===> 'b' | 'c'
  - 3、 泛型约束 （类型变量，规则与 `泛型三元表达式相同`）
    - 对入参变量进行约束，确保传入的泛型参数遵循特定的规则或结构。它提高了泛型的灵活性和安全性，允许你在使用泛型时对传入的类型有更多的控制

## 😊 implements 与 extends 的区别

- extends, 子类会继承父类的所有属性和方法。
- implements，使用 implements 关键字的类将需要实现需要实现的类的所有属性和方法。

## 😊 枚举和 object 的区别

1. 枚举可以通过枚举的名称，获取枚举的值。也可以通过枚举的值获取枚举的名称。
2. object 只能通过 key 获取 value
3. 数字枚举在不指定初始值的情况下，枚举值会从 0 开始递增。
4. 虽然在运行时，枚举是一个真实存在的对象。但是使用 keyof 时的行为却和普通对象不一致。必须使用 keyof typeof 才可以获取枚举所有属性名。

## 😊 never, void 的区别

- never，never 表示永远不存在的类型。比如一个函数总是抛出错误，而没有返回值。或者一个函数内部有死循环，永远不会有返回值。函数的返回值就是 never 类型。
- void, 没有显示的返回值的函数返回值为 void 类型。如果一个变量为 void 类型，只能赋予 undefined 或者 null。

## unknown, any 的区别

unknown 类型和 any 类型类似。与 any 类型不同的是。unknown 类型可以接受任意类型赋值，但是 unknown 类型赋值给其他类型前，必须被断言

## 😊 如何在 window 扩展类型

```ts
declare global {
  interface Window {
    myCustomFn: () => void;
  }
}
```

## 复杂的类型推导题目

### 🤔 implement UnionToIntersection<T>

```ts
type A = UnionToIntersection<{ a: string } | { b: string } | { c: string }>;
// {a: string} & {b: string} & {c: string}

// 实现UnionToIntersection<T>
type UnionToIntersection<U> = (U extends any ? (k: U) => void : never) extends (
  k: infer I
) => void
  ? I
  : never;
// https://stackoverflow.com/questions/50374908/transform-union-type-to-intersection-type
// https://jkchao.github.io/typescript-book-chinese/tips/infer.html#%E4%B8%80%E4%BA%9B%E7%94%A8%E4%BE%8B
```

### 😊 implement ToNumber<T>

```ts
type A = ToNumber<"1">; // 1
type B = ToNumber<"40">; // 40
type C = ToNumber<"0">; // 0

// 实现ToNumber
type ToNumber<
  T extends string,
  R extends any[] = []
> = T extends `${R["length"]}` ? R["length"] : ToNumber<T, [1, ...R]>;
```

### 😊 implement Add<A, B>

```ts
type A = Add<1, 2>; // 3
type B = Add<0, 0>; // 0

// 实现ADD
type NumberToArray<T, R extends any[]> = T extends R["length"]
  ? R
  : NumberToArray<T, [1, ...R]>;
type Add<T, R> = [...NumberToArray<T, []>, ...NumberToArray<R, []>]["length"];
```

### 😊 implement SmallerThan<A, B>

```ts
type A = SmallerThan<0, 1>; // true
type B = SmallerThan<1, 0>; // false
type C = SmallerThan<10, 9>; // false

// 实现SmallerThan
type SmallerThan<
  N extends number,
  M extends number,
  L extends any[] = [],
  R extends any[] = []
> = N extends L["length"]
  ? M extends R["length"]
    ? false
    : true
  : M extends R["length"]
  ? false
  : SmallerThan<N, M, [1, ...L], [1, ...R]>;
```

### 😊 implement LargerThan<A, B>

```ts
type A = LargerThan<0, 1>; // false
type B = LargerThan<1, 0>; // true
type C = LargerThan<10, 9>; // true

// 实现LargerThan
type LargerThan<
  N extends number,
  M extends number,
  L extends any[] = [],
  R extends any[] = []
> = N extends L["length"]
  ? false
  : M extends R["length"]
  ? true
  : LargerThan<N, M, [1, ...L], [1, ...R]>;
```

### 😊 implement IsAny<T>

```ts
type A = IsAny<string>; // false
type B = IsAny<any>; // true
type C = IsAny<unknown>; // false
type D = IsAny<never>; // false

// 实现IsAny
type IsAny<T> = true extends (T extends never ? true : false)
  ? false extends (T extends never ? true : false)
    ? true
    : false
  : false;

// 更简单的实现
type IsAny<T> = 0 extends T & 1 ? true : false;
```

### 😊 implement Filter<T, A>

```ts
type A = Filter<[1, "BFE", 2, true, "dev"], number>; // [1, 2]
type B = Filter<[1, "BFE", 2, true, "dev"], string>; // ['BFE', 'dev']
type C = Filter<[1, "BFE", 2, any, "dev"], string>; // ['BFE', any, 'dev']

// 实现Filter
type Filter<T extends any[], A, N extends any[] = []> = T extends [
  infer P,
  ...infer Q
]
  ? 0 extends P & 1
    ? Filter<Q, A, [...N, P]>
    : P extends A
    ? Filter<Q, A, [...N, P]>
    : Filter<Q, A, N>
  : N;
```

### 😊 implement TupleToString<T>

```ts
type A = TupleToString<["a"]>; // 'a'
type B = TupleToString<["B", "F", "E"]>; // 'BFE'
type C = TupleToString<[]>; // ''

// 实现TupleToString
type TupleToString<
  T extends any[],
  S extends string = "",
  A extends any[] = []
> = A["length"] extends T["length"]
  ? S
  : TupleToString<T, `${S}${T[A["length"]]}`, [1, ...A]>;
```

### 😊 implement RepeatString<T, C>

```ts
type A = RepeatString<"a", 3>; // 'aaa'
type B = RepeatString<"a", 0>; // ''

// 实现RepeatString
type RepeatString<
  T extends string,
  C extends number,
  S extends string = "",
  A extends any[] = []
> = A["length"] extends C ? S : RepeatString<T, C, `${T}${S}`, [1, ...A]>;
```

### 😊 implement Push<T, I>

```ts
type A = Push<[1, 2, 3], 4>; // [1,2,3,4]
type B = Push<[1], 2>; // [1, 2]
type C = Push<[], string>; // [string]

// 实现Push
type Push<T extends any[], I> = T extends [...infer P] ? [...P, I] : [I];
```

### 😊 implement Flat<T>

```ts
type A = Flat<[1, 2, 3]>; // [1,2,3]
type B = Flat<[1, [2, 3], [4, [5, [6]]]]>; // [1,2,3,4,5,6]
type C = Flat<[]>; // []

// 实现Flat
type Flat<T extends any[]> = T extends [infer P, ...infer Q]
  ? P extends any[]
    ? [...Flat<P>, ...Flat<Q>]
    : [P, ...Flat<Q>]
  : [];
```

### 😊 implement Shift<T>

```ts
type A = Shift<[1, 2, 3]>; // [2,3]
type B = Shift<[1]>; // []
type C = Shift<[]>; // []

// 实现Shift
type Shift<T extends any[]> = T extends [infer P, ...infer Q] ? [...Q] : [];
```

### 😊 implement Repeat<T, C>

```ts
type A = Repeat<number, 3>; // [number, number, number]
type B = Repeat<string, 2>; // [string, string]
type C = Repeat<1, 1>; // [1, 1]
type D = Repeat<0, 0>; // []

// 实现Repeat
type Repeat<T, C, R extends any[] = []> = R["length"] extends C
  ? R
  : Repeat<T, C, [...R, T]>;
```

### 😊 implement ReverseTuple<T>

```ts
type A = ReverseTuple<[string, number, boolean]>; // [boolean, number, string]
type B = ReverseTuple<[1, 2, 3]>; // [3,2,1]
type C = ReverseTuple<[]>; // []

// 实现ReverseTuple
type ReverseTuple<T extends any[], A extends any[] = []> = T extends [
  ...infer Q,
  infer P
]
  ? A["length"] extends T["length"]
    ? A
    : ReverseTuple<Q, [...A, P]>
  : A;
```

### 😊 implement UnwrapPromise<T>

```ts
type A = UnwrapPromise<Promise<string>>; // string
type B = UnwrapPromise<Promise<null>>; // null
type C = UnwrapPromise<null>; // Error

// 实现UnwrapPromise
type UnwrapPromise<T> = T extends Promise<infer P> ? P : Error;
```

### 😊 implement LengthOfString<T>

```ts
type A = LengthOfString<"BFE.dev">; // 7
type B = LengthOfString<"">; // 0

// 实现LengthOfString
type LengthOfString<
  T extends string,
  A extends any[] = []
> = T extends `${infer P}${infer Q}`
  ? LengthOfString<Q, [1, ...A]>
  : A["length"];
```

### 😊 implement StringToTuple<T>

```ts
type A = StringToTuple<"BFE.dev">; // ['B', 'F', 'E', '.', 'd', 'e','v']
type B = StringToTuple<"">; // []

// 实现
type StringToTuple<
  T extends string,
  A extends any[] = []
> = T extends `${infer K}${infer P}` ? StringToTuple<P, [...A, K]> : A;
```

### 😊 implement LengthOfTuple<T>

```ts
type A = LengthOfTuple<["B", "F", "E"]>; // 3
type B = LengthOfTuple<[]>; // 0

// 实现
type LengthOfTuple<
  T extends any[],
  R extends any[] = []
> = R["length"] extends T["length"] ? R["length"] : LengthOfTuple<T, [...R, 1]>;
```

### 😊 implement LastItem<T>

```ts
type A = LastItem<[string, number, boolean]>; // boolean
type B = LastItem<["B", "F", "E"]>; // 'E'
type C = LastItem<[]>; // never

// 实现LastItem
type LastItem<T> = T extends [...infer P, infer Q] ? Q : never;
```

### 😊 implement FirstItem<T>

```ts
type A = FirstItem<[string, number, boolean]>; // string
type B = FirstItem<["B", "F", "E"]>; // 'B'

// 实现FirstItem
type FirstItem<T> = T extends [infer P, ...infer Q] ? P : never;
```

### 😊 implement FirstChar<T>

```ts
type A = FirstChar<"BFE">; // 'B'
type B = FirstChar<"dev">; // 'd'
type C = FirstChar<"">; // never

// 实现FirstChar
type FirstChar<T> = T extends `${infer P}${infer Q}` ? P : never;
```

### 😊 implement Pick<T, K>

```ts
type Foo = {
  a: string;
  b: number;
  c: boolean;
};

type A = MyPick<Foo, "a" | "b">; // {a: string, b: number}
type B = MyPick<Foo, "c">; // {c: boolean}
type C = MyPick<Foo, "d">; // Error

// 实现MyPick<T, K>
type MyPick<T, K extends keyof T> = {
  [Key in K]: T[Key];
};
```

### 😊 implement Readonly<T>

```ts
type Foo = {
  a: string;
};

const a: Foo = {
  a: "BFE.dev",
};
a.a = "bigfrontend.dev";
// OK

const b: MyReadonly<Foo> = {
  a: "BFE.dev",
};
b.a = "bigfrontend.dev";
// Error

// 实现MyReadonly
type MyReadonly<T> = {
  readonly [K in keyof T]: T[K];
};
```

### 😊 implement Record<K, V>

```ts
type Key = "a" | "b" | "c";

const a: Record<Key, string> = {
  a: "BFE.dev",
  b: "BFE.dev",
  c: "BFE.dev",
};
a.a = "bigfrontend.dev"; // OK
a.b = 123; // Error
a.d = "BFE.dev"; // Error

type Foo = MyRecord<{ a: string }, string>; // Error

// 实现MyRecord
type MyRecord<K extends number | string | symbol, V> = {
  [Key in K]: V;
};
```

### 🤔️ implement Exclude

```ts
type Foo = "a" | "b" | "c";

type A = MyExclude<Foo, "a">; // 'b' | 'c'
type B = MyExclude<Foo, "c">; // 'a' | 'b
type C = MyExclude<Foo, "c" | "d">; // 'a' | 'b'
type D = MyExclude<Foo, "a" | "b" | "c">; // never

// 实现 MyExclude<T, K>
type MyExclude<T, K> = T extends K ? never : T;
```

### 🤔️ implement Extract<T, U>

```ts
type Foo = "a" | "b" | "c";

type A = MyExtract<Foo, "a">; // 'a'
type B = MyExtract<Foo, "a" | "b">; // 'a' | 'b'
type C = MyExtract<Foo, "b" | "c" | "d" | "e">; // 'b' | 'c'
type D = MyExtract<Foo, never>; // never

// 实现MyExtract<T, U>
type MyExtract<T, U> = T extends U ? T : never;
```

### 😊 implement Omit<T, K>

```ts
type Foo = {
  a: string;
  b: number;
  c: boolean;
};

type A = MyOmit<Foo, "a" | "b">; // {c: boolean}
type B = MyOmit<Foo, "c">; // {a: string, b: number}
type C = MyOmit<Foo, "c" | "d">; // {a: string, b: number}

// 实现MyOmit
type MyOmit<T, K extends number | string | symbol> = {
  [Key in Exclude<keyof T, K>]: T[Key];
};

type MyOmit<T, K extends number | string | symbol> = Pick<
  T,
  Exclude<keyof T, K>
>;
```

### 😊 implement NonNullable<T>

```ts
type Foo = "a" | "b" | null | undefined;

type A = MyNonNullable<Foo>; // 'a' | 'b'

// 实现NonNullable
type MyNonNullable<T> = T extends null | undefined ? never : T;
```

### 😊 implement Parameters<T>

```ts
type Foo = (a: string, b: number, c: boolean) => string;

type A = MyParameters<Foo>; // [a:string, b: number, c:boolean]
type B = A[0]; // string
type C = MyParameters<{ a: string }>; // Error

// 实现MyParameters<T>
type MyParameters<T extends (...params: any[]) => any> = T extends (
  ...params: [...infer P]
) => any
  ? P
  : never;
```

### 😊 implement ConstructorParameters<T>

```ts
class Foo {
  constructor(a: string, b: number, c: boolean) {}
}

type C = MyConstructorParameters<typeof Foo>;
// [a: string, b: number, c: boolean]

// 实现MyConstructorParameters<T>
type MyConstructorParameters<T extends new (...params: any[]) => any> =
  T extends new (...params: [...infer P]) => any ? P : never;
```

### 😊 implement ReturnType<T>

```ts
type Foo = () => { a: string };

type A = MyReturnType<Foo>; // {a: string}

// 实现MyReturnType<T>
type MyReturnType<T extends (...params: any[]) => any> = T extends (
  ...params: any[]
) => infer P
  ? P
  : never;
```

### 😊 implement InstanceType<T>

```ts
class Foo {}
type A = MyInstanceType<typeof Foo>; // Foo
type B = MyInstanceType<() => string>; // Error

// 实现MyInstanceType<T>
type MyInstanceType<T extends new (...params: any[]) => any> = T extends new (
  ...params: any[]
) => infer P
  ? P
  : never;
```

### 😊 implement ThisParameterType<T>

```ts
function Foo(this: { a: string }) {}
function Bar() {}

type A = MyThisParameterType<typeof Foo>; // {a: string}
type B = MyThisParameterType<typeof Bar>; // unknown

// 实现MyThisParameterType<T>
type MyThisParameterType<T extends (this: any, ...params: any[]) => any> =
  T extends (this: infer P, ...params: any[]) => any ? P : unknown;
```

### 😊 implement TupleToUnion<T>

```ts
type Foo = [string, number, boolean];

type Bar = TupleToUnion<Foo>; // string | number | boolean

// 实现TupleToUnion<T>
type TupleToUnion<T extends any[], R = T[0]> = T extends [infer P, ...infer Q]
  ? TupleToUnion<Q, R | P>
  : R;

// 其他回答
type TupleToUnion<T extends any[]> = T[number];
```

### 😊 implement Partial<T>

```ts
type Foo = {
  a: string;
  b: number;
  c: boolean;
};

// below are all valid

const a: MyPartial<Foo> = {};

const b: MyPartial<Foo> = {
  a: "BFE.dev",
};

const c: MyPartial<Foo> = {
  b: 123,
};

const d: MyPartial<Foo> = {
  b: 123,
  c: true,
};

const e: MyPartial<Foo> = {
  a: "BFE.dev",
  b: 123,
  c: true,
};

// 实现MyPartial<T>
type MyPartial<T> = {
  [K in keyof T]?: T[K];
};
```

### 😊 Required<T>

```ts
// all properties are optional
type Foo = {
  a?: string;
  b?: number;
  c?: boolean;
};

const a: MyRequired<Foo> = {};
// Error

const b: MyRequired<Foo> = {
  a: "BFE.dev",
};
// Error

const c: MyRequired<Foo> = {
  b: 123,
};
// Error

const d: MyRequired<Foo> = {
  b: 123,
  c: true,
};
// Error

const e: MyRequired<Foo> = {
  a: "BFE.dev",
  b: 123,
  c: true,
};
// valid

// 实现MyRequired<T>
type MyRequired<T> = {
  [K in keyof T]-?: T[K];
};
```

### 😊 implement LastChar<T>

```ts
type A = LastChar<"BFE">; // 'E'
type B = LastChar<"dev">; // 'v'
type C = LastChar<"">; // never

// 实现FirstChar<T>
type LastChar<
  T extends string,
  A extends string[] = []
> = T extends `${infer P}${infer Q}`
  ? LastChar<Q, [...A, P]>
  : A extends [...infer L, infer R]
  ? R
  : never;
```

### 😊 implement IsNever<T>

```ts
// https://stackoverflow.com/questions/53984650/typescript-never-type-inconsistently-matched-in-conditional-type
// https://www.typescriptlang.org/docs/handbook/advanced-types.html#v
type A = IsNever<never>; // true
type B = IsNever<string>; // false
type C = IsNever<undefined>; // false

// 实现IsNever<T>
type IsNever<T> = [T] extends [never] ? true : false;
```

### 😊 implement KeysToUnion<T>

```ts
type A = KeyToUnion<{
  a: string;
  b: number;
  c: symbol;
}>;
// 'a' | 'b' | 'c'

// 实现KeyToUnion
type KeyToUnion<T> = {
  [K in keyof T]: K;
}[keyof T];
```

### 😊 implement ValuesToUnion<T>

```ts
type A = ValuesToUnion<{
  a: string;
  b: number;
  c: symbol;
}>;
// string | number | symbol

// ValuesToUnion
type ValuesToUnion<T> = T[keyof T];
```

### FindIndex<T, E>

> https://bigfrontend.dev/zh/typescript/Search

```ts
type IsAny<T> = 0 extends T & 1 ? true : false;
type IsNever<T> = [T] extends [never] ? true : false;

type TwoAny<A, B> = IsAny<A> extends IsAny<B> ? IsAny<A> : false;
type TwoNever<A, B> = IsNever<A> extends IsNever<B> ? IsNever<A> : false;

type SingleAny<A, B> = IsAny<A> extends true ? true : IsAny<B>;
type SingleNever<A, B> = IsNever<A> extends true ? true : IsNever<B>;

type FindIndex<T extends any[], E, A extends any[] = []> = T extends [
  infer P,
  ...infer Q
]
  ? TwoAny<P, E> extends true
    ? A["length"]
    : TwoNever<P, E> extends true
    ? A["length"]
    : SingleAny<P, E> extends true
    ? FindIndex<Q, E, [1, ...A]>
    : SingleNever<P, E> extends true
    ? FindIndex<Q, E, [1, ...A]>
    : P extends E
    ? A["length"]
    : FindIndex<Q, E, [1, ...A]>
  : never;
```

### implement Trim<T>

```ts
type A = Trim<"    BFE.dev">; // 'BFE'
type B = Trim<" BFE. dev  ">; // 'BFE. dev'
type C = Trim<"  BFE .   dev  ">; // 'BFE .   dev'

type StringToTuple<
  T extends string,
  A extends any[] = []
> = T extends `${infer K}${infer P}` ? StringToTuple<P, [...A, K]> : A;

type TupleToString<
  T extends any[],
  S extends string = "",
  A extends any[] = []
> = A["length"] extends T["length"]
  ? S
  : TupleToString<T, `${S}${T[A["length"]]}`, [1, ...A]>;

type Trim<T extends string, A extends any[] = StringToTuple<T>> = A extends [
  infer P,
  ...infer Q
]
  ? P extends " "
    ? Trim<T, Q>
    : A extends [...infer M, infer N]
    ? N extends " "
      ? Trim<T, M>
      : TupleToString<A>
    : ""
  : "";
```

还有更多 `UnionToTuple`, `IntersectionToUnion` ?
