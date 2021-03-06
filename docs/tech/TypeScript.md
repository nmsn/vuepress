# TypeScript

## TS中的内置类型

### 常用关键字

#### typeof

`typeof`可以获取值的类型

```ts
const obj = { a: '1' };
type Foo = typeof obj; // { a: string }
```

eg:

```ts
interface Person {
  name: string;
  age: number;
}

const sem: Person = { name: 'semlinker', age: 30 };
type Sem= typeof sem; // -> Person

function toArray(x: number): Array<number> {
  return [x];
}

type Func = typeof toArray; // -> (x: number) => number[]
```

#### keyof

`keyof`可以用来取得一个对象接口的所有`key`值

eg:

```ts
interface Person {
    name: string;
    age: number;
}

type K1 = keyof Person; // "name" | "age"
type K2 = keyof Person[]; // "length" | "toString" | "pop" | "push" | "concat" | "join" 
type K3 = keyof { [x: string]: Person };  // string | number
```

#### in

`in` 则可以遍历枚举类型

```ts
type Keys = "a" | "b"
type Obj =  {
  [p in Keys]: any
} // -> { a: any, b: any }
```

上面 `in` 遍历 `Keys`，并为每个值赋予 `any` 类型。

eg:

```ts
interface Admin {
  name: string;
  privileges: string[];
}

interface Employee {
  name: string;
  startDate: Date;
}

type UnknownEmployee = Employee | Admin;

function printEmployeeInformation(emp: UnknownEmployee) {
  console.log("Name: " + emp.name);
  if ("privileges" in emp) {
    console.log("Privileges: " + emp.privileges);
  }
  if ("startDate" in emp) {
    console.log("Start Date: " + emp.startDate);
  }
}
```

#### infer

在条件类型语句中, 可以用 infer 声明一个类型变量并且对它进行使用，

我们可以用它获取函数的返回类型， 源码如下：

```ts
type ReturnType<T> = T extends (
  ...args: any[]
) => infer R
  ? R
  : any;
```

其实这里的 `infer R` 就是声明一个变量来承载传入函数签名的返回值类型, 简单说就是用它取到函数返回值的类型方便之后使用。

### 工具泛型

内置的泛型在 typescript 包下的 lib/lib.es5.d.ts 文件中

#### Partial

Partial 作用是将传入的属性变为可选项

源码如下

```ts
type Partial<T> = {
    [P in keyof T]?: T[P];
};
```

Partial的实例

```ts
interface Person {
    name: string;
    age: number;
}

type NewPerson = Partial<Person>;

// 相当于
interface NewPerson {
    name?: string;
    age?: number;
}
```

但是 Partial 有个局限性，就是只支持处理第一层的属性

#### Required

Required 的作用是将传入的属性变为必选项, 源码如下

```ts
type Required<T> = { [P in keyof T]-?: T[P] };
```

其中 `-?` 是代表移除 `?` 这个 `modifier` 的标识。
与之对应的还有个 `+?` , 这个含义自然与 `-?` 之前相反, 它是用来把属性变成可选项的，`+` 可省略，见 `Partial`。
再拓展一下，除了可以应用于 `?` 这个 `modifiers` ，还有应用在 `readonly` ，比如 `Readonly`.

#### Readonly

将传入的属性变为只读选项，源码如下

```ts
type Readonly<T> = { readonly [P in keyof T]: T[P] };
```

给子属性添加 `readonly` 的标识，如果将上面的 `readonly` 改成 `-readonly`， 就是移除子属性的 `readonly` 标识。

#### Record

将 `K` 中所有的属性的值转化为 `T` 类型，源码如下

```ts
type Record<K extends keyof any, T> = { [P in K]: T };
```

可以根据 `K` 中的所有可能值来设置 `key`，以及 `value` 的类型，举个例子：

```ts
type T11 = Record<'a' | 'b' | 'c', Person>; // -> { a: Person; b: Person; c: Person; }
```

#### Pick

这个类型则可以将某个类型中的子属性挑出来，变成包含这个类型部分属性的子类型。


从 `T` 中取出 一系列 `K` 的属性, 源码试下如下

```ts
type Pick<T, K extends keyof T> = { [P in K]: T[P] };
```

从源码可以看到 `K` 必须是 `T` 的 key，然后用 `in` 进行遍历, 将值赋给 `P`, 最后 `T[P]` 取得相应属性的值

#### Exclude

