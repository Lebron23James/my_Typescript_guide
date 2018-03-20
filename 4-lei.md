> ## 简介

传统的JavaScript 是通过函数 和 基于原型的继承来创建可重用的组件。

ES6 能够使用基于类的面向对象的方式。

TypeScript 允许使用类的特性，并且编译后的js 可以在所有主流浏览器和平台上运行。

---

## 1、类的定义

```js
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

```js
class Animal {
    move(distanceInMeters: number = 0) {
        console.log(` Animal moved ${distanceInMeters}m `)
    }
}

class Dog extends Animal {
    bark() {
        console.log('woof!!');
    }   
}

const dog = new Dog();
dog.bark();
dog.move(10);
```

以上展示了最基本的类继承，类从基类中继承了属性和方法。

Dog是一个派生类，派生自Animal基类，通过extends关键字； 派生类通常称为**子类**，基类通常称为**超类**。

   

以下例子创建了Animal的两个子类，描述了如何在子类中重写父类的方法：

```js
class Animal {
    name: string;
    constructor(theName: string) { this.name = theName; }
    move(distanceInMeters: number = 0) {
        console.log(`${this.name} moved ${distanceInMeters}m.`);
    }
}

class Snake extends Animal {
    constructor(name: string) { super(name); }
    move(distanceInMeters = 5) {
        console.log("Slithering...");
        super.move(distanceInMeters);
    }
}

class Horse extends Animal {
    constructor(name: string) { super(name); }
    move(distanceInMeters = 45) {
        console.log("Galloping...");
        super.move(distanceInMeters);
    }
}

let sam = new Snake("Sammy the Python");
let tom: Animal = new Horse("Tommy the Palomino");

sam.move();
tom.move(34);
```

派生类中包含一个构造函数，必须调用super\(\) 来执行基类的构造函数。

在构造函数里访问this的属性之前，一定要调用super\(\)；**这是TypeScript强制执行的一条重要规则。**

Snake类 和 Horse类都创建了move方法，重写了从Animal继承来的move方法，使move方法根据不同的类有不同的功能。





