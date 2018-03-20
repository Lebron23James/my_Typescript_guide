> ## 简介

传统的JavaScript 是通过函数 和 基于原型的继承来创建可重用的组件。

ES6 能够使用基于类的面向对象的方式。

TypeScript 允许使用类的特性，并且编译后的js 可以在所有主流浏览器和平台上运行。

---

## 1、类的定义

```
class Greeter {
    greeting: string;                        //属性greeting
    
    constructor (message: string) {          //构造函数
        this.greeting = message;
    }
    greet () {                               //方法greet
        return "Hello," + this.greeting;
    }
}

let greeter = new Greeter("world");          //创建了Greeter类的一个实例
```

引用类成员的时候会用this； this表示的我们访问的是类的成员。

new关键字调用之前定义的构造函数，并执行构造函数创建一个Greeter类型的新对象。



## 2、类的继承

基于类的程序设计中一种最基本的模式：允许使用继承扩展现有的类。



