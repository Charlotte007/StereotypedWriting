## 什么是泛型

## type 和 interfa 的区别

1. 类型别名可以为任何类型引入名称。例如基本类型，联合类型等
2. 类型别名不支持继承
3. 类型别名不会创建一个真正的名字
4. 类型别名无法被实现，而接口可以被派生类实现
5. 类型别名重名时编译器会抛出错误，接口重名时会产生合并
## 复杂的类型推导题目 （🤯好难）

### 😊 implement ToNumber<T>

```ts
type A = ToNumber<'1'> // 1
type B = ToNumber<'40'> // 40
type C = ToNumber<'0'> // 0

// 实现ToNumber
type ToNumber<T extends string, R extends any[] = []> =
    T extends `${R['length']}` ? R['length'] : ToNumber<T, [1, ...R]>;
```
### 😊 implement Add<A, B>

```ts
type A = Add<1, 2> // 3
type B = Add<0, 0> // 0

// 实现ADD
type NumberToArray<T, R extends any[]> = T extends R['length'] ? R : NumberToArray<T, [1, ...R]>
type Add<T, R> = [...NumberToArray<T, []>, ...NumberToArray<R, []>]['length']
```

### 😊 implement SmallerThan<A, B>

```ts
type A = SmallerThan<0, 1> // true
type B = SmallerThan<1, 0> // false
type C = SmallerThan<10, 9> // false

// 实现SmallerThan
type SmallerThan<N extends number, M extends number, L extends any[] = [], R extends any[] = []> = 
    N extends L['length'] ? 
        M extends R['length'] ? false : true
        :
        M extends R['length'] ? false : SmallerThan<N, M, [1, ...L], [1, ...R]>;
```

### 😊 implement LargerThan<A, B>

```ts
type A = LargerThan<0, 1> // false
type B = LargerThan<1, 0> // true
type C = LargerThan<10, 9> // true

// 实现LargerThan
type LargerThan<N extends number, M extends number, L extends any[] = [], R extends any[] = []> =
    N extends L['length'] ?
        false : M extends R['length'] ?
            true : LargerThan<N, M, [1, ...L], [1, ...R]>;
```

### 😊 implement IsAny<T>

```ts
type A = IsAny<string> // false
type B = IsAny<any> // true
type C = IsAny<unknown> // false
type D = IsAny<never> // false

// 实现IsAny
type IsAny<T> = true extends (T extends never ? true : false) ?
                false extends (T extends never ? true : false) ? true : false
                : false;

// 更简单的实现
type IsAny<T> = 0 extends (T & 1) ? true : false;
```

### 😊 implement Filter<T, A>

```ts
type A = Filter<[1,'BFE', 2, true, 'dev'], number> // [1, 2]
type B = Filter<[1,'BFE', 2, true, 'dev'], string> // ['BFE', 'dev']
type C = Filter<[1,'BFE', 2, any, 'dev'], string> // ['BFE', any, 'dev']

// 实现Filter
type Filter<T extends any[], A, N extends any[] = []> =
    T extends [infer P, ...infer Q] ?
        0 extends (P & 1) ? Filter<Q, A, [...N, P]> : 
        P extends A ? Filter<Q, A, [...N, P]> : Filter<Q, A, N>
        : N;
```

### 😊 implement TupleToString<T>

```ts
type A = TupleToString<['a']> // 'a'
type B = TupleToString<['B', 'F', 'E']> // 'BFE'
type C = TupleToString<[]> // ''

// 实现TupleToString
```
### implement RepeatString<T, C>

```ts
type A = RepeatString<'a', 3> // 'aaa'
type B = RepeatString<'a', 0> // ''

// 实现RepeatString
type RepeatString<T extends string, C extends number, S extends string = '', A extends any[] = []> =
    A['length'] extends C ? S : RepeatString<T, C, `${T}${S}`, [1, ...A]>
```

### 😊 implement Push<T, I>

```ts
type A = Push<[1,2,3], 4> // [1,2,3,4]
type B = Push<[1], 2> // [1, 2]
type C = Push<[], string> // [string]

// 实现Push
type Push<T extends any[], I> = T extends [...infer P] ? [...P, I] : [I]
```

### 😊 implement Flat<T>

```ts
type A = Flat<[1,2,3]> // [1,2,3]
type B = Flat<[1,[2,3], [4,[5,[6]]]]> // [1,2,3,4,5,6]
type C = Flat<[]> // []

// 实现Flat
type Flat<T extends any[]> =
    T extends [infer P, ...infer Q] ?
        P extends any[] ? [...Flat<P>, ...Flat<Q>] : [P, ...Flat<Q>]
        : [];
```
### 😊 implement Shift<T>

```ts
type A = Shift<[1,2,3]> // [2,3]
type B = Shift<[1]> // []
type C = Shift<[]> // []

// 实现Shift
type Shift<T extends any[]> = T extends [infer P, ...infer Q] ? [...Q] : [];
```

### 😊 implement Repeat<T, C>

```ts
type A = Repeat<number, 3> // [number, number, number]
type B = Repeat<string, 2> // [string, string]
type C = Repeat<1, 1> // [1, 1]
type D = Repeat<0, 0> // []

// 实现Repeat
type Repeat<T, C, R extends any[] = []> = 
    R['length'] extends C ? R : Repeat<T, C, [...R, T]>
```
### 😭 implement IsEmptyType<T>

```ts
type A = IsEmptyType<string> // false
type B = IsEmptyType<{a: 3}> // false
type C = IsEmptyType<{}> // true
type D = IsEmptyType<any> // false
type E = IsEmptyType<object> // false
type F = IsEmptyType<Object> // false

// 实现IsEmptyType
```
