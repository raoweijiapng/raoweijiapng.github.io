---
title: javascript基本语句
tags: javascript
categories: javascript
---

* TOC
{:toc}

# javascript基本任务

## 定时器

- setInterval()
间隔指定的毫秒数不停地执行指定的代码。
```
  <script>
    //间隔1秒执行myTime（）
    var myVar = setInterval(function(){myTime()},1000);

    function myTime(){
      var d = new Date();
      var t = d.toLocaleTimeString();
      document.getElementById("demo").innerHTML=d;
    }

    //停止计时
    function myStopFunction(){
      clearInterval(myVar);
    }
```


- setTimeout()
在指定的毫秒数后执行指定的代码。
```
  <script>
    var myVar;
    //3秒之后可以执行弹出弹框
    function myFunction(){
      myVar = setTimeout(function(){alert("hello")},3000);
    }

    //必须在定时器开启之前3秒内关闭
    function myStopFunction(){
      clearTimeout(myVar);
    }
  </script>
```

## 对象
在JavaScript中，几乎所有的事物都是对象，对象也是一个变量，但对象可以包含多个值（多个变量）。

- 可以使用字符来定义和创建JavaScript对象。
```
  <script>
    var person = {
      firstName:"jhon",
      lastName:"Doe",
      age:50,
      eyeColor:"blue",
      //定义了一个函数，并作为对象的属性
      fullName:function(){
        return this.firstName+" "+this.lastName;
      }

    };

    //第一种访问对象属性（person.lastName）
    document.getElementById("demo").innerHTML=person.firstName+"现在"+person.age+"岁"；
    
    //第二种访问对象属性（person["lastName"]）
    document.getElementById("demo").innerHTML=person["firstName"]+"现在"+person["age"]+"岁"；
   
    //不加括号直接输出函数表达式作为字符串
    document.getElementById("demo1").innerHTML = person.fullName;
    //加括号输出函数执行结果
    document.getElementById("demo2").innerHTML = person.fullName();
  <script>
```

## 改变css
JavaScript可以改变HTML元素的样式。
```
  <p id="p1">hello world!</p>
  <p id="p2">hello word!</p>
  <script>
    document.getElementById("p2").style.color="blue";
    document.getElementById("p2").style.fontFamily="Arial";
    document.getElementById("p2").style.fontSize="larger";
  </script>
```

## 弹窗

- 警告框
window.alert()方法可以不带上window对象，直接使用alert()方法。
只能点击确定。
```
  <script>
    function myFunction(){
      //加\n让提示语句换行
      alert("你好\n我是一个警告框！")；
    }
  </script>
  <input tupe="button" onclick="myFuction()" value="显示警告框"/>
```

- 确认框
window.confirm()方法可以不带上window对象，直接使用confirm()方法。
可以点击确定或取消。
```
  <p>点击按钮，显示确认框。</p>
  <button onclick="myFunction()">点我</button>
  <script>
    function myFunction(){
      var x;
      var r=confirm("按下按钮!");
      if (r==true){
        x="你按下了\"确定\"按钮!";
      }
      else{
        x="你按下了\"取消\"按钮!";
      }
      document.getElementById("demo").innerHTML=x;
    }
  </script>
```

- 提示框
window.prompt()方法可以不带上window对象，直接使用prompt()方法。
当提示框出现后，用户需要输入某个值，然后点击确认或取消按钮才能继续操纵。
```
  <p>点击按钮查看输入的对话框。</p>
  <button onclick="myFunction()">点我</button>
  <p id="demo"></p>
  <script>
    function myFunction(){
      var x;
      //prompt(提示语句，默认值)，返回输入的值
      var person = prompt("请输入你的名字"，"Harry potter")；
      if(person!=null && person!=""){
        x="你好"+person+"!今天感觉如何？"；
        document.getElementById("demo").innerHTML=x;
      }
    }
  </script>
```

## 流程控制
- 条件语句

  1. if
  ```
    if(condition){
      当condition为true时执行的代码
    }
  ```

  2. if...else
  ```
    if (condition){
      当condition为true时执行的代码
    }
    else{
      当condition不为true时执行的代码
    }
  ```

  3. if...else if...else
  ```
    if (condition1){
      当condition1为true时执行的代码
    }
    else if (condition2){
      当condition2为true时执行的代码
    }
    else{
      当condition1和condition2都不为true时执行的代码
    }
  ```

- switch
```
  switch(n){
    case 1:
        执行代码块1
        break;
    case 2:
        执行代码块2
        break;
    default:
        与case1和case2不同时执行的代码
  }
```

## 循环
- for
```
  for(语句1; 语句2; 语句3){
    被执行的代码块
  }

  语句1 （代码块）开始前执行。
  语句2 定义运行循环（代码块）的条件。
  语句3 在循环（代码块）已被执行之后执行。
```

- for/in
```
  循环遍历对象 "person" 的属性:
  var x;
  var txt="";
  var person={fname:"John",lname:"Doe",age:25}; 
 
  for (x in person){  // x为属性名
    txt=txt + person[x];
  }
  document.getElementById("demo").innerHTML=txt;
```

- while
while循环会在指定条件为真时循环执行代码块。
```
  while (条件){
    需要执行的代码
  }
```

- do while
do/while 循环是while循环的变体。该循环会在检查条件是否为真之前执行一次代码块，然后如果条件为真的话，就会重复这个循环。
```
  do{
      需要执行的代码
  }
  while (条件);
```