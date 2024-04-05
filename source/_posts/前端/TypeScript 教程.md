---
title: TypeScript 教程
date: 2024-03-29 18:01:05
tags: 
  - 前端
  - TypeScript
categories: 
  - 前端
top_img: https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E5%89%8D%E7%AB%AF/TypeScript%20%E6%95%99%E7%A8%8B/preview.jpg
cover: https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E5%89%8D%E7%AB%AF/TypeScript%20%E6%95%99%E7%A8%8B/preview.jpg
---



# TypeScript 教程

## 基本用法



TypeScript 代码最明显的特征，就是为 JavaScript 变量加上了类型声明

```JS
let foo:string;
```

上面示例中，变量`foo`的后面使用冒号，声明了它的类型为`string`

另外，TypeScript 规定，变量只有赋值后才能使用，否则就会报错

```JS
let x:number;
console.log(x) // 报错
```

上面示例中，变量`x`没有赋值就被读取，导致报错。而 JavaScript 允许这种行为，不会报错，没有赋值的变量会返回`undefined`



类型声明并不是必需的，如果没有，TypeScript 会自己推断类型

```JS
let foo = 123;
```

上面示例中，变量`foo`并没有类型声明，TypeScript 就会推断它的类型。由于它被赋值为一个数值，因此 TypeScript 推断它的类型为`number`

后面，如果变量`foo`更改为其他类型的值，跟推断的类型不一致，TypeScript 就会报错



