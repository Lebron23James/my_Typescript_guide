> ## 简介

介绍几种TypeScript 中的高级类型

---

## 1、交叉类型（intersection types）

交叉类型是将现有的多个类型叠加到一起合并为一个类型 ，它包含了所需的所有类型的特性。

我们大多数是在混入（mixins）或 其他不适合典型面向对象模型的地方看到交叉类型的使用。

```js
function extend<T, U>(first: T, second: U): T & U {
    let result = <T & U>{};
    for (let id in first) {
        (<any>result)[id] = (<any>first)[id];
    }
    for (let id in second) {
        if (!result.hasOwnProperty(id)) {
            (<any>result)[id] = (<any>second)[id];
        }
    }
    return result;
}

class Person {
    constructor(public name: string) { }
}
interface Loggable {
    log(): void;
}
class ConsoleLogger implements Loggable {
    log() {
        // ...
    }
}
var jim = extend(new Person("Jim"), new ConsoleLogger());
var n = jim.name;
jim.log();
```

## 2、联合类型（Union Types）

联合类型与交叉类型很有关联，但是使用上却完全不同。

例如：一个代码库希望传入number 或string 类型的参数。如下所示：

```js
function padLeft(value: string, padding: any) {
    if (typeof padding === "number") {
        return Array(padding + 1).join(" ") + value;
    }
    if (typeof padding === "string") {
        return padding + value;
    }
    throw new Error(`Expected string or number, got '${padding}'.`);
}

padLeft("Hello world", 4); // returns "    Hello world"
```

以上，padding参数的类型为any，当我们传入一个既不是number，也不是string类型的参数：

```js
let indentedString = padLeft("Hello world", true); // 编译阶段通过，运行时报错
```

---

在传统的面向对象语言里，我们可能会将这两种类型抽象成有层级的类型。 这么做显然是非常清晰的，但同时也存在了过度设计。

padLeft原始版本的好处之一是允许我们传入原始类型。 这样做的话使用起来既简单又方便。 如果我们就是想使用已经存在的函数的话，这种新的方式就不适用了。

---

**联合类型** 表示一个值可以是几种类型之一，我们用竖线分割每种类型：

```js
 function padLeft(value: string, padding: string | number) {  // 这里表示一个值可以为string类型 或者 number类型
    // ...
}

let indentedString = padLeft("Hello world", true); // 编译阶段就回报错
```

---

如果一个值是联合类型，那么我们只能访问此联合类型的所有类型里共有的成员。

```js
interface Bird {
    fly();
    layEggs();
}

interface Fish {
    swim();
    layEggs();
}

function getSmallPet(): Fish | Bird {
    // ...
}

let pet = getSmallPet();
pet.layEggs(); // okay
pet.swim();    // errors
```

## 3、类型保护与区分类型（Type Guards and Differentiating Types）

JavaScript中常用来区分两个值的方法就是检查成员是否存在。

```js
let pet = getSmallPet();

// 每一个成员访问都会报错 --- 因为我们只能访问联合类型中共同拥有的成员。
if (pet.swim) {
    pet.swim();
}
else if (pet.fly) {
    pet.fly();
}
```

我们可以使用类型断言来是这段代码可以工作：

```js
let pet = getSmallPet();

if ((<Fish>pet).swim) {
    (<Fish>pet).swim();
}
else {
    (<Bird>pet).fly();
}
```

> #### 用户自定义的类型保护

想要实现：假若我们一旦检查过类型，就能在之后的每个分支里清楚滴知道pet 的类型。TypeScript里的`类型保护机制`可以实现。

**类型保护**就是一些表达式，它们会在运行时检查以确保在某个作用域里的类型。要定义一个类型保护，我们只要简单的定义一个函数，他的返回值是一个**类型谓词**：（`parameterName is Type 这种格式`）

```js
function isFish(pet: Fish | Bird): pet is Fish {
    return (<Fish>pet).swim !== undefined;
}
```

以上例子中，`pet  is Fish` 就是类型谓词；其中参数名\(pet\)必须来自当前函数签名里的一个参数名。

