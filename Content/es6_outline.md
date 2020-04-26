# ES6学习笔记

阮一峰： https://es6.ruanyifeng.com/

## 1. ECMAScript 6 简介

1. ECMAScript 和 JavaScript 的关系
2. ES6 与 ECMAScript 2015 的关系
3. 语法提案的批准流程
4. ECMAScript 的历史
5. Babel 转码器

## 2. let 和 const 命令

1. let 命令
2. 块级作用域
3. const 命令
4. 顶层对象的属性
5. globalThis 对象

## 3. 变量的解构赋值

1. 数组的解构赋值
2. 对象的解构赋值
3. 字符串的解构赋值
4. 数值和布尔值的解构赋值
5. 函数参数的解构赋值
6. 圆括号问题
7. 用途

## 4. 字符串的扩展

1. 字符的 Unicode 表示法
2. 字符串的遍历器接口
3. 直接输入 U+2028 和 U+2029
4. JSON.stringify() 的改造
5. 模板字符串
6. 实例：模板编译
7. 标签模板
8. 模板字符串的限制

## 5. 字符串的新增方法

1. String.fromCodePoint()
2. String.raw()
3. 实例方法：codePointAt()
4. 实例方法：normalize()
5. 实例方法：includes(), startsWith(), endsWith()
6. 实例方法：repeat()
7. 实例方法：padStart()，padEnd()
8. 实例方法：trimStart()，trimEnd()
9. 实例方法：matchAll()

## 6. 正则的扩展

1. RegExp 构造函数
2. 字符串的正则方法
3. u 修饰符
4. RegExp.prototype.unicode 属性
5. y 修饰符
6. RegExp.prototype.sticky 属性
7. RegExp.prototype.flags 属性
8. s 修饰符：dotAll 模式
9. 后行断言
10. Unicode 属性类
11. 具名组匹配
12. 正则匹配索引
13. String.prototype.matchAll()

## 7. 数值的扩展

1. 二进制和八进制表示法
2. Number.isFinite(), Number.isNaN()
3. Number.parseInt(), Number.parseFloat()
4. Number.isInteger()
5. Number.EPSILON
6. 安全整数和 Number.isSafeInteger()
7. Math 对象的扩展
8. 指数运算符
9. BigInt 数据类型

## 8. 函数的扩展

1. 函数参数的默认值
2. rest 参数
3. 严格模式
4. name 属性
5. 箭头函数
6. 尾调用优化
7. 函数参数的尾逗号
8. Function.prototype.toString()
9. catch 命令的参数省略

## 9. 数组的扩展

1. 扩展运算符
2. Array.from()
3. Array.of()
4. 数组实例的 copyWithin()
5. 数组实例的 find() 和 findIndex()
6. 数组实例的 fill()
7. 数组实例的 entries()，keys() 和 values()
8. 数组实例的 includes()
9. 数组实例的 flat()，flatMap()
10. 数组的空位
11. Array.prototype.sort() 的排序稳定性

## 10. 对象的扩展

1. 属性的简洁表示法
2. 属性名表达式
3. 方法的 name 属性
4. 属性的可枚举性和遍历
5. super 关键字
6. 对象的扩展运算符
7. 链判断运算符
8. Null 判断运算符

## 11. 对象新增的方法

1. Object.is()
2. Object.assign()
3. Object.getOwnPropertyDescriptors()
4. __proto__属性，Object.setPrototypeOf()，Object.getPrototypeOf()
5. Object.keys()，Object.values()，Object.entries()
6. Object.fromEntries()

## 12. Symbol

1. 概述
2. Symbol.prototype.description
3. 作为属性名的 Symbol
4. 实例：消除魔术字符串
5. 属性名的遍历
6. Symbol.for()，Symbol.keyFor()
7. 实例：模块的 Singleton 模式
8. 内置的 Symbol 值

## 13. Set 和 Map 数据结构

1. Set
2. WeakSet
3. Map
4. WeakMap

## 14. Proxy

1. 概述
2. Proxy 实例的方法
3. Proxy.revocable()
4. this 问题
5. 实例：Web 服务的客户端

## 15. Reflect

1. 概述
2. 静态方法
3. 实例：使用 Proxy 实现观察者模式

## 16. Promise 对象

1. Promise 的含义
2. 基本用法
3. Promise.prototype.then()
4. Promise.prototype.catch()
5. Promise.prototype.finally()
6. Promise.all()
7. Promise.race()
8. Promise.allSettled()
9. Promise.any()
10. Promise.resolve()
11. Promise.reject()
12. 应用
13. Promise.try()

## 17. Iterator 和 for...of 循环

1. Iterator（遍历器）的概念
2. 默认 Iterator 接口
3. 调用 Iterator 接口的场合
4. 字符串的 Iterator 接口
5. Iterator 接口与 Generator 函数
6. 遍历器对象的 return()，throw()
7. for...of 循环

## 18. Generator 函数的语法

1. 简介
2. next 方法的参数
3. for...of 循环
4. Generator.prototype.throw()
5. Generator.prototype.return()
6. next()、throw()、return() 的共同点
7. yield* 表达式
8. 作为对象属性的 Generator 函数
9. Generator 函数的this
10. 含义
11. 应用

## 19. Generator 函数的异步应用

1. 传统方法
2. 基本概念
3. Generator 函数
4. Thunk 函数
5. co 模块

## 20. async 函数

1. 含义
2. 基本用法
3. 语法
4. async 函数的实现原理
5. 与其他异步处理方法的比较
6. 实例：按顺序完成异步操作
7. 顶层 await

## 21. Class 的基本语法

1. 简介
2. 静态方法
3. 实例属性的新写法
4. 静态属性
5. 私有方法和私有属性
6. new.target 属性

## 22. Class的继承

1. 简介
2. Object.getPrototypeOf()
3. super 关键字
4. 类的 prototype 属性和__proto__属性
5. 原生构造函数的继承
6. Mixin 模式的实现

## 23. Module 的语法

1. 概述
2. 严格模式
3. export 命令
4. import 命令
5. 模块的整体加载
6. export default 命令
7. export 与 import 的复合写法
8. 模块的继承
9. 跨模块常量
10. import()

## 24. Module 的加载实现

1. 浏览器加载
2. ES6 模块与 CommonJS 模块的差异
3. Node.js 加载
4. 循环加载

## 25. 编程风格

1. 块级作用域
2. 字符串
3. 解构赋值
4. 对象
5. 数组
6. 函数
7. Map 结构
8. Class
9. 模块
10. ESLint 的使用

## 26. 读懂 ECMAScript 规格

1. 概述
2. 术语
3. 抽象操作的标准流程
4. 相等运算符
5. 数组的空位
6. 数组的 map 方法

# 27. 异步遍历器

1. 同步遍历器的问题
2. 异步遍历的接口
3. for await...of
4. 异步 Generator 函数
5. yield* 语句

## 28. ArrayBuffer

1. ArrayBuffer 对象
2. TypedArray 视图
3. 复合视图
4. DataView 视图
5. 二进制数组的应用
6. SharedArrayBuffer
7. Atomics 对象

## 29. 最新提案

1. do 表达式
2. throw 表达式
3. 函数的部分执行
4. 管道运算符
5. 数值分隔符
6. Math.signbit()
7. 双冒号运算符
8. Realm API
9. #!命令
10. import.meta

## 30. 装饰器

1. 类的装饰
2. 方法的装饰
3. 为什么装饰器不能用于函数？
4. core-decorators.js
5. 使用装饰器实现自动发布事件
6. Mixin
7. Trait