Exclude 的作用是从 `T` 中找出 `U` 中没有的元素

```ts
type Exclude<T, U> = T extends U ? never : T;
```

以上语句的意思就是 如果 `T` 能赋值给 `U` 类型的话，那么就会返回 `never` 类型，否则返回 `T`，最终结果是将 `T` 中的某些属于 `U` 的类型移除掉，举个例子：

```ts
type T00 = Exclude<'a' | 'b' | 'c' | 'd', 'a' | 'c' | 'f'>;  // -> 'b' | 'd'
```

可以看到 T 是 `'a' | 'b' | 'c' | 'd'` ，然后 `U` 是 `'a' | 'c' | 'f'` ，返回的新类型就可以将 `U` 中的类型给移除掉，也就是 `'b' | 'd'` 了。

#### Extract

Extract 的作用是提取出 `T` 包含在 `U` 中的元素, 换种更加贴近语义的说法就是从 `T` 中提取出 `U`
源码如下

```ts
type Extract<T, U> = T extends U ? T : never;
```

以上语句的意思就是 如果 `T` 能赋值给 `U` 类型的话，那么就会返回 `T` 类型，否则返回 `never`，最终结果是将 `T` 和 `U` 中共有的属性提取出来，举个例子：

```ts
type T01 = Extract<'a' | 'b' | 'c' | 'd', 'a' | 'c' | 'f'>;  // -> 'a' | 'c'
```

可以看到 `T` 是 `'a' | 'b' | 'c' | 'd'` ，然后 `U` 是 `'a' | 'c' | 'f'` ，返回的新类型就可以将 `T` 和 `U` 中共有的属性提取出来，也就是 `'a' | 'c'` 了。

#### ReturnType

在阅读源码之前我们需要了解一下 infer 这个关键字, 在条件类型语句中, 我们可以用 infer 声明一个类型变量并且对它进行使用,
我们可以用它获取函数的返回类型， 源码如下

```ts
type ReturnType<T> = T extends (
  ...args: any[]
) => infer R
  ? R
  : any;
```

实际使用的话，就可以通过 `ReturnType` 拿到函数的返回类型，如下的示例：

```ts
function foo(x: number): Array<number> {
  return [x];
}

type fn = ReturnType<typeof foo>; // -> number[]
```

#### ThisType

用于指定上下文对象类型的

```ts
interface Person {
    name: string;
    age: number;
}

const obj: ThisType<Person> = {
  dosth() {
    this.name // string
  }
}

// 这样的话，就可以指定 obj 里的所有方法里的上下文对象改成 Person 这个类型了
const obj = {
  dosth(this: Person) {
    this.name // string
  }
}
```

#### InstanceType

该类型的作用是获取构造函数类型的实例类型。

```ts
type InstanceType<T extends new (...args: any[]) => any> = T extends new (...args: any[]) => infer R ? R : any;
```

#### NonNullable

这个类型可以用来过滤类型中的 null 及 undefined 类型

```ts
type NonNullable<T> = T extends null | undefined ? never : T;
```

```ts
type T22 = string | number | null;
type T23 = NonNullable<T22>; // -> string | number;
```

#### Parameters

该类型可以获得函数的参数类型组成的元组类型。

```ts
type Parameters<T extends (...args: any[]) => any> = T extends (...args: infer P) => any ? P : never;
```

```ts
function foo(x: number): Array<number> {
  return [x];
}

type P = Parameters<typeof foo>; // -> [number]
```

此时 `P` 的真实类型就是 `foo` 的参数组成的元组类型 `[number]`。

#### ConstructorParameters

该类型的作用是获得类的参数类型组成的元组类型，源码如下

```ts
type ConstructorParameters<T extends new (...args: any[]) => any> = T extends new (...args: infer P) => any ? P : never;
```

```ts
class Person {
  private firstName: string;
  private lastName: string;
  
  constructor(firstName: string, lastName: string) {
      this.firstName = firstName;
      this.lastName = lastName;
  }
}

type P = ConstructorParameters<typeof Person>; // -> [string, string]
```

此时 `P` 就是 `Person` 中 `constructor` 的参数 `firstName` 和 `lastName` 的类型所组成的元组类型 `[string, string]`。

#### Omit

有时候我们想要继承某个接口，但是又需要在新接口中将某个属性给 overwrite 掉，这时候通过 Pick 和 Exclude 就可以组合出来 Omit，用来忽略对象某些属性功能：