最简单的 TypeScript 使用方法，就是使用官网的在线编译页面，叫做 [TypeScript Playground](http://www.typescriptlang.org/play/)

只要打开这个网页，把 TypeScript 代码贴进文本框，它就会在当前页面自动编译出 JavaScript 代码，还可以在浏览器执行编译产物。如果编译报错，它也会给出详细的报错信息

这个页面还具有支持完整的 IDE 支持，可以自动语法提示。此外，它支持把代码片段和编译器设置保存成 URL，分享给他人



TypeScript 官方提供的编译器叫做 tsc，可以将 TypeScript 脚本编译成 JavaScript 脚本。本机想要编译 TypeScript 代码，必须安装 tsc

根据约定，TypeScript 脚本文件使用`.ts`后缀名，JavaScript 脚本文件使用`.js`后缀名。tsc 的作用就是把`.ts`脚本转变成`.js`脚本



------

## 三种特殊类型



**any 类型**

any 类型表示没有任何限制，该类型的变量可以赋予任意类型的值

```JS
let x:any;

x = 1; // 正确
x = 'foo'; // 正确
x = true; // 正确
```

变量类型一旦设为`any`，TypeScript 实际上会关闭这个变量的类型检查。即使有明显的类型错误，只要句法正确，都不会报错

当类型是`any`，TypeScript 不对其进行类型检查。由于这个原因，应该尽量避免使用`any`类型，否则就失去了使用 TypeScript 的意义



**unknow 类型**

为了解决`any`类型“污染”其他变量的问题，TypeScript 3.0 引入了`unknown`类型。它与`any`含义相同，表示类型不确定，可能是任意类型，但是它的使用有一些限制，不像`any`那样自由，可以视为严格版的`any`

`unknown`跟`any`的相似之处，在于所有类型的值都可以分配给`unknown`类型

```JS
let x:unknown;

x = true; // 正确
x = 42; // 正确
x = 'Hello World'; // 正确
```



`unknown`类型跟`any`类型的不同之处在于，它不能直接使用。主要有以下几个限制：

1. 首先，`unknown`类型的变量，不能直接赋值给其他类型的变量（除了`any`类型和`unknown`类型）
2. 其次，不能直接调用`unknown`类型变量的方法和属性
3. 再次，`unknown`类型变量能够进行的运算是有限的，只能进行比较运算（运算符`==`、`===`、`!=`、`!==`、`||`、`&&`、`?`）、取反运算（运算符`!`）、`typeof`运算符和`instanceof`运算符这几种，其他运算都会报错



那么，怎么才能使用`unknown`类型变量呢

答案是只有经过“类型缩小”，`unknown`类型变量才可以使用。所谓“类型缩小”，就是缩小`unknown`变量的类型范围，确保不会出错

```JS
let a:unknown = 1;

if (typeof a === 'number') {
  let r = a + 10; // 正确
}
```



**never 类型**

为了保持与集合论的对应关系，以及类型运算的完整性，TypeScript 还引入了“空类型”的概念，即该类型为空，不包含任何值

由于不存在任何属于“空类型”的值，所以该类型被称为`never`，即不可能有这样的值

`never`类型的使用场景，主要是在一些类型运算之中，保证类型运算的完整性。另外，不可能返回值的函数，返回值的类型就可以写成`never`



------

## 联合类型和交叉类型



联合类型（union types）指的是多个类型组成的一个新类型，使用符号`|`表示。联合类型`A|B`表示，任何一个类型只要属于`A`或`B`，就属于联合类型`A|B`

```TS
let x:string|number;

x = 123; // 正确
x = 'abc'; // 正确
```

联合类型可以与值类型相结合，表示一个变量的值有若干种可能

```TS
let setting:true|false;

let gender:'male'|'female';

let rainbowColor:'赤'|'橙'|'黄'|'绿'|'青'|'蓝'|'紫';
```



交叉类型（intersection types）指的多个类型组成的一个新类型，使用符号`&`表示

交叉类型`A&B`表示，任何一个类型必须同时属于`A`和`B`，才属于交叉类型`A&B`，即交叉类型同时满足`A`和`B`的特征

```TS
let x:number&string;
```

上面示例中，变量`x`同时是数值和字符串，这当然是不可能的，所以 TypeScript 会认为`x`的类型实际是`never`



`type`命令用来定义一个类型的别名

```TS
type Age = number;

let age:Age = 55;
```

上面示例中，`type`命令为`number`类型定义了一个别名`Age`。这样就能像使用`number`一样，使用`Age`作为类型

别名可以让类型的名字变得更有意义，也能增加代码的可读性，还可以使复杂类型用起来更方便，便于以后修改变量的类型

别名不允许重名



------

## 数组



TypeScript 数组有一个根本特征：所有成员的类型必须相同，但是成员数量是不确定的，可以是无限数量的成员，也可以是零成员

数组的类型有两种写法。第一种写法是在数组成员的类型后面，加上一对方括号

```TS
let arr:number[] = [1, 2, 3];
```

如果数组成员的类型比较复杂，可以写在圆括号里面

```TS
let arr:(number|string)[];
```

数组类型的第二种写法是使用 TypeScript 内置的 Array 接口

```TS
let arr:Array<number> = [1, 2, 3];
```

这种写法对于成员类型比较复杂的数组，代码可读性会稍微好一些

```TS
let arr:Array<number|string>;
```



由于成员数量可以动态变化，所以 TypeScript 不会对数组边界进行检查，越界访问数组并不会报错

```TS
let arr:number[] = [1, 2, 3];
let foo = arr[3]; // 正确
```

TypeScript 允许使用方括号读取数组成员的类型



JavaScript 规定，`const`命令声明的数组变量是可以改变成员的

```TS
const arr = [0, 1];
arr[0] = 2;
```

TypeScript 允许声明只读数组，方法是在数组类型前面加上`readonly`关键字

```TS
const arr:readonly number[] = [0, 1];

arr[1] = 2; // 报错
arr.push(3); // 报错
delete arr[0]; // 报错
```



TypeScript 使用`T[][]`的形式，表示二维数组，`T`是最底层数组成员的类型

```TS
var multi:number[][] = [[1,2,3], [23,24,25]];
```



------

## 元组



元组（tuple）是 TypeScript 特有的数据类型，JavaScript 没有单独区分这种类型。它表示成员类型可以自由设置的数组，即数组的各个成员的类型可以不同

由于成员的类型可以不一样，所以元组必须明确声明每个成员的类型

```TS
const s:[string, string, boolean] = ['a', 'b', true];
```

元组类型的写法，与上一章的数组有一个重大差异。数组的成员类型写在方括号外面（`number[]`），元组的成员类型是写在方括号里面（`[number]`）。TypeScript 的区分方法就是，成员类型写在方括号里面的就是元组，写在外面的就是数组

```TS
// 数组
let a:number[] = [1];

// 元组
let t:[number] = [1];
```



元组成员的类型可以添加问号后缀（`?`），表示该成员是可选的

```TS
let a:[number, number?] = [1];
```

注意，问号只能用于元组的尾部成员，也就是说，所有可选成员必须在必选成员之后

由于需要声明每个成员的类型，所以大多数情况下，元组的成员数量是有限的，从类型声明就可以明确知道，元组包含多少个成员，越界的成员会报错

但是，使用扩展运算符（`...`），可以表示不限成员数量的元组

```TS
type NamedNums = [
  string,
  ...number[]
];

const a:NamedNums = ['A', 1, 2];
const b:NamedNums = ['B', 1, 2, 3];
```



元组也可以是只读的，不允许修改，有两种写法

```TS
// 写法一
type t = readonly [number, string]

// 写法二
type t = Readonly<[number, string]>
```

上面示例中，两种写法都可以得到只读元组，其中写法二是一个泛型，用到了工具类型`Readonly<T>`



------

## Symbol



Symbol 是 ES2015 新引入的一种原始类型的值。它类似于字符串，但是每一个 Symbol 值都是独一无二的，与其他任何值都不相等

Symbol 值通过`Symbol()`函数生成。在 TypeScript 里面，Symbol 的类型使用`symbol`表示

```TS
let x:symbol = Symbol();
let y:symbol = Symbol();

x === y // false
```

上面示例中，变量`x`和`y`的类型都是`symbol`，且都用`Symbol()`生成，但是它们是不相等的



`symbol`类型包含所有的 Symbol 值，但是无法表示某一个具体的 Symbol 值

为了解决这个问题，TypeScript 设计了`symbol`的一个子类型`unique symbol`，它表示单个的、某个具体的 Symbol 值

因为`unique symbol`表示单个值，所以这个类型的变量是不能修改值的，只能用`const`命令声明，不能用`let`声明

```TS
// 正确
const x:unique symbol = Symbol();

// 报错
let y:unique symbol = Symbol();
```



------

## 函数



函数的类型声明，需要在声明函数时，给出参数的类型和返回值的类型

```TS
function hello(txt:string):void {
  console.log('hello ' + txt);
}
```

返回值的类型通常可以不写，因为 TypeScript 自己会推断出来

```TS
function hello(txt:string) {
  console.log('hello ' + txt);
}
```

不过，有时候出于文档目的，或者为了防止不小心改掉返回值，还是会写返回值的类型



如果变量被赋值为一个函数，变量的类型有两种写法

```TS
// 写法一
const hello = function (txt:string) {
  console.log('hello ' + txt);
}

// 写法二
const hello:(txt:string) => void = function (txt) {
  console.log('hello ' + txt);
};
```

如果函数的类型定义很冗长，或者多个函数使用同一种类型，写法二用起来就很麻烦。因此，往往用`type`命令为函数类型定义一个别名，便于指定给其他变量

```TS
type MyFunc = (txt:string) => void;

const hello:MyFunc = function (txt) {
  console.log('hello ' + txt);
};
```



箭头函数是普通函数的一种简化写法，它的类型写法与普通函数类似

```TS
const repeat = (
  str:string,
  times:number
):string => str.repeat(times);
```



如果函数的某个参数可以省略，则在参数名后面加问号表示

```TS
function f(x?:number) {
  // ...
}

f(); // OK
f(10); // OK
```

参数名带有问号，表示该参数的类型实际上是`原始类型|undefined`，它有可能为`undefined`。比如，上例的`x`虽然类型声明为`number`，但是实际上是`number|undefined`



TypeScript 函数的参数默认值写法，与 JavaScript 一致

设置了默认值的参数，就是可选的。如果不传入该参数，它就会等于默认值

```TS
function createPoint(
  x:number = 0,
  y:number = 0
):[number, number] {
  return [x, y];
}

createPoint() // [0, 0]
```



如果函数内部不能修改某个参数，可以在函数定义时，在参数类型前面加上`readonly`关键字，表示这是只读参数

```TS
function arraySum(arr:readonly number[]) {
  // ...
  arr[0] = 0; // 报错
}
```



函数内部允许声明其他类型，该类型只在函数内部有效，称为局部类型

```TS
function hello(txt:string) {
  type message = string;
  let newTxt:message = 'hello ' + txt;
  return newTxt;
}

const newTxt:message = hello('world'); // 报错
```



有些函数可以接受不同类型或不同个数的参数，并且根据参数的不同，会有不同的函数行为。这种根据参数类型不同，执行不同逻辑的行为，称为函数重载

```TS
reverse('abc') // 'cba'
reverse([1, 2, 3]) // [3, 2, 1]
```

这意味着，该函数内部有处理字符串和数组的两套逻辑，根据参数类型的不同，分别执行对应的逻辑。这就叫“函数重载”

TypeScript 对于“函数重载”的类型声明方法是，逐一定义每一种情况的类型

```TS
function reverse(str:string):string;
function reverse(arr:any[]):any[];
```

上面示例中，分别对函数`reverse()`的两种参数情况，给予了类型声明。但是，到这里还没有结束，后面还必须对函数`reverse()`给予完整的类型声明

```TS
function reverse(str:string):string;
function reverse(arr:any[]):any[];
function reverse(stringOrArray:string|any[]):string|any[] {
  if (typeof stringOrArray === 'string')
    return stringOrArray.split('').reverse().join('');
  else
    return stringOrArray.slice().reverse();
}
```



------

## 对象



除了原始类型，对象是 JavaScript 最基本的数据结构。TypeScript 对于对象类型有很多规则

对象类型的最简单声明方法，就是使用大括号表示对象，在大括号内部声明每个属性和方法的类型

```TS
const obj:{
  x:number;
  y:number;
} = { x: 1, y: 1 };
```

一旦声明了类型，对象赋值时，就不能缺少指定的属性，也不能有多余的属性

读写不存在的属性也会报错

同样地，也不能删除类型声明中存在的属性，修改属性值是可以的



如果某个属性是可选的（即可以忽略），需要在属性名后面加一个问号

```TS
const obj: {
  x: number;
  y?: number;
} = { x: 1 };
```



属性名前面加上`readonly`关键字，表示这个属性是只读属性，不能修改

```TS
interface MyInterface {
  readonly prop: number;
}
```

只读属性只能在对象初始化期间赋值，此后就不能修改该属性



如果对象的属性非常多，一个个声明类型就很麻烦，而且有些时候，无法事前知道对象会有多少属性，比如外部 API 返回的对象。这时 TypeScript 允许采用属性名表达式的写法来描述类型，称为“属性名的索引类型”

索引类型里面，最常见的就是属性名的字符串索引

```TS
type MyObj = {
  [property: string]: string
};

const obj:MyObj = {
  foo: 'a',
  bar: 'b',
  baz: 'c',
};
```



------

## interface



interface 是对象的模板，可以看作是一种类型约定，中文译为“接口”。使用了某个模板的对象，就拥有了指定的类型结构

```TS
interface Person {
  firstName: string;
  lastName: string;
  age: number;
}
```

实现该接口很简单，只要指定它作为对象的类型即可

```TS
const p:Person = {
  firstName: 'John',
  lastName: 'Smith',
  age: 25
};
```

方括号运算符可以取出 interface 某个属性的类型

```TS
interface Foo {
  a: string;
}

type A = Foo['a']; // string
```





interface 可以表示对象的各种语法，它的成员有 5 种形式

1. 对象属性

   ```TS
   interface Point {
     x: number;
     y: number;
   }
   ```

   属性之间使用分号或逗号分隔，最后一个属性结尾的分号或逗号可以省略

   如果属性是可选的，就在属性名后面加一个问号

   ```TS
   interface Foo {
     x?: string;
   }
   ```

   如果属性是只读的，需要加上`readonly`修饰符

   ```TS
   interface A {
     readonly a: string;
   }
   ```

2. 对象的属性索引

   ```TS
   interface A {
     [prop: string]: number;
   }
   ```

   上面示例中，`[prop: string]`就是属性的字符串索引，表示属性名只要是字符串，都符合类型要求

   属性索引共有`string`、`number`和`symbol`三种类型

   一个接口中，最多只能定义一个字符串索引。字符串索引会约束该类型中所有名字为字符串的属性

   如果一个 interface 同时定义了字符串索引和数值索引，那么数值索引必须服从于字符串索引。因为在 JavaScript 中，数值属性名最终是自动转换成字符串属性名

3. 对象的方法

   对象的方法共有三种写法

   ```TS
   // 写法一
   interface A {
     f(x: boolean): string;
   }
   
   // 写法二
   interface B {
     f: (x: boolean) => string;
   }
   
   // 写法三
   interface C {
     f: { (x: boolean): string };
   }
   ```

   类型方法可以重载

   ```TS
   interface A {
     f(): number;
     f(x: boolean): boolean;
     f(x: string, y: string): string;
   }
   ```

   interface 里面的函数重载，不需要给出实现。但是，由于对象内部定义方法时，无法使用函数重载的语法，所以需要额外在对象外部给出函数方法的实现

4. 函数

   interface 也可以用来声明独立的函数

   ```TS
   interface Add {
     (x:number, y:number): number;
   }
   
   const myAdd:Add = (x,y) => x + y;
   ```

5. 构造函数

   interface 内部可以使用`new`关键字，表示构造函数

   ```TS
   interface ErrorConstructor {
     new (message?: string): Error;
   }
   ```

   

interface 可以继承其他类型，主要有下面三种情况：

1. interface 可以使用`extends`关键字，继承其他 interface

   `extends`关键字会从继承的接口里面拷贝属性类型，这样就不必书写重复的属性

   如果子接口与父接口存在同名属性，那么子接口的属性会覆盖父接口的属性

   interface 允许多重继承，多重继承时，如果多个父接口存在同名属性，那么这些同名属性不能有类型冲突，否则会报错

2. interface 可以继承`type`命令定义的对象类型

   ```TS
   type Country = {
     name: string;
     capital: string;
   }
   
   interface CountryWithPop extends Country {
     population: number;
   }
   ```

3. interface 还可以继承 class，即继承该类的所有成员



`interface`命令与`type`命令作用类似，都可以表示对象类型

interface 与 type 的区别有下面几点

1. `type`能够表示非对象类型，而`interface`只能表示对象类型（包括数组、函数等）

2. `interface`可以继承其他类型，`type`不支持继承

3. 同名`interface`会自动合并，同名`type`则会报错。也就是说，TypeScript 不允许使用`type`多次定义同一个类型

4. `interface`不能包含属性映射（mapping），`type`可以

5. `this`关键字只能用于`interface`

6. type 可以扩展原始数据类型，interface 不行

7. `interface`无法表达某些复杂类型（比如交叉类型和联合类型），但是`type`可以



------

## class



类（class）是面向对象编程的基本构件，封装了属性和方法，TypeScript 给予了全面支持

类的属性可以在顶层声明，也可以在构造方法内部声明

对于顶层声明的属性，可以在声明时同时给出类型

```TS
class Point {
  x:number;
  y:number;
}
```

如果不给出类型，TypeScript 会认为`x`和`y`的类型都是`any`

如果声明时给出初值，可以不写类型，TypeScript 会自行推断属性的类型

```TS
class Point {
  x = 0;
  y = 0;
}
```



属性名前面加上 readonly 修饰符，就表示该属性是只读的。实例对象不能修改这个属性

```TS
class A {
  readonly id = 'foo';
}

const a = new A();
a.id = 'bar'; // 报错
```

readonly 属性的初始值，可以写在顶层属性，也可以写在构造方法里面

```TS
class A {
  readonly id:string;

  constructor() {
    this.id = 'bar'; // 正确
  }
}
```



类的方法就是普通函数，类型声明方式与函数一致

```TS
class Point {
  x:number;
  y:number;

  constructor(x:number, y:number) {
    this.x = x;
    this.y = y;
  }

  add(point:Point) {
    return new Point(
      this.x + point.x,
      this.y + point.y
    );
  }
}
```

类的方法跟普通函数一样，可以使用参数默认值，以及函数重载



存取器（accessor）是特殊的类方法，包括取值器（getter）和存值器（setter）两种方法

它们用于读写某个属性，取值器用来读取属性，存值器用来写入属性

```TS
class C {
  _name = '';
  get name() {
    return this._name;
  }
  set name(value) {
    this._name = value;
  }
}
```

`set name()`是存值器，其中`set`是关键词，`name`是属性名。外部写入`name`属性时，实例对象会自动调用这个方法，并将所赋的值作为函数参数传入

TypeScript 对存取器有以下规则：

1. 如果某个属性只有`get`方法，没有`set`方法，那么该属性自动成为只读属性
2. TypeScript 5.1 版之前，`set`方法的参数类型，必须兼容`get`方法的返回值类型，否则报错
3. `get`方法与`set`方法的可访问性必须一致，要么都为公开方法，要么都为私有方法



类允许定义属性索引

```TS
class MyClass {
  [s:string]: boolean |
    ((s:string) => boolean);

  get(s:string) {
    return this[s] as boolean;
  }
}
```

上面示例中，`[s:string]`表示所有属性名类型为字符串的属性，它们的属性值要么是布尔值，要么是返回布尔值的函数



类的内部成员的外部可访问性，由三个可访问性修饰符（access modifiers）控制：`public`、`private`和`protected`

这三个修饰符的位置，都写在属性或方法的最前面

`public`修饰符表示这是公开成员，外部可以自由访问

`private`修饰符表示私有成员，只能用在当前类的内部，类的实例和子类都不能使用该成员

`protected`修饰符表示该成员是保护成员，只能在类的内部使用该成员，实例无法使用该成员，但是子类内部可以使用



类的内部可以使用`static`关键字，定义静态成员

静态成员是只能通过类本身使用的成员，不能通过实例对象使用

```TS
class MyClass {
  static x = 0;
  static printX() {
    console.log(MyClass.x);
  }
}

MyClass.x // 0
MyClass.printX() // 0
```

静态私有属性也可以用 ES6 语法的`#`前缀表示

`public`和`protected`的静态成员可以被继承



TypeScript 允许在类的定义前面，加上关键字`abstract`，表示该类不能被实例化，只能当作其他类的模板。这种类就叫做“抽象类”（abstract class）



------

## 泛型



`function`关键字定义的泛型函数，类型参数放在尖括号中，写在函数名后面

```TS
function id<T>(arg:T):T {
  return arg;
}
```

那么对于变量形式定义的函数，泛型有下面两种写法

```TS
// 写法一
let myId:<T>(arg:T) => T = id;

// 写法二
let myId:{ <T>(arg:T): T } = id;
```

interface 也可以采用泛型的写法

```TS
interface Box<Type> {
  contents: Type;
}

let box:Box<string>;
```

泛型接口还有第二种写法

```TS
interface Fn {
  <Type>(arg:Type): Type;
}

function id<Type>(arg:Type): Type {
  return arg;
}

let myId:Fn = id;
```



泛型类的类型参数写在类名后面

```TS
class Pair<K, V> {
  key: K;
  value: V;
}
```



泛型有一些使用注意点

1. 尽量少用泛型
2. 类型参数越少越好
3. 类型参数需要出现两次
4. 泛型可以嵌套



------

## Enum



TypeScript 就设计了 Enum 结构，用来将相关常量放在一个容器里面，方便使用

```TS
enum Color {
  Red,     // 0
  Green,   // 1
  Blue     // 2
}
```

Enum 结构比较适合的场景是，成员的值不重要，名字更重要，从而增加代码的可读性和可维护性

[TypeScript 5.0](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-5-0.html#enum-overhaul) 之前，Enum 有一个 Bug，就是 Enum 类型的变量可以赋值为任何数值，TypeScript 5.0 纠正了这个问题



Enum 成员的值除了设为数值，还可以设为字符串。也就是说，Enum 也可以用作一组相关字符串的集合

```TS
enum Direction {
  Up = 'UP',
  Down = 'DOWN',
  Left = 'LEFT',
  Right = 'RIGHT',
}
```

Enum 成员可以是字符串和数值混合赋值



keyof 运算符可以取出 Enum 结构的所有成员名，作为联合类型返回

```TS
enum MyEnum {
  A = 'a',
  B = 'b'
}

// 'A'|'B'
type Foo = keyof typeof MyEnum;
```



数值 Enum 存在反向映射，即可以通过成员值获得成员名

```TS
enum Weekdays {
  Monday = 1,
  Tuesday,
  Wednesday,
  Thursday,
  Friday,
  Saturday,
  Sunday
}

console.log(Weekdays[3]) // Wednesday
```



------

## 类型断言



TypeScript 提供了“类型断言”这样一种手段，允许开发者在代码中“断言”某个值的类型，告诉编译器此处的值是什么类型。TypeScript 一旦发现存在类型断言，就不再对该值进行类型推断，而是直接采用断言给出的类型

这种做法的实质是，允许开发者在某个位置“绕过”编译器的类型推断，让本来通不过类型检查的代码能够通过，避免编译器报错。这样虽然削弱了 TypeScript 类型系统的严格性，但是为开发者带来了方便，毕竟开发者比编译器更了解自己的代码

```TS
type T = 'a'|'b'|'c';

let foo = 'a';
let bar:T = foo as T; // 正确
```

上面示例中，最后一行的`foo as T`表示告诉编译器，变量`foo`的类型断言为`T`，所以这一行不再需要类型推断了，编译器直接把`foo`的类型当作`T`，就不会报错了



类型断言有两种语法，推荐使用语法二

```TS
// 语法一：<类型>值
<Type>value

// 语法二：值 as 类型
value as Type
```



下面是一个网页编程的实际例子

```TS
const username = document.getElementById('username');

if (username) {
  (username as HTMLInputElement).value; // 正确
}
```



如果没有声明变量类型，let 命令声明的变量，会被类型推断为 TypeScript 内置的基本类型之一；const 命令声明的变量，则被推断为值类型常量

```TS
// 类型推断为基本类型 string
let s1 = 'JavaScript';

// 类型推断为字符串 “JavaScript”
const s2 = 'JavaScript';
```



对于那些可能为空的变量（即可能等于`undefined`或`null`），TypeScript 提供了非空断言，保证这些变量不会为空，写法是在变量名后面加上感叹号`!`

```TS
function f(x?:number|null) {
  validateNumber(x); // 自定义函数，确保 x 是数值
  console.log(x!.toFixed());
}

function validateNumber(e?:number|null) {
  if (typeof e !== 'number')
    throw new Error('Not a number');
}
```



------

## 模块



任何包含 import 或 export 语句的文件，就是一个模块（module）。相应地，如果文件不包含 export 语句，就是一个全局的脚本文件

模块本身就是一个作用域，不属于全局作用域。模块内部的变量、函数、类只在内部可见，对于模块外部是不可见的。暴露给外部的接口，必须用 export 命令声明；如果其他文件要使用模块的接口，必须用 import 命令来输入

如果一个文件不包含 export 语句，但是希望把它当作一个模块（即内部变量对外不可见），可以在脚本头部添加一行语句

```TS
export {};
```

TypeScript 模块除了支持所有 ES 模块的语法，特别之处在于允许输出和输入类型

```TS
export type Bool = true | false;
```



import 在一条语句中，可以同时输入类型和正常接口

```TS
// a.ts
export interface A {
  foo: string;
}

export let a = 123;

// b.ts
import { A, a } from './a';
```

这样很不利于区分类型和正常接口，容易造成混淆。为了解决这个问题，TypeScript 引入了两个解决方法

第一个方法是在 import 语句输入的类型前面加上`type`关键字

```TS
import { type A, a } from './a';
```

第二个方法是使用 import type 语句，这个语句只能输入类型，不能输入正常接口

```TS
// 正确
import type { A } from './a';

// 报错
import type { a } from './a';
```



CommonJS 是 Node.js 的专用模块格式，与 ES 模块格式不兼容

TypeScript 使用`import =`语句输入 CommonJS 模块

```TS
import fs = require('fs');
const code = fs.readFileSync('hello.ts', 'utf8');
```

除了使用`import =`语句，TypeScript 还允许使用`import * as [接口名] from "模块文件"`输入 CommonJS 模块

TypeScript 使用`export =`语句，输出 CommonJS 模块的对象，等同于 CommonJS 的`module.exports`对象

```TS
let obj = { foo: 123 };

export = obj;
```

`export =`语句输出的对象，只能使用`import =`语句加载



TypeScript 允许开发者在`tsconfig.json`文件里面，手动指定脚本模块的路径

1. baseUrl

   `baseUrl`字段可以手动指定脚本模块的基准目录

   ```TS
   {
     "compilerOptions": {
       "baseUrl": "."
     }
   }
   ```

2. paths

   `paths`字段指定非相对路径的模块与实际脚本的映射

   ```TS
   {
     "compilerOptions": {
       "baseUrl": ".",
       "paths": {
         "jquery": ["node_modules/jquery/dist/jquery"]
       }
     }
   }
   ```

3. rootDirs

   `rootDirs`字段指定模块定位时必须查找的其他目录

   ```TS
   {
     "compilerOptions": {
       "rootDirs": ["src/zh", "src/de", "src/#{locale}"]
     }
   }
   ```



------

## declare 关键字



declare 关键字用来告诉编译器，某个类型是存在的，可以在当前文件中使用

它的主要作用，就是让当前文件可以使用其他文件声明的类型。举例来说，自己的脚本使用外部库定义的函数，编译器会因为不知道外部函数的类型定义而报错，这时就可以在自己的脚本里面使用`declare`关键字，告诉编译器外部函数的类型。这样的话，编译单个脚本就不会因为使用了外部类型而报错

declare 只能用来描述已经存在的变量和数据结构，不能用来声明新的变量和数据结构。另外，所有 declare 语句都不会出现在编译后的文件里面

declare 关键字可以描述以下类型。

- 变量（const、let、var 命令声明）
- type 或者 interface 命令声明的类型
- class
- enum
- 函数（function）
- 模块（module）
- 命名空间（namespace）



举例来说，当前脚本使用了其他脚本定义的全局变量`x`

```TS
x = 123; // 报错
```

上面示例中，变量`x`是其他脚本定义的，当前脚本不知道它的类型，编译器就会报错

这时使用 declare 命令给出它的类型，就不会报错了

```TS
declare let x:number;
x = 1;
```



------

## d.ts 类型声明文件



单独使用的模块，一般会同时提供一个单独的类型声明文件（declaration file），把本模块的外部接口的所有类型都写在这个文件里面，便于模块使用者了解接口，也便于编译器检查使用者的用法是否正确

类型声明文件里面只有类型代码，没有具体的代码实现。它的文件名一般为`[模块名].d.ts`的形式，其中的`d`表示 declaration（声明）

举例来说，有一个模块的代码如下

```TS
const maxInterval = 12;

function getArrayLength(arr) {
  return arr.length;
}

module.exports = {
  getArrayLength,
  maxInterval,
};
```

它的类型声明文件可以写成下面这样

```TS
export function getArrayLength(arr: any[]): number;
export const maxInterval: 12;
```



类型声明文件主要有以下三种来源。

- TypeScript 编译器自动生成，只要使用编译选项`declaration`，编译器就会在编译时自动生成单独的类型声明文件

  在`tsconfig.json`文件里面，打开这个选项

  ```JSOn
  {
    "compilerOptions": {
      "declaration": true
    }
  }
  ```

- TypeScript 内置类型文件，安装 TypeScript 语言时，会同时安装一些内置的类型声明文件，主要是内置的全局对象（JavaScript 语言接口和运行环境 API）的类型声明

- 外部模块的类型声明文件，需要自己安装，如果项目中使用了外部的某个第三方代码库，那么就需要这个库的类型声明文件
