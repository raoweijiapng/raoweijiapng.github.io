---
layout: post
title: ECMAScript6
tags: ECMAScript6
categories: ECMAScript6
---

* TOC
{:toc}

# ECMAScript6

- JavaScript-let

let 语句允许您使用块作用域声明变量。

```
  var x = 10;
  // Here x is 10
  { 
    let x = 2;
    // Here x is 2
  }
  // Here x is 10
```

- JavaScript-const

const 语句允许您声明常量（具有常量值的 JavaScript 变量）。常量类似于let变量，但不能更改值。

```
var x = 10;
// Here x is 10
{ 
  const x = 2;
  // Here x is 2
}
// Here x is 10
```

- 指数运算符

取幂运算符（ ** ）将第一个操作数提升到第二个操作数的幂。

```
  var x = 5;
  var z = x ** 2;          // 结果是 25
```

- x ** y 的结果与 Math.pow(x,y) 相同：

```
  var x = 5;
  var z = Math.pow(x,2);   // 结果是 25
```

- 默认参数值

ES6允许函数参数具有默认值。

```
  function myFunction(x, y = 10) {
    // y is 10 if not passed or undefined
    return x + y;
  }
  myFunction(5); // 将返回 15
```

- Array.find()

find()方法返回通过测试函数的第一个数组元素的值。此例查找（返回）第一个大于 18 的元素（的值）：

```
  var numbers = [4, 9, 16, 25, 29];
  var first = numbers.find(myFunction);

  function myFunction(value, index, array) {
    return value > 18;
  }
  请注意此函数接受 3 个参数：

  项目值
  项目索引
  数组本身
```

- Array.findIndex()

findIndex() 方法返回通过测试函数的第一个数组元素的索引。此例确定大于18的第一个元素的索引：

```
  var numbers = [4, 9, 16, 25, 29];
  var first = numbers.findIndex(myFunction);

  function myFunction(value, index, array) {
    return value > 18;
  }
```

- 新的数字属性

ES6将以下属性添加到Number对象：EPSILON、MIN_SAFE_INTEGER、MAX_SAFE_INTEGER

```
  var x = Number.EPSILON;
  var x = Number.MIN_SAFE_INTEGER;
  var x = Number.MAX_SAFE_INTEGER;
```

- 新的数字方法

ES6为Number对象添加了2个新方法：Number.isInteger()、Number.isSafeInteger()

```
  Number.isInteger() 方法
  如果参数是整数，则 Number.isInteger() 方法返回 true。
  Number.isInteger(10);        // 返回 true
  Number.isInteger(10.5);      // 返回 false
```

```
  Number.isSafeInteger() 方法
  安全整数是可以精确表示为双精度数的整数。
  如果参数是安全整数，则 Number.isSafeInteger() 方法返回 true。
  Number.isSafeInteger(10);    // 返回 true
  Number.isSafeInteger(12345678901234567890);  // 返回 false
```

- 新的全局方法

ES6还增加了2个新的全局数字方法：isFinite()、isNaN()

```
  isFinite() 方法
  如果参数为 Infinity 或 NaN，则全局 isFinite() 方法返回 false。
  否则返回 true：
  isFinite(10/0);       // 返回 false
  isFinite(10/1);       // 返回 true
```

```
  isNaN() 方法
  如果参数是 NaN，则全局 isNaN() 方法返回 true。否则返回 false：
  isNaN("Hello");       // 返回 true
```

- 箭头函数（Arrow Function）

箭头函数允许使用简短的语法来编写函数表达式。您不需要function关键字、return关键字以及花括号。

```
  // ES5
  var x = function(x, y) {
     return x * y;
  }
  // ES6
  const x = (x, y) => x * y;
```


