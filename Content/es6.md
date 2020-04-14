# ES6学习笔记

## 1. var和let

这里面有很多细节，一不小心容易出错，具体参考： https://es6.ruanyifeng.com/#docs/let

1. var是函数作用域

```javascript

// 输出一次abc   原因是：函数作用域，var i = "abc"执行完毕之后，i++就变成了 abc++，abc++ < 3 判断为false，故不会继续执行循环体了
for (var i = 0; i < 3; i++) {
  var i = 'abc';
  console.log(i);
}

```

2. let是块作用域

```javascript

// 输出3次abc   原因是：abc被限制的for循环的花括弧里（块作用域里），不影响for循环的条件
for (let i = 0; i < 3; i++) {
  let i = 'abc';
  console.log(i);
}


// Error: Cannot access 'i' before initialization   原因是：console.log(i)中的i尚未定义，它和循环条件里的i不是一回事
for (let i = 0; i < 3; i++) {
  console.log(i);
  let i = 'abc';
}

```

3. 变量提升

对于var声明的变量来说，变量的声明都将被提升到函数的最顶部。

以下两段代码效果一致，运行的结构都是y undefined，第一段代码中的 (var y = 7;) 其实相当于两行代码，一个是声明(var y;)，一个是初始化(y = 7)。

(var y;) 会被提升到函数顶部，但是初始化不会提升。


```javascript

var x = 5; // 初始化 x

elem = document.getElementById("demo"); // 查找元素
elem.innerHTML = x + " " + y;           // 显示 x 和 y

var y = 7; // 初始化 y


```


```javascript

var y;
var x = 5; // 初始化 x

elem = document.getElementById("demo"); // 查找元素
elem.innerHTML = x + " " + y;           // 显示 x 和 y

y = 7; // 初始化 y


```

如果上面两段代码可以理解，那么也就比较好理解下面这段代码了。

```javascript

var tmp = new Date();

function f() {
  console.log(tmp);  // 调用f()函数，会在这里打印出undefined
  if (false) {
    var tmp = 'hello world';
  }
}

f(); // undefined


```

上面的这段代码可以写成：


```javascript

var tmp = new Date();

function f() {
  var tmp;
  console.log(tmp);  // 调用f()函数，会在这里打印出undefined
  if (false) {
    tmp = 'hello world';
  }
}

f(); // undefined


```


4. 用法区别

 * var可以先使用后声明，只不过未声明之前，是undefined

```javascript

console.log(a); //undefined
var a = 1;

```

 * let不可以，使用之前必须进行声明，否则会报错

 ```javascript

console.log(a); // Error: Cannot access 'a' before initialization
let a = 1;

 ```



 ## 2. 块级作用域和函数声明

一、ES5 规定，函数只能在顶层作用域和函数作用域之中声明，不能在块级作用域声明。但是，浏览器没有遵守这个规定，为了兼容以前的旧代码，还是支持在块级作用域之中声明函数，

二、ES6 引入了块级作用域，明确允许在块级作用域之中声明函数。ES6 规定，块级作用域之中，函数声明语句的行为类似于let，在块级作用域之外不可引用。但是实际上ES6允许浏览器不遵守这样的规则，允许浏览器：

1) 允许在块级作用域内声明函数。
2) 函数声明类似于var，即会提升到全局作用域或函数作用域的头部。
3) 同时，函数声明还会提升到所在的块级作用域的头部。


所以基于ES6的规则，在遵循ES6的浏览器中，下面两段代码的效果是一致的：

```javascript

// 浏览器的 ES6 环境
function f() { console.log('I am outside!'); }

(function () {
  if (false) {
    // 重复声明一次函数f
    function f() { console.log('I am inside!'); }
  }

  f();  // Error: f is not a function
}());


```


```javascript

// 浏览器的 ES6 环境
function f() { console.log('I am outside!'); }
(function () {
  var f = undefined;
  if (false) {
    function f() { console.log('I am inside!'); }
  }

  f(); // Error: f is not a function
}());

```


考虑到环境导致的行为差异太大，应该避免在块级作用域内声明函数。如果确实需要，也应该写成函数表达式，而不是函数声明语句。

```javascript

// 块级作用域内部的函数声明语句，建议不要使用
{
  let a = 'secret';
  function f() {
    return a;
  }
}

// 块级作用域内部，优先使用函数表达式
{
  let a = 'secret';
  let f = function () {
    return a;
  };
}

```


总结：
1. 避免在块级作用域内声明函数。如果确实需要，也应该写成函数表达式，而不是函数声明语句。
2. ES6的块级作用域必须有大括号，如果没有大括号，JavaScript引擎就认为不存在块级作用域，变量和函数都是一样。


## 3. const命令

1. const的作用域与let一致；
2. const用于声明一个常量，必须在声明的时候就进行赋值；
3. 跟let一样，不提升；
4. 在同一个作用域下，不可重复声明。


## 4. ES6变量声明方法

1. var 
2. function
3. let 
4. const
5. import 
6. class


## 5. ES6中的顶层对象

1. var命令和function命令声明的全局变量，依旧是顶层对象的属性；
2. let命令、const命令、class命令声明的全局变量，不属于顶层对象的属性。


```javascript

var a = 1;
// 如果在 Node 的 REPL 环境，可以写成 global.a
// 或者采用通用方法，写成 this.a
window.a // 1

let b = 1;
window.b // undefined


```

## 6. ES2020顶层对象 globalThis