```ts
type Omit<T, K> = Pick<T, Exclude<keyof T, K>>;

// 使用
type Foo = Omit<{name: string, age: number}, 'name'> // -> { age: number }
```

#### Mutable

将 `T` 的所有属性的 `readonly` 移除：

```ts
type Mutable<T> = {
  -readonly [P in keyof T]: T[P]
}
```

#### PowerPartial

内置的 Partial 有个局限性，就是只支持处理第一层的属性，如果是嵌套多层的就没有效果了，不过可以如下自定义：

```ts
type PowerPartial<T> = {
    // 如果是 object，则递归类型
    [U in keyof T]?: T[U] extends object
      ? PowerPartial<T[U]>
      : T[U]
};
```

#### Deferred

相同的属性名称，但使值是一个 Promise，而不是一个具体的值：

```ts
type Deferred<T> = {
    [P in keyof T]: Promise<T[P]>;
};
```

### 参考文献

- TS 中的内置类型简述：[https://github.com/whxaxes/blog/issues/14](https://github.com/whxaxes/blog/issues/14)
- TypeScript 强大的类型别名[https://juejin.im/post/5c2f87ce5188252593122c98](https://juejin.im/post/5c2f87ce5188252593122c98)


## 装饰器

### 类装饰器

```ts
declare type ClassDecorator = <TFunction extends Function>(
  target: TFunction
) => TFunction | void;
```

eg1: 

```ts
function Greeter(target: Function): void {
  target.prototype.greet = function (): void {
    console.log("Hello Semlinker!");
  };
}

@Greeter
class Greeting {
  constructor() {
    // 内部实现
  }
}

let myGreeting = new Greeting();
myGreeting.greet(); // console output: 'Hello Semlinker!';
```

eg2:

```ts
function Greeter(greeting: string) {
  return function (target: Function) {
    target.prototype.greet = function (): void {
      console.log(greeting);
    };
  };
}

@Greeter("Hello TS!")
class Greeting {
  constructor() {
    // 内部实现
  }
}

let myGreeting = new Greeting();
myGreeting.greet(); // console output: 'Hello TS!';
```

### 属性装饰器

```ts
declare type PropertyDecorator = (target:Object, 
  propertyKey: string | symbol ) => void;
```

参数：

- target: Object - 被装饰的类
- propertyKey: string | symbol - 被装饰类的属性名


eg:

```ts
function logProperty(target: any, key: string) {
  delete target[key];

  const backingField = "_" + key;

  Object.defineProperty(target, backingField, {
    writable: true,
    enumerable: true,
    configurable: true
  });

  // property getter
  const getter = function (this: any) {
    const currVal = this[backingField];
    console.log(`Get: ${key} => ${currVal}`);
    return currVal;
  };

  // property setter
  const setter = function (this: any, newVal: any) {
    console.log(`Set: ${key} => ${newVal}`);
    this[backingField] = newVal;
  };

  // Create new property with getter and setter
  Object.defineProperty(target, key, {
    get: getter,
    set: setter,
    enumerable: true,
    configurable: true
  });
}

class Person { 
  @logProperty
  public name: string;

  constructor(name : string) { 
    this.name = name;
  }
}

const p1 = new Person("semlinker");
p1.name = "kakuqo";

// Set: name => semlinker
// Set: name => kakuqo
```

### 方法装饰器

```ts
declare type MethodDecorator = <T>(target:Object, propertyKey: string | symbol,
  descriptor: TypePropertyDescript<T>) => TypedPropertyDescriptor<T> | void;
```

参数：

- target: Object - 被装饰的类
- propertyKey: string | symbol - 方法名
- descriptor: TypePropertyDescript - 属性描述符

eg:

```ts
function LogOutput(tarage: Function, key: string, descriptor: any) {
  let originalMethod = descriptor.value;
  let newMethod = function(...args: any[]): any {
    let result: any = originalMethod.apply(this, args);
    if(!this.loggedOutput) {
      this.loggedOutput = new Array<any>();
    }
    this.loggedOutput.push({
      method: key,
      parameters: args,
      output: result,
      timestamp: new Date()
    });
    return result;
  };
  descriptor.value = newMethod;
}

class Calculator {
  @LogOutput
  double (num: number): number {
    return num * 2;
  }
}

let calc = new Calculator();
calc.double(11);

// console ouput: [{method: "double", output: 22, ...}]
console.log(calc.loggedOutput); 
```

### 参数装饰器

```ts
declare type ParameterDecorator = (target: Object, propertyKey: string | symbol, 
  parameterIndex: number ) => void
```

- target: Object - 被装饰的类
- propertyKey: string | symbol - 方法名
- parameterIndex: number - 方法中参数的索引值

eg:

```ts
function Log(target: Function, key: string, parameterIndex: number) {
  let functionLogged = key || target.prototype.constructor.name;
  console.log(`The parameter in position ${parameterIndex} at ${functionLogged} has
 been decorated`);
}

class Greeter {
  greeting: string;
  constructor(@Log phrase: string) {
 this.greeting = phrase; 
  }
}

// console output: The parameter in position 0 
// at Greeter has been decorated
```
## tsconfig.json compilerOptions配置项

```json
{
  "compilerOptions": {

    /* 基本选项 */
    "target": "es5",                       // 指定 ECMAScript 目标版本: 'ES3' (default), 'ES5', 'ES6'/'ES2015', 'ES2016', 'ES2017', or 'ESNEXT'
    "module": "commonjs",                  // 指定使用模块: 'commonjs', 'amd', 'system', 'umd' or 'es2015'
    "lib": [],                             // 指定要包含在编译中的库文件
    "allowJs": true,                       // 允许编译 javascript 文件
    "checkJs": true,                       // 报告 javascript 文件中的错误
    "jsx": "preserve",                     // 指定 jsx 代码的生成: 'preserve', 'react-native', or 'react'
    "declaration": true,                   // 生成相应的 '.d.ts' 文件
    "sourceMap": true,                     // 生成相应的 '.map' 文件
    "outFile": "./",                       // 将输出文件合并为一个文件
    "outDir": "./",                        // 指定输出目录
    "rootDir": "./",                       // 用来控制输出目录结构 --outDir.
    "removeComments": true,                // 删除编译后的所有的注释
    "noEmit": true,                        // 不生成输出文件
    "importHelpers": true,                 // 从 tslib 导入辅助工具函数
    "isolatedModules": true,               // 将每个文件做为单独的模块 （与 'ts.transpileModule' 类似）.

    /* 严格的类型检查选项 */
    "strict": true,                        // 启用所有严格类型检查选项
    "noImplicitAny": true,                 // 在表达式和声明上有隐含的 any类型时报错
    "strictNullChecks": true,              // 启用严格的 null 检查
    "noImplicitThis": true,                // 当 this 表达式值为 any 类型的时候，生成一个错误
    "alwaysStrict": true,                  // 以严格模式检查每个模块，并在每个文件里加入 'use strict'

    /* 额外的检查 */
    "noUnusedLocals": true,                // 有未使用的变量时，抛出错误
    "noUnusedParameters": true,            // 有未使用的参数时，抛出错误
    "noImplicitReturns": true,             // 并不是所有函数里的代码都有返回值时，抛出错误
    "noFallthroughCasesInSwitch": true,    // 报告 switch 语句的 fallthrough 错误。（即，不允许 switch 的 case 语句贯穿）

    /* 模块解析选项 */
    "moduleResolution": "node",            // 选择模块解析策略： 'node' (Node.js) or 'classic' (TypeScript pre-1.6)
    "baseUrl": "./",                       // 用于解析非相对模块名称的基目录
    "paths": {},                           // 模块名到基于 baseUrl 的路径映射的列表
    "rootDirs": [],                        // 根文件夹列表，其组合内容表示项目运行时的结构内容
    "typeRoots": [],                       // 包含类型声明的文件列表
    "types": [],                           // 需要包含的类型声明文件名列表
    "allowSyntheticDefaultImports": true,  // 允许从没有设置默认导出的模块中默认导入。

    /* Source Map Options */
    "sourceRoot": "./",                    // 指定调试器应该找到 TypeScript 文件而不是源文件的位置
    "mapRoot": "./",                       // 指定调试器应该找到映射文件而不是生成文件的位置
    "inlineSourceMap": true,               // 生成单个 soucemaps 文件，而不是将 sourcemaps 生成不同的文件
    "inlineSources": true,                 // 将代码与 sourcemaps 生成到一个文件中，要求同时设置了 --inlineSourceMap 或 --sourceMap 属性

    /* 其他选项 */
    "experimentalDecorators": true,        // 启用装饰器
    "emitDecoratorMetadata": true          // 为装饰器提供元数据的支持
  }
}
```