每当使用一些变量调用isFish 时，TypeScript会将变量缩减为那个具体的类型。只要这个类型与变量的原始类型是兼容的。

```js
// 'swim' 和 'fly' 调用都没有问题了

if (isFish(pet)) {
    pet.swim();
}
else {
    pet.fly();
}
```

TypeScript不仅知道在if 分支里pet 是Fish类型； 它还清楚再else 分支里一定不是Fish 类型，一定是Bird 类型。

> #### typeof 类型保护

现在我们回过头来看看怎么使用联合类型书写`padLeft`代码。 我们可以像下面这样利用类型断言来写：

```js
function isNumber(x: any): x is number {
    return typeof x === "number";
}

function isString(x: any): x is string {
    return typeof x === "string";
}

function padLeft(value: string, padding: string | number) {
    if (isNumber(padding)) {
        return Array(padding + 1).join(" ") + value;
    }
    if (isString(padding)) {
        return padding + value;
    }
    throw new Error(`Expected string or number, got '${padding}'.`);
}
```

但是，以上例子中必须定义一个函数来判断类型是否为原始类型，这非常的不方便；

在TypeScript中我们不必将 typeo x === 'number' 抽象成一个函数； 因为TypeScript可以将它识别为一个类型保护。

```js
function padLeft(value: string, padding: string | number) {
    if (typeof padding === "number") {
        return Array(padding + 1).join(" ") + value;
    }
    if (typeof padding === "string") {
        return padding + value;
    }
    throw new Error(`Expected string or number, got '${padding}'.`);
}
```

以上TypeScript类型保护只有两种形式被识别：`typeof v === "typename"`和`typeof v !== "typename"`

“typename” 必须是 `number`  `string`  `boolean`  `symbol。`

但是TypeScript并不会阻止你与其它字符串比较，语言不会把那些表达式识别为类型保护。

> #### instanceof 类型保护

instanceof 类型保护是通过构造函数来细化类型 一种方式。

```js
interface Padder {
    getPaddingString(): string
}

class SpaceRepeatingPadder implements Padder {
    constructor(private numSpaces: number) { }
    getPaddingString() {
        return Array(this.numSpaces + 1).join(" ");
    }
}

class StringPadder implements Padder {
    constructor(private value: string) { }
    getPaddingString() {
        return this.value;
    }
}

function getRandomPadder() {
    return Math.random() < 0.5 ?
        new SpaceRepeatingPadder(4) :
        new StringPadder("  ");
}

// 类型为SpaceRepeatingPadder | StringPadder
let padder: Padder = getRandomPadder();

if (padder instanceof SpaceRepeatingPadder) {
    padder; // 类型细化为'SpaceRepeatingPadder'
}
if (padder instanceof StringPadder) {
    padder; // 类型细化为'StringPadder'
}
```

instanceof 的右侧要求是一个构造函数，TypeScript 将细化为：

1. 此构造函数的prototype 属性的类型（如果类型不为any）
2. 构造签名所返回的类型的联合

## 4、可以为null 的类型

TypeScript具有两种特殊的类型：null 和 undefined， 它们的值分别为null 和 undefined。

默认情况下，类型检查器认为null 和 undefined 可以赋值任意类型； null和undefined是所有其他类型的一个有效值；

也就是说你阻止不了将它们赋值给其他类型。

`--strictNullChecks`  标记可以解决此问题，也就是说当你声明一个变量的时候，它不会自动的包含null和undefined。

你可以使用联合类型明确的包含null和undefined：

```js
let s = "foo";
s = null; // 错误, 'null'不能赋值给'string'
let sn: string | null = "bar";
sn = null; // 可以

sn = undefined; // error, 'undefined'不能赋值给'string | null'
```

> #### 关于可选参数 和 可选属性

使用了`--strictNullChecks`，可选参数会被自动的加上`|undefined`

```js
function f(x: number, y?: number) {
    return x + (y || 0);
}
f(1, 2);
f(1);
f(1, undefined);
f(1, null); // error, 'null' is not assignable to 'number | undefined'
```

可选属性也会做同样的处理：

