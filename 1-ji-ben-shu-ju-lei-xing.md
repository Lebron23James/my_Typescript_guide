> ## 简介

TypeScript 除了支持JavaScript 中所有的数据类型之外，还提供了使用的枚举类型。

---

1、布尔值

在TypeScript 和 JavaScript中都叫做 **boolean** ，值为true 或 false；

```
let isDone:boolean = false;
```



2、数字

在TypeScript 和 JavaScript中所有的数字都是浮点数，这些浮点数的类型是** number**。

除了支持十进制和十六进制字面量，TypeScript中还支持 ES6中引入的二进制和八进制字面量。

```
let decLiteral: number = 6;
let hexLiteral: number = 0xf00d;
let binaryLiteral: number = 0b1010;
let octalLiteral: number = 0o774;
```



3、字符串

在TypeScript 和 JavaScript中所有文本数据类型用**string **表示；使用单引号（‘’）或者双引号（“”）来表示。

```
let name:string = "Bob";
name = "smith";
```

还可以使用模板字符串

