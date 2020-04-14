# ES6学习笔记

## var和let

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