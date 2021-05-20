## 什么是泛型

## type 和 interfa 的区别

1. 类型别名可以为任何类型引入名称。例如基本类型，联合类型等
2. 类型别名不支持继承
3. 类型别名不会创建一个真正的名字
4. 类型别名无法被实现，而接口可以被派生类实现
5. 类型别名重名时编译器会抛出错误，接口重名时会产生合并
## 复杂的类型推导题目 （🤯好难）

### implement ToNumber<T>

```ts
type A = ToNumber<'1'> // 1
type B = ToNumber<'40'> // 40
type C = ToNumber<'0'> // 0

// 实现ToNumber
type ToNumber<T extends string, R extends any[] = []> =
    T extends `${R['length']}` ? R['length'] : ToNumber<T, [1, ...R]>;
```
### implement Add<A, B>

```ts
type A = Add<1, 2> // 3
type B = Add<0, 0> // 0

// 实现ADD
type NumberToArray<T, R extends any[]> = T extends R['length'] ? R : NumberToArray<T, [1, ...R]>
type Add<T, R> = [...NumberToArray<T, []>, ...NumberToArray<R, []>]['length']
```

### implement SmallerThan<A, B>

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

### implement LargerThan<A, B>

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

### implement Filter<T, A>

```ts
type A = Filter<[1,'BFE', 2, true, 'dev'], number> // [1, 2]
type B = Filter<[1,'BFE', 2, true, 'dev'], string> // ['BFE', 'dev']
type C = Filter<[1,'BFE', 2, any, 'dev'], string> // ['BFE', any, 'dev']

// 实现Filter

```
