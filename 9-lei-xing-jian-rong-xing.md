> ## 简介

TypeScript 里的类型兼容性是基于结构子类型的。

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