```js
class C {
    a: number;
    b?: number;
}
let c = new C();
c.a = 12;
c.a = undefined; // error, 'undefined' is not assignable to 'number'
c.b = 13;
c.b = undefined; // ok
c.b = null; // error, 'null' is not assignable to 'number | undefined'
```

> #### 类型保护和类型断言

由于可以为null 的类型是通过联合类型实现的，你需要使用类型保护来去除 null

```js
function f(sn: string | null): string {
    if (sn == null) {
        return "default";
    }
    else {
        return sn;
    }
}
```

还可以使用短路运算符来去除null：

```js
function f(sn: string | null): string {
    return sn || "default";
}
```

如果编译器不能去除null 和 undefined，你可以使用类型断言手动去除null 和 undefined；

语法为添加`！` 后缀，identifier！从identifier 的类型里去除了null 和 undefined。

```js
function broken(name: string | null): string {
  function postfix(epithet: string) {
    return name.charAt(0) + '.  the ' + epithet; // error, 'name' is possibly null
  }
  name = name || "Bob";
  return postfix("great");
}

function fixed(name: string | null): string {
  function postfix(epithet: string) {
    return name!.charAt(0) + '.  the ' + epithet; // ok
  }
  name = name || "Bob";
  return postfix("great");
}
```

本例使用了嵌套函数，因为编译器无法去除嵌套函数的null（除非是立即调用的函数表达式）。

因为它无法跟踪所有对嵌套函数的调用，尤其是你将内层函数做为外层函数的返回值。

如果无法知道函数在哪里被调用，就无法知道调用时`name`的类型。

## 5、类型别名

**类型别名**就是会给一个类型起个新的名字----来引用这个类型；类型别名不会新建一个类型。

给原始类型起别名通常没有什么用，尽管可以作为文档的一种形式使用。

```js
type Name = string;
type NameResolver = () => string;
type NameOrResolver = Name | NameResolver;
function getName(n: NameOrResolver): Name {
    if (typeof n === 'string') {
        return n;
    }
    else {
        return n();
    }
}
```

**类型别名**有时和`接口`很像--- 可以作用于原始值、联合类型、元组等。

**类型别名**可以是`泛型`： 我们可以添加类型参数，并且在别名声明的右侧传入。

```js
type Container<T> = { value: T };
```

**类型别名**也可以在属性里自己引用自己：

```js
type Tree<T> = {
    value: T;
    left: Tree<T>;
    right: Tree<T>;
}
```

类型别名与交叉类型一起使用，我们可以创造出十分古怪的类型：

```js
type LinkedList<T> = T & { next: LinkedList<T> };

interface Person {
    name: string;
}

var people: LinkedList<Person>;
var s = people.name;
var s = people.next.name;
var s = people.next.next.name;
var s = people.next.next.next.name;
```

然而，类型别名不能出现在声明右侧的任何地方。

```js
type Yikes = Array<Yikes>; // error
```

> #### 接口 vs 类型别名

接口与类型别名之间的差别：

（1） **接口**创建了一个新的名字，可以在其他任何地方使用；**类型别名**并不创建新的名字----- 例如，错误信息就不会使用别名。

如下，在编译器中将鼠标悬停在interfaced 上，显示啊返回的是interface； 但悬停在aliased 上时，现实的却是对象字面量类型。

```js
type Alias = { num: number }
interface Interface {
    num: number;
}
declare function aliased(arg: Alias): Alias;
declare function interfaced(arg: Interface): Interface;
```

（2）另一个重要的区别就是类型别名不能被extends 和 implements （自己也不能extends 和 implements）；

