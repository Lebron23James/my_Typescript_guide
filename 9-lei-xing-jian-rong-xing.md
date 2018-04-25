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

