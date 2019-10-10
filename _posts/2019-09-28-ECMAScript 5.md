---
title: ECMAScript5
tags: ECMAScript5
categories: ECMAScript5
---

* TOC
{:toc}

# ECMAScript5

- "use strict"指令

“use strict”定义JavaScript代码应该以“严格模式”执行。例如，使用严格模式，不能使用未声明的变量。
您可以在所有程序中使用严格模式。它可以帮助您编写更清晰的代码，例如阻止您使用未声明的变量。
“use strict” 只是一个字符串表达式。旧浏览器如果不理解它们就不会抛出错误。

- String.trim()

String.trim()删除字符串两端的空白字符。

```
  var str = "       Hello World!        ";
  alert(str.trim());
```

- Array.isArray()

isArray()方法检查对象是否为数组。

```
  function myFunction() {
    var fruits = ["Banana", "Orange", "Apple", "Mango"];
    var x = document.getElementById("demo");
    x.innerHTML = Array.isArray(fruits);
  }
```

- Array.forEach()

forEach()方法为每个数组元素调用一次函数。

```
  var txt = "";
  var numbers = [45, 4, 9, 16, 25];
  numbers.forEach(myFunction);

  function myFunction(value){
    txt = txt + value + "<br>"; 
  }
```

- Array.map()

这个例子给每个数组值乘以2：

```
  var numbers1 = [45, 4, 9, 16, 25];
  var numbers2 = numbers1.map(myFunction);

  function myFunction(value) {
    return value * 2;
  }
```

- Array.filter()

此例用值大于18的元素创建一个新数组：

```
  var numbers = [45, 4, 9, 16, 25];
  var over18 = numbers.filter(myFunction);
  function myFunction(value) {
    return value > 18;
  }
```

- Array.reduce()

这个例子确定数组中所有数的总和：

```
var numbers1 = [45, 4, 9, 16, 25];
var sum = numbers1.reduce(myFunction);

function myFunction(total, value) {
  return total + value;
}
```

- Array.reduceRight()

这个例子同样是确定数组中所有数的总和：

```
  var numbers1 = [45, 4, 9, 16, 25];
  var sum = numbers1.reduceRight(myFunction);

  function myFunction(total, value) {
    return total + value;
  }
```

- Array.every()

这个例子检查是否所有值都超过18：

```
  var numbers = [45, 4, 9, 16, 25];
  var allOver18 = numbers.every(myFunction);

  function myFunction(value) {
    return value > 18;
  }
```

- Array.some()

这个例子检查某些值是否超过18：

```
  var numbers = [45, 4, 9, 16, 25];
  var allOver18 = numbers.some(myFunction);

  function myFunction(value) {
    return value > 18;
  }
```

- Array.indexOf()

检索数组中的某个元素值并返回其位置：

```
  var fruits = ["Banana", "Orange", "Apple", "Mango"];
  var a = fruits.indexOf("Apple");
```

- JSON.parse()

JSON的一个常见用途是从Web服务器接收数据。想象一下，您从Web服务器收到这条文本字符串：

```
  '{"name":"Bill", "age":62, "city":"Seatle"}'
  JavaScript 函数 JSON.parse() 用于将文本转换为 JavaScript 对象：
  var obj = JSON.parse('{"name":"Bill", "age":62, "city":"Seatle"}');
```

- JSON.stringify()

JSON的一个常见用途是将数据发送到Web服务器。将数据发送到Web服务器时，数据必须是字符串。想象一下，我们在JavaScript中有这个对象：

```
  var obj = {"name":"Bill", "age":62, "city":"Seatle"};
  请使用 JavaScript 函数 JSON.stringify() 将其转换为字符串。

  var myJSON = JSON.stringify(obj);
  结果将是遵循 JSON 表示法的字符串。

  myJSON 现在是一个字符串，准备好发送到服务器：
  var obj = {"name":"Bill", "age":62, "city":"Seatle"};
  var myJSON = JSON.stringify(obj);
  document.getElementById("demo").innerHTML = myJSON;
```

- Date.now()

Date.now()返回自零日期(1970年1月1日 00:00:00:00)以来的毫秒数。

```
  var timInMSs = Date.now();
```


- 属性Getter和Setter

ES5允许您使用类似于获取或设置属性的语法来定义对象方法。这个例子为名为fullName的属性创建一个getter:

```
  // 创建对象：
  var person = {
    firstName: "Bill",
    lastName : "Gates",
    get fullName() {
      return this.firstName + " " + this.lastName;
    }
  };
  // 使用 getter 显示来自对象的数据：
  document.getElementById("demo").innerHTML = person.fullName;
```

这个例子为语言属性创建一个setter和一个getter：

```
  var person = {
    firstName: "Bill",
    lastName : "Gates",
    language : "NO",
    get lang() {
      return this.language;
    },
    set lang(value) {
      this.language = value;
    }
  };
  // 使用 setter 设置对象属性：
  person.lang = "en";
  // 使用 getter 显示来自对象的数据：
  document.getElementById("demo").innerHTML = person.lang;
```

这个例子使用 setter 来确保语言的大写更新：

```
  var person = {
    firstName: "Bill",
    lastName : "Gates",
    language : "NO",
    set lang(value) {
      this.language = value.toUpperCase();
    }
  };
  // 使用 setter 设置对象属性：
  person.lang = "en";
  // 显示来自对象的数据：
  document.getElementById("demo").innerHTML = person.language;
```

- 新的对象属性和方法

Object.defineProperty()是ES5中的新对象方法。它允许您定义对象属性和/或更改属性的值和/或元数据。

```
  // 创建对象：
  var person = {
    firstName: "Bill",
    lastName : "Gates",
    language : "NO", 
  };
  // 更改属性：
  Object.defineProperty(person, "language", {
    value: "EN",
    writable : true,
    enumerable : true,
    configurable : true
  });
  // 枚举属性
  var txt = "";
  for (var x in person) {
    txt += person[x] + "<br>";
  }
  document.getElementById("demo").innerHTML = txt;
```
 
- ECMAScript5为JavaScript添加了许多新的对象方法：

```
  ES5 新的对象方法

  // 添加或更改对象属性
  Object.defineProperty(object, property, descriptor)

  // 添加或更改多个对象属性
  Object.defineProperties(object, descriptors)

  // 访问属性
  Object.getOwnPropertyDescriptor(object, property)

  // 将所有属性作为数组返回
  Object.getOwnPropertyNames(object)

  // 将可枚举属性作为数组返回
  Object.keys(object)

  // 访问原型
  Object.getPrototypeOf(object)

  // 防止向对象添加属性
  Object.preventExtensions(object)

  // 如果可以将属性添加到对象，则返回 true
  Object.isExtensible(object)

  // 防止更改对象属性（而不是值）
  Object.seal(object)

  // 如果对象被密封，则返回 true
  Object.isSealed(object)

  // 防止对对象进行任何更改
  Object.freeze(object)

  // 如果对象被冻结，则返回 true
  Object.isFrozen(object)
```

- 对字符串的属性访问

charAt()方法返回字符串中指定索引(位置)的字符：

```
  var str = "HELLO WORLD";
  str.charAt(0);            // 返回 H
```

- ECMAScript 5允许对字符串进行属性访问：

```
  var str = "HELLO WORLD";
  str[0];                   // 返回 H
```

- 尾随逗号(Trailing Commas)

ECMAScript 5 允许在对象和数组定义中使用尾随逗号：

```
  Object 实例
  person = {
    firstName: "Bill",
    lastName: " Gates",
    age: 62,
  }
  Array 实例
  points = [
    1,
    5,
    10,
    25,
    40,
    100,
  ];
  JSON 不允许使用尾随逗号。
```

- JSON 对象：

```
  // 允许：
  var person = '{"firstName":"Bill", "lastName":"Gates", "age":62}'
  JSON.parse(person)

  // 不允许：
  var person = '{"firstName":"Bill", "lastName":"Gates", "age":62,}'
  JSON.parse(person)
  JSON 数组：
  // 允许：
  points = [40, 100, 1, 5, 25, 10]

  // 不允许：
  points = [40, 100, 1, 5, 25, 10,]
```

- 多行字符串

如果使用反斜杠转义，ECMAScript 5 允许多行的字符串文字（字面量）：

```
  "Hello \
  Kitty!";
```

\ 方法可能没有得到普遍的支持。较旧的浏览器可能会以不同的方式处理反斜杠周围的空格。一些旧的浏览器不允许\字符后面的空格。分解字符串文字的一种更安全的方法是使用字符串添加：

```
  "Hello " + 
  "Kitty!";
```

- 保留字作为属性名称

ECMAScript 5允许保留字作为属性名称：

```
  var obj = {name: "Bill", new: "yes"}
```