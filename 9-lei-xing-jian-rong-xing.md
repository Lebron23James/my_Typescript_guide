> ## 简介

TypeScript 里的类型兼容性是基于**结构子类型**的。

结构类型是一种只使用其成员来描述类型的方式。它正好与名义类型形成对比。

在基于名义类型的类型系统中，数据类型的兼容性或等价性是通过明确的声明和/或类型的名称来决定的。

---

## 1、基础

```js
interface Named {
    name: string;
}

class Person {
    name: string;
}

let p: Named;
// OK, because of structural typing
p = new Person();
```

使用基于名义类型的语言，以上代码就回报错， 因为Person类没有明确说明其实现了Named 接口。

因为js里广泛地使用匿名对象，如函数表达式和对象字面量；TypeScript的结构性子类型是根据JavaScript代码的典型写法来设计的。

**--- 关于可靠性---**

TypeScript的类型系统允许某些在编译阶段无法确认其安全性的操作。当一个类型系统具此属性时，被当做是“不可靠”的。

## 2、开始

TypeScript结构化类型系统的基本规则：如果x要兼容y，那么y至少具有与x相同的属性。

```js
interface Named {
    name: string;
}

let x: Named;

let y = { name: 'Alice', location: 'Seattle' }; // y's inferred type is { name: string; location: string; }

x = y;
```

编译器会检查x中的每个属性，看能否在y 中找到对应的属性。

以上y是否能赋值给x；检查y必须要包含名字是name 的string 类型成员---- 满足条件，所以赋值正确。

---

检查函数参数时，使用相同的规则：

```js
function greet(n: Named) {
    alert('Hello, ' + n.name);
}
greet(y); // OK
```

`y`有个额外的`location`属性，但这不会引发错误。 只有目标类型（这里是`Named`）的成员会被一一检查是否兼容。

这个比较过程是递归进行的，检查每个成员及子成员！！！

## 3、比较两个函数

#### ①函数的参数列表类型不同：

```js
let x = (a: number) => 0;
let y = (b: number, s: string) => 0;

y = x; // OK
x = y; // Error
```

（1）检查x 是否能赋值给y：x的每个参数必须能在y 里找到对应类型的参数。【参数名称相同与否无所谓，只看参数类型】

（2）检查y 是否能赋值给x：y有必须的第二个参数，x没有，所以不允许赋值。

---

如上例子（1）中 y = x ，允许忽略参数， 因为额外的参数在JavaScript中是很常见的。

```js
let items = [1, 2, 3];

// Don't force these extra arguments
items.forEach((item, index, array) => console.log(item));

// Should be OK!
items.forEach((item) => console.log(item));
```

---

#### ② 对于函数返回值类型不同的处理：

```js
let x = () => ({name: 'Alice'});
let y = () => ({name: 'Alice', location: 'Seattle'});

x = y; // OK
y = x; // Error because x() lacks a location property
```

类型系统强制要求：** 源函数的返回值类型必须是目标函数返回值类型的子类型。**

#### ③函数参数的双向协变

#### ④可选参数及剩余参数

比较函数兼容性的时候，可选参数与必须参数是可互换的。

源类型上有额外的可选参数不是错误，目标类型的可选参数在源类型里没有对应的参数也不是错误。

当一个函数有剩余参数的 时候，它被当做无限个可选参数。

如下例子，常见函数接受一个回调函数，并用参数（对程序员来说可预知，对类型系统来说不确定）来调用：

```js
function invokeLater(args: any[], callback: (...args: any[]) => void) {
    /* ... Invoke callback with 'args' ... */
}

// Unsound - invokeLater "might" provide any number of arguments
invokeLater([1, 2], (x, y) => console.log(x + ', ' + y));

// Confusing (x and y are actually required) and undiscoverable
invokeLater([1, 2], (x?, y?) => console.log(x + ', ' + y));
```

## 4、枚举

枚举类型与数字类型兼容，且数字类型与枚举类型兼容； 但是不同枚举类型之间是不兼容的。

```js
enum Status { Ready, Waiting };
enum Color { Red, Blue, Green };

let status = Status.Ready;
status = Color.Green;  //error
```

## 5、类

类有静态部分 和 实例部分的类型。比较两个类 类型的对象时，只有**实例的成员**会被比较，静态成员和构造函数不在比较的范围内。

```js
class Animal {
    feet: number;
    constructor(name: string, numFeet: number) { }
}

class Size {
    feet: number;
    constructor(numFeet: number) { }
}

let a: Animal;
let s: Size;

a = s;  //OK
s = a;  //OK
```

#### 类的私有成员

类的私有成员会影响兼容性的判断：

```
      当类的实例用来检查兼容时，如果目标类型包含一个私有成员，那么源类型必须包含来自同一个类的这个私有成员。

      这样允许子类赋值给父类，但是不能赋值给其他的类（即使这个类有相同的类型）。
```

## 6、泛型

因为TypeScript是结构性的类型系统，类型参数只影响-- 使用其作为类型一部分的结果类型。

```js
interface Empty<T> {
}
let x: Empty<number>;
let y: Empty<string>;

x = y;  // okay, y matches structure of x
```

以上代码的x 和 y  是兼容的，因为它们的结构使用类型参数时并没有什么不同。

---

```js
interface NotEmpty<T> {
    data: T;
}
let x: NotEmpty<number>;
let y: NotEmpty<string>;

x = y;  // error, x and y are not compatible
```

以上代码，泛型类型在使用时就好比不是一个泛型类型

---

对于没有指定泛型类型的泛型参数时，会把所有泛型参数当做  any 比较。 然后用结果类型进行比较。

```js
let identity = function<T>(x: T): T {
    // ...
}

let reverse = function<U>(y: U): U {
    // ...
}

identity = reverse;  // Okay because (x: any)=>any matches (y: any)=>any
```

## 7、高级主题

在TypeScript 中，有两种类型的兼容性： **子类型** 与** 赋值**。

他们的不同点在于： 赋值扩展了子类型的兼容，允许给any 赋值或从any 取值；允许数字赋值给枚举类型或枚举类型赋值给数字。

---

实际上，类型兼容性是由赋值兼容性来控制的，即使在implements 和 extends 语句中不例外。

