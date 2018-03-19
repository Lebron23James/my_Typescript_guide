> ## 简介

因为TypeScript 是 JavaScript的超集，所以本身就支持 let 和 const。

---

## 1、var声明

作用域规则：var声明可以在包含它的函数、模块、命名空间或全局作用域内部任何位置被访问。

```js
function f(isTrue: boolean) {
   if(isTrue) {
      var x = 10;
   }
   return x;
}

f(true);  // 10
f(false); // undefined
```

变量捕获的怪异：

```js
for (var i = 0; i < 10; i++) {
    setTimeout( function(){ console.log(i)}, 100*i );   //打印 10个 10
}
```

因为我们传给setTimeout的每一个函数表达式，实际上都引用了相同作用域里的同一个i 。

一个常用的解决方法，使用立即执行的函数表达式（IIFE）来捕获每次迭代时的i的值：

```js
for (var i = 0; i < 10; i++) {
    (function(i){
        setTimeout( function(){ console.log(i)}, 100*i ); //打印 10个 10
    })(i)
}
```

每进入一个作用域内，创建一个变量的环境；就算作用域内代码已经执行完毕，这个环境与其捕获的变量仍然存在。

## 2、let声明

块作用域（词法作用域）：在包含它们的块 或 for循环之外是不能访问的。

```js
for (var i = 0; i < 10; i++) {
    setTimeout( function(){ console.log(i)}, 100*i );   //打印 0/1/2/3/4/5/6/7/8/9
}
```

暂时性死区：变量不能在声明之前读 或 写。

```js
a++;  //报错
let a;
```

不能在同一个作用域内多次声明同一个变量。

```js
let s = 10;
let s = 20; // 报错
```

## 3、const声明

拥有与let相同的作用域规则，但是不能对它们重新赋值。

> const实际保证的并不是变量的值不能改动，而是变量指向的那个内存地址不能改动。

## 4、let VS const

最小特权原则：所有变量，除去你计划去修改的都应该使用const。如果一个变量不需要对他写入，那么其他使用这些代码的人也不能够写入它们，并且要思考为什么要对这些变量重新赋值。

const更容易推测数据流的流动。

## 5、解构赋值



