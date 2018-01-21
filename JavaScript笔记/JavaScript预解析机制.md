## JavaScript预解析机制

**网上摘录**

**原作者：DarinZanya**

**原文链接[http://blog.csdn.net/darinzanya/article/details/53067743](http://blog.csdn.net/darinzanya/article/details/53067743)**

**一、原理**

>1. 定义：JavaScript”预解析”，可以理解为把变量或函数预先解析到它们被使用的环境中。
>2. 通俗点讲，即认为浏览器在正式运行JavaScript代码前，

>第一步，会预先根据关键字var、function等，来查找一些需要被解析的东西，
>例如：var a = 1; function test(){ alert(2); }; 

>第二步，给这些需要被解析的东西提前赋值，其中包括：
>⑴ 所有的变量，提前赋值：undefined；
>⑵ 所有的函数，在正式运行代码前，都赋值为整个函数块。

>第三部，“预解析”结束后，浏览器再逐行解读代码，并通过表达式：= + - * -- ++ !等来修改这些“预解析”的值。

**二、解析原则**

>1. JavaScript “预解析”过程中，遇到重名的内容，只保留一个：
>⑴ 重名变量或重名函数，保留最后的一个；
>⑵ 变量和函数重名，只保留函数；
>2. 根据作用域链，来查找上文第一步中提到的需要被解析的东西
>即先找局部作用域里找，然后子对象会一级一级向上寻找所有父对象的变量。

**三、案例分析**

案例一：
```
alert(a); // function a(){alert(4);}
var a=1;
alert(a); // 1
function a(){alert(2);}
alert(a); // 1
var a=3;
alert(a); // 3
function a(){alert(4);}
alert(a); // 3
a(); //报错
```
分析：
>1. 找出需要被解析的东西：a
>2. 预解析赋值：undefined，function a( alert(2); )，undefined，function a( alert(4); )
根据解析原则2，变量与函数重名保留函数，则第一个alert预解析为：function a( alert(4);
>3. 预解析结束，代码开始执行，第一个alert通过表达式赋值，则输出预解析的值，其余均有表达式操作，依次改变为：1，3
>4. 最后的a为一个变量，不能以函数的方式执行

案例二：
```
var a = 1;
function test(){
   alert(a); //undefined 
   var a = 2;
}
test();
alert(a); //1
```
分析：
>通过var关键字在局部作用域里，查找到需要被解析的变量a，赋值undefined。
>这里注意函数的执行顺序，test函数执行时，alert(a)并未有表达式操作，故为undefined。

**个人总结**
>javascript在单线程运作，决定了JavaScript的执行模式是从上向下。
>JavaScript执行代码分成两部分，JavaScript预解析和JavaScript代码执行。
>JavaScript在进行预解析，分为全局作用域下的预解析和函数作用域下的预解析。
>全局作用域下的预解析，根据关键字进行查找 变量 赋值为 undefined，函数赋值为函数本身。
>局部作用域下的预解析，会在函数调用时进行该函数作用域的预解析。类似于全局作用域下的预解析，但是要注意函数的参数实际上是在函数内部声明一个局部变量，该变量名为参数名。
>在执行的过程中，变量的查找根据作用域链向上查找，根据就近原则，如果找到返回该值，如果没找到报错。
**补充**
>使用var声明的变量和函数表达式会被自动提升到作用域的顶部,hoisting。
>当函数传参时，其实是将该函数参数(局部变量)进行从新赋值。
```
var a = 0;
function fn1(a){
   alert(a);      //0
   a = 2;
}
fn1(a);           
alert(a);         //0
```