因为[软件中的对象应该对于扩展是开放的，但是对于修改是封闭的](https://en.wikipedia.org/wiki/Open/closed_principle)，你应该尽量去使用接口代替类型别名。

（3）如果你无法通过接口来描述一个类型 且 需要使用联合类型或元组类型；这时通常会使用类型别名。

## 6、字符串字面量类型

字符串字面量类型允许你指定 字符串必须的固定值。

在实际应用中，字符串字面量类型可以与联合类型、类型保护 和 类型别名很好的配合。

通过结合使用这些特性，你可以实现类似枚举类型的字符串。

```js
type Easing = "ease-in" | "ease-out" | "ease-in-out";
class UIElement {
    animate(dx: number, dy: number, easing: Easing) {
        if (easing === "ease-in") {
            // ...
        }
        else if (easing === "ease-out") {
        }
        else if (easing === "ease-in-out") {
        }
        else {
            // error! should not pass null or undefined.
        }
    }
}

let button = new UIElement();
button.animate(0, 0, "ease-in");
button.animate(0, 0, "uneasy"); // error: "uneasy" is not allowed here
```

你只能从三种允许的字符中选择其一来作为参数传递，传入其它值则会产生错误。

字符串字面量还可以用来区分函数重载：

```js
function createElement(tagName: "img"): HTMLImageElement;
function createElement(tagName: "input"): HTMLInputElement;
// ... more overloads ...
function createElement(tagName: string): Element {
    // ... code goes here ...
}
```

## 7、数字字面量类型

TypeScript 还具有数字字面量类型。

```js
function rollDie(): 1 | 2 | 3 | 4 | 5 | 6 {
    // ...
}
```

我们很少直接这样使用，但可以在缩小范围查找bug 的时候使用：

```js
function foo(x: number) {
    if (x !== 1 || x !== 2) {
        //         ~~~~~~~
        // Operator '!==' cannot be applied to types '1' and '2'.
    }
}
```

上面的比较检查是非法的，也就是说：当x与2 进行比较的时候，它的值必须是1 。

## 8、可辨识联合（Discrimimated Unions）

```
在我们谈及“单例类型”的时候，多数是指枚举成员类型和数字/字符串字面量类型，尽管大多数用户会互换使用“单例类型”和“字面量类型”。
```

可合并**单例类型**、**联合类型**、**类型保护** 和 **类型别名**来创建一个叫做 `可辨识联合`的高级模式，或者“`标签联合`”、“`代数数据类型`”

可辨识联合在函数式编程很有用处。

一些语言会自动为你辨识联合；而TypeScript则是基于已有的JavaScript模式，它具有三个要素：

1. 具有普通的单例类型属性 --- 可辨识的特性；
2. 一个类型别名包含了哪些类型的联合 --- 联合；
3. 此属性上的类型保护；

```js
interface Square {
    kind: "square";
    size: number;
}
interface Rectangle {
    kind: "rectangle";
    width: number;
    height: number;
}
interface Circle {
    kind: "circle";
    radius: number;
}
```

首先，我们在上例子声明了将要联合的接口。每个接口都有 kind 属性，但有不同的字面量类型。

**kind** 属性称作 **可辨识的特性 或 标签**； 其他属性则特定于某个接口。

以上的各个接口是没有联系的，下面将它们联合到一起：

```js
typ shape = Square | Rectangle | Circle ;
```

现在我们使用可辨识的联合：

```js
function area(s: Shape) {
    switch (s.kind) {
        case "square": return s.size * s.size;
        case "rectangle": return s.height * s.width;
        case "circle": return Math.PI * s.radius ** 2;
    }
}
```

> #### 完整性检查

当没有涵盖所有可辨识联合的变化时，我们想让编译器可以通知我们。有两种方式可以实现：

比如，如果我们添加了`Triangle`到`Shape`，我们同时还需要更新`area`:

```js
type Shape = Square | Rectangle | Circle | Triangle;
function area(s: Shape) {
    switch (s.kind) {
        case "square": return s.size * s.size;
        case "rectangle": return s.height * s.width;
        case "circle": return Math.PI * s.radius ** 2;
    }
    // should error here - we didn't handle case "triangle"
}
```

&lt;1&gt; 启用 --strictNullChecks 并且指定一个返回值类型。

```js
function area(s: Shape): number { // error: returns number | undefined
    switch (s.kind) {
        case "square": return s.size * s.size;
        case "rectangle": return s.height * s.width;
        case "circle": return Math.PI * s.radius ** 2;
    }
}
```

因为switch 没有包含所有的情况，所以TypeScript 认为这个函数有时候会返回 undefined。

如果你明确的指定了返回值类型为Number ，会得到错误；因为实际的返回值的类型为 number \| undefined。

然而，这种方法存在些微妙之处且`--strictNullChecks`对旧代码支持不好。

&lt;2&gt; 使用 never 类型，编译器用它来进行完整性检查。

```js
function assertNever(x: never): never {
    throw new Error("Unexpected object: " + x);
}
function area(s: Shape) {
    switch (s.kind) {
        case "square": return s.size * s.size;
        case "rectangle": return s.height * s.width;
        case "circle": return Math.PI * s.radius ** 2;
        default: return assertNever(s); // error here if there are missing cases
    }
}
```

assertNever 检查 s 是否为 `never类型` --- 即为除去所有可能情况后剩下的类型。

如果你忘记了某个 case， 那么s 将具有一个真实的类型，并且你会得到一个错误。

## 9、多态的this类型

多态的this类型表示的是：某个包含类或接口的子类型。这被称作F-bounded 多态性。它能很容易的表示连贯接口间的继承。

例如：在计算器的例子中，在每个操作之后返回this类型：

```js
class BasicCalculator {
    public constructor(protected value: number = 0) { }
    public currentValue(): number {
        return this.value;
    }
    public add(operand: number): this {
        this.value += operand;
        return this;
    }
    public multiply(operand: number): this {
        this.value *= operand;
        return this;
    }
    // ... other operations go here ...
}

let v = new BasicCalculator(2)
            .multiply(5)
            .add(1)
            .currentValue();
```

由于这个类使用了this类型，你可以继承它。新的类直接使用之前的方法，不需要做任何的改变。

```js
class ScientificCalculator extends BasicCalculator {
    public constructor(value = 0) {
        super(value);
    }
    public sin() {
        this.value = Math.sin(this.value);
        return this;
    }
    // ... other operations go here ...
}

let v = new ScientificCalculator(2)
        .multiply(5)
        .sin()
        .add(1)
        .currentValue();
```

正因为有this 类型，ScientificCalculator可以在继承BasicCalculator的同时，还保持接口的连贯性。

## 10、索引类型

使用索引类型，编译器就能够 检查使用了动态属性名的代码。

例如：一个常见的JavaScript模式就是 从对象中选取互相的自己。

```js
function pluck(o, names) {
    return names.map(n => o[n])
}
```

下面例子就是在TypeScript中使用此函数，通过 **索引类型** 查询和 **索引访问** 操作符：

```js
function pluck<T, K extends keyof T>(o:T, name:K[]) :T[K][] {
    return names.map(n => 0[n])
} 
interface Person {
    name: string,
    age: number
}
let person: Person{
    name: 'tom',
    age: 18
}

let strings: string[] = pluck(person, ['name']);  // string[]--- 字符串类型的数组:['tom']

// 以上编译器会检查name 是否真的是person 的一个属性。
```

（1）第一个新的类型操作符  `keyof T`**  ： 索引类型查询操作符**。

对于任何类型的T，keyof T 的结果为T 上已知的公共属性名的联合。例如：

```js
let personProps: keyof Person ; // 'name' | 'age'
```

keyof Person 这里是完全可以与 ‘name’ \| 'age' 相互替换的。

不同之处在于，如果你添加新的属性‘address’ 到Peerson；那么keyof Person会自动变为`'name' | 'age' | 'address'`

所以我们在像`pluck`函数这类上下文里使用`keyof`，因为我们并不清楚可能出现的属性名，但编译器会检查是否传入正确属性名：

```js
pluck(person, ['age', 'unknown']); // error, 'unknown' is not in 'name' | 'age'
```

（2）第二个新的类型操作符`T[K]`  ：**索引访问操作符**。

类型语法反映了表达式语法，这就意味着`person['name']`具有类型`Person['name']`--- 在这里就是string类型。

```js
function getProperty<T, K extends keyof T>(o: T, name: K): T[K] {
    return o[name]; // o[name] is of type T[K]
}
```

`getProperty`里的`o: T`和`name: K`，意味着`o[name]: T[K]`。

当你返回`T[K]`的结果，编译器会实例化键的真实类型，因此`getProperty`的返回值类型会随着你需要的属性改变。

```js
let name: string = getProperty(person, 'name');
let age: number = getProperty(person, 'age');
let unknown = getProperty(person, 'unknown'); // error, 'unknown' is not in 'name' | 'age'
```

> #### 索引类型和字符串索引签名

typof 和 T\[K\] 与字符串索引签名进行交互。

如果你有一个带有字符串索引签名的类型，那么`keyof T`会是string。 并且T\[string\] 为索引签名的类型。

```js
interface Map<T> {
    [key: string]: T;
}
let keys: keyof Map<number>; // string
let value: Map<number>['foo']; // number
```

## 11、映射类型

一个常见的任务就是将一个已知的类型每个属性都变为可选的、或者是只读的：

```js
interface PersonPartial {
    name?: string;
    age?: number;
}

interface PersonReadonly {
    readonly name: string;
    readonly age: number;
}
```

TypeScript 提供了一种从旧类型中创建新类型的一种方式 ---** 映射类型**。

```js
type Readonly<T> = {
    readonly [P in keyof T]: T[P];
}
type Partial<T> = {
    [P in keyof T]?: T[P];
}

//然后使用
type PersonPartial = Partial<Person>;
type ReadonlyPerson = Readonly<Person>;
```

下面来分析一下最简单的映射类型和它的组成部分：

```
type Keys = 'option1' | 'option2';
type Flags = { [K in keys]:boolean };
```

以上语法与索引签名的语法类似，内部使用了for...in... 。具有三个部分：

1. 类型变量k ，它会依次绑定到每个属性；
2. 字符串字面量联合的keys ，它包含了要迭代的属性名的集合；
3. 属性的结果类型；

以上例子的keys 是硬编码的属性名的列表，并且属性类型永远是boolean，因此这个映射类型等同于：

```js
type Flags = {
    option1: boolean;
    option2: boolean;
}
```

在真正的应用中，会根据一些已存在的类型、按照一定的方式转换字段。这就是keyof 和 索引访问类型 要做的事情：

```js
type NullablePerson = { [P in keyof Person]: Person[P] | null }
type PartialPerson =  { [P in keyof Person]?:Person[P] }
```

它更有用的地方是可以有一些通用版本：

```js
type Nullable<T> = { [P in keyof T]: T[p] | null }
type Partial<T> =  { [P in keyof T]?:T[p] }
```

在这些例子里，属性列表是`keyof T`且结果类型是`T[P]`的变体。 这是使用通用映射类型的一个好模版。

 因为这类转换是[同态](https://en.wikipedia.org/wiki/Homomorphism)的，映射只作用于`T`的属性而没有其它的。 

编译器知道在添加任何新属性之前可以拷贝所有存在的属性修饰符。

 例如，假设`Person.name`是只读的，那么`Partial<Person>.name`也将是只读的且为可选的。

下面是另一个例子，`T[P]`被包装在`Proxy<T>`类里：

```js
type Proxy<T> = {
    get(): T;
    set(value: T): void;
}
type Proxify<T> = {
    [P in keyof T]: Proxy<T[P]>;
}
function proxify<T>(o: T): Proxify<T> {
   // ... wrap proxies ...
}
let proxyProps = proxify(props);
```

注意`Readonly<T>`和`Partial<T>`用处不小，因此它们与`Pick`和`Record`一同被包含进了TypeScript的标准库里：

```js
type Pick<T, K extends keyof T> = {
    [P in K]: T[P];
}
type Record<K extends string, T> = {
    [P in K]: T;
}
```

`Readonly`，`Partial`和`Pick`是同态的，但`Record`不是。 因为`Record`并不需要输入类型来拷贝属性，所以它不属于同态：

```js
type ThreeStringProps = Record<'prop1' | 'prop2' | 'prop3', string>
```

非同态类型本质上会创建新的属性，因此它们不会从它处拷贝属性修饰符。



