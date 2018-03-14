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

还可以使用模板字符串【定义多行文本和内嵌表达式】；

    let name: string = "janny";
    let age: number = 23;

    let sentence: string = ` Hello, my name is ${name}，
                             I am ${age} years old;  `

4、数组

TypeScript 和 JavaScript一样可以操作数组元素。 有两种方式可以定义数组：

第一种： 在元素类型后面接上\[ \]； 表示有此类型元素组成的一个数组

```
let list:number[] = [1, 2, 3];
```

第二种： 使用数组泛型， **Array&lt;元素类型&gt;**

```
let list: Array<number> = [1, 2, 3];
```

5、元组 **Tuple**

元组类型表示一个一直元素数量和类型数组，其中各元素的类型不必相同。

```
let x: [string, number];   //定义一对值分别为string和number类型的元组
x = ["hello", 10];         //true
x = [10, "hello"];         //false
```

当访问一个已知索引的类型，会得到正确的值。

```
console.log(x[0].substr(1));  // OK
console.log(x[1].substr(1));  // Error  number类型没有substr 方法
```

当访问一个越界元素的时候，回使用联合类型代替。

```
x[3] = "world";  //OK 字符串可以赋值给（string | number）类型
x[5].toString();  //OK String和number都有toString方法
x[6] = true;      //Error, 布尔不是（string | number）类型

```

6、枚举**enum**

枚举类型是对JavaScript标准数据类型的一个补充，可以为一组数值赋予友好的名字。 

