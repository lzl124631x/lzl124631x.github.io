---
layout: post
title:  "Closure in Javascript"
date:   March 26, 2016 9:28 PM
categories: JavaScript
---

本文为一些有关JS闭包的笔记.

# TODO

* [JS核心系列：漫谈JS引擎的运行机制](http://www.cnblogs.com/onepixel/p/5090799.html)
* [Closures - JavaScript | MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Closures)


# [干货分享：让你分分钟学会 javascript 闭包](http://www.cnblogs.com/onepixel/p/5062456.html)

闭包的定义: 当一个内部函数被其外部函数之外的变量引用时，就形成了一个闭包。

javascript中的GC机制:在javascript中，如果一个对象不再被引用，那么这个对象就会被GC回收，否则这个对象一直会保存在内存中。

B定义在A中，因此B依赖于A,而外部变量 c 又引用了B, 所以A间接的被 c 引用，也就是说，A不会被GC回收，会一直保存在内存中

```
function A(){
    var count = 0;
    function B(){
       count ++;
       console.log(count);
    }
    return B;
}
var c = A();
c();// 1
c();// 2
c();// 3
```

要深入了解闭包, 还需要了解执行环境(execution context)、活动对象(activation object)以及作用域(scope)和作用域链(scope chain)的运行机制

# [Javascript Closures](http://www.jibbering.com/faq/notes/closures/)

# 引言
> Closure
A "closure" is an expression (typically a function) that can have free variables together with an environment that binds those variables (that "closes" the expression).

闭包(Closure)是一个表达式(通常是函数), 它有着一些自由变量以及一个绑定了这些变量的环境 (该环境"封闭"了这些表达式)

闭包是ECMAScript(JavaScript)最重要的特性之一. 想要合理利用它, 不透彻理解它是不行的. 然而, 创建闭包却是很容易的(虽然有时是无意的), 而且可能会带来一些有害的作用, 特别是在一些常用的浏览器环境中. 要避免无意间触及闭包的害处, 或者要有效利用闭包的好处, 都需要你理解闭包的机制, 而这又要求你先要了解scope chain(作用域链)在标识符解析(indentifier resolution)中的作用, 以及对象属性名的解析过程.

一个关于闭包的简单解释是: ECMAScript允许内函数(innner function), 即位于其他函数内部的函数定义或函数表达式. 内函数能够访问所有在外层函数中的局部变量, 形式参数, 以及其他内函数. 当一个内函数在它所属的外层函数之外被引用的时候, 一个闭包就形成了, 这使得即使在外层函数返回之后, 内函数仍然能够被执行, 并且能够访问它所属外层函数的局部变量, 形式参数以及其他内函数. 这些局部变量, 形式参数, 和内函数的值(一开始)就是它们在外函数返回时所具有的值, 而之后可能被内函数修改.

要正确地理解闭包就必须理解它背后的机制, 以及不少的技术细节. 下面的讲解中已经尽量省略了一些ECMA 262中的算法细节, 但是还是有很多内容是无法被省略或简化的. 如果你对对象属性名解析很熟悉的话, 可以跳过那个章节. 否则, 你就认认真真读完这些章节, 然后去尝试利用闭包吧!

# 对象的属性名解析

ECMAScript将对象分为两类, "Native Object(本地对象)"和"Host Object(宿主对象)". Native对象还包含一个子类, 叫做"Built-in Object(内建对象)" (ECMA 262 第3版 章节4.3). Native对象源自语言本身, 而Host对象则源自运行环境, 比方说, `document`对象, DOM节点等等.

Native对象松散, 动态地包装着一些有名属性 (Built-in对象的某些实现并不那么动态, 不过这关系不大). 对象的一个被定义的有名属性可以保存一个值, 该值可能是另一个对象的引用(函数是一种对象), 或者是一个原始值, 即String(字符串), Number(数), Boolean(布尔值), Null(空)或Undefined(未定义)中的一种. 原始值中, Undefined是比较特殊的一个: 对象的某个属性并不会因为被赋值了Undefined而被删除, 它将仍然是一个已经定义了的有名属性, 只不过它的值是`undefined`罢了.

下面简单地讲解了属性值是如何被读取和写入的. 内部细节已经尽可能地被略过了.

## 赋值

通过向对象的属性赋值, 可以创建或更新属性.

```js
var objectRef = new Object(); //create a generic javascript object.
```

一个名为"testNumber"的属性可以这样被创建:

```js
objectRef.testNumber = 5;
/* - or:- */
objectRef["testNumber"] = 5;
```

`objectRef`对象在赋值操作之前没有`testNumber`这是个属性, 但是赋值操作之后, 它就有了. 接下来的任何赋值操作都只会更新该属性值, 而不会创建新的属性.

```js
objectRef.testNumber = 8;
/* - or:- */
objectRef["testNumber"] = 8;
```

JavaScript对象有一个叫做`prototype`的属性, 它本身就可以是一个对象, 也可以有自己的属性. 但是, `prototype`在属性赋值中不扮演任何角色.

总结一下, 如果对一个对象所不具有的属性赋值, 就对该对象创建一个新的属性并赋值. 如果该属性已经存在, 则会更新该属性值.

## 读值

如果访问对象已有的属性, 则该属性的值会被返回:

```js
/* Assign a value to a named property. If the object does not have a
   property with the corresponding name prior to the assignment it
   will have one after it:-
*/
objectRef.testNumber = 8;

/* Read the value back from the property:- */

var val = objectRef.testNumber;
/* and  - val - now holds the value 8 that was just assigned to the
   named property of the object. */
```

所有对象都可能有`prototype`, `prototype`本身是对象, 所以它们本身又可能有`prototype`, 依次类推, 就形成了prototype chain (原型链). 原型链以一个有着null prototype (空原型)的对象作为结尾标志. `Object`构造函数的默认`prototype`有着null prototype, 即`Object.prototype.prototype === undefined`.

```js
var objectRef = new Object(); //create a generic javascript object.
```

创建一个对象`objectRef`, 它的`prototype`是`Object.prototype`.

(译者注: 注意`objectRef.prototype === undefined`; 获取对象的原型不是通过`obj.prototype`获取, 而是通过`constructor.prototype`获取的, 上例中就是`Object.prototype`.)

而`Object.prototype`又有着null prototype. 所以`objectRef`的原型链只有一个对象, 即`Object.prototype`.

```js
/* A "constructor" function for creating objects of a -
   MyObject1 - type.
*/
function MyObject1(formalParameter){
    /* Give the constructed object a property called - testNumber - and
       assign it the value passed to the constructor as its first
       argument:-
    */
    this.testNumber = formalParameter;
}

/* A "constructor" function for creating objects of a -
   MyObject2 - type:-
*/
function MyObject2(formalParameter){
   /* Give the constructed object a property called - testString -
      and assign it the value passed to the constructor as its first
      argument:-
    */
    this.testString = formalParameter;
}

/* The next operation replaces the default prototype associated with
   all MyObject2 instances with an instance of MyObject1, passing the
   argument - 8 - to the MyObject1 constructor so that its -
   testNumber - property will be set to that value:-
*/
var obj1 = MyObject2.prototype = new MyObject1( 8 );

/* Finally, create an instance of - MyObject2 - and assign a reference
   to that object to the variable - objectRef - passing a string as the
   first argument for the constructor:-
*/

var obj2 = new MyObject2( "String_Value" );
```

`objectRef`所引用的`MyObject2`实例有这样一个原型链: 第一个对象是`MyObject1`的实例`obj1`, 它被创建并赋值到`MyObject2`构造函数的`prototype`属性上 (`obj1.isPrototypeOf(obj2)`). `obj1`本身又有`prototype`, 即由实现赋值给`MyObject1`构造函数的`prototype`. 这个`prototype`又有自己的`prototype`, 即`Object.prototype`. 因为`Object.prototype`有null prototype, 所以原型链至此到达了结尾.

当访问`obj2`的属性时, 整个原型链都会参与到这个过程中来. 最简单的情况:

```js
var val = obj2.testString;
```

`obj2`所引用的`MyObject2`实例有一个属性名为`testString`, 所以该属性的值`"String_Value"`就被赋值到变量`val`. 但是:

```js
var val = obj2.testNumber;
```

`obj2`本身没有`testNumber`这个属性但是`val`仍然被赋值为`8`而不是`undefined`, 这是因为由于在`obj2`上找不到`testNumber`属性, js解释器便到`obj2`的`prototype`上查找. 这个`prototype`就是`MyObject1`的实例`obj1`, 而`obj1`有值为8的`testNumber`属性, 因此`obj2.testNumber`的值就是8. `MyObject1`和`MyObject2`都没有定义`toString`属性, 但是如果尝试访问`obj2`的`toString`属性:

```js
var val = obj2.toString;
```

`val`变量会被赋值为一个函数的引用, 即`Object.prototype`的`toString`属性. 这是因为, `obj2`没有`toString`属性, 其`prototype`--`obj1`--也没有`toString`属性, 而`obj1`的`prototype`--`Object.prototype`有`toString`属性, 于是该函数会被赋值到`val`上.

最后:

```js
var val = obj2.madeUpProperty;
```

会返回`undefined`, 因为在原型链上逐层向上查找, 直至`Object.prototype`都没有`madeUpProperty`属性, 最终到达null prototype结束, 于是返回`undefined`.

在对象及其原型链上读取属性时, 会在第一次找到对应属性时停止. 而为对象的某个属性赋值时, 若该对象没有对应的属性, 则会在该对象上创建一个属性并赋值, 这一过程与原型链无关.

这意味着, 如果进行`obj2.testNumber = 3`的赋值, 会在`obj2`上创建一个名为`testNumber`的属性, 接下来读取`testNumber`都会直接读取`obje2`上的`testNumber`属性. 原型链在接下来的读取操作中都不会被检查, `obj1.testNumber`会一直保持为8不变. 对`obj2.testNumber`的赋值, 会屏蔽掉原型链上的对应属性.

注: ECMAScript为内部`Object`类型定义了一个内部属性`[[prototype]]`. 该属性无法直接通过脚本访问, 但是解析属性时一定用到的就是`[[prototype]]`所引用的对象链, 即原型链. 公共属性`prototype`的存在是为了能够对`[[prototype]]`所引用的原型链进行定义, 赋值和修改. 至于`prototype`和`[[prototype]]`的关系, 在ECMA 262 第三版中可以找到详细解释, 这里不再赘述.

# 标识符解析, 执行上下文和作用域链

## 执行上下文

执行上下文 (Execution Context, 简称EC) 是ECMAScript规范(ECMA 262第三版)中定义的一个抽象属性. 该规范没有规定EC应该如何实现, 但是EC的属性会引用该规范中定义的其他结构, 所以EC会被认为是(甚至它的实现就是)只有私有属性的对象.

所有js代码都执行于EC中. 全局代码(当js文件或HTML页面加载时的行内代码)执行于全局EC中, 而每个函数调用(包括构造函数)都有一个对应的EC. 执行于`eval`函数内的代码也有一个特殊的EC, 但是由于`eval`的使用并不是很常见, 本处不对其进行讨论. 关于EC的详细内容请参考ECMA 262第三版的第10.2章节.

当一个函数被调用的时候, 它会进入EC, 如果此时另一个函数(或者递归函数本身)被调用, 则一个新的EC会被创建, 并且在该函数的执行期间, 代码都会在该EC内部执行, 直到函数执行结束, 程序才会退回到外层的EC中去. 因此, js代码的执行会形成一组EC堆栈.

当EC被创建时, 一系列的事情会按照预定义的顺序被执行. 首先, 在函数的EC中, 一个叫做"活动对象(Activation Object)"的对象会被创建. 它是规范中的另一个机制, 可以把它看作一个对象, 因为它有可以访问的属性, 但它又不是一个普通的对象, 因为它没有`prototype`(至少没有预定义的`prototype`), 并且它不能够直接被js代码所引用.

下一步就是创建一个名为`arguments`的对象, 它是一个数组型对象, 以整数为序号按序索引每一个传入函数的参数. 它还有`length`和`callee`属性(与本文无关, 详见规范文档). 活动对象会创建一个名为`arguments`的属性, 来引用这个`arguments`对象.

然后, 一个作用域(Scope)会被指定给该EC. 每个Scope都是一个对象链. 而函数对象有一个私有的`[[scope]]`属性(稍后详细讨论), 该属性也是一个对象链. 这两个对象链之间的关系是: Scope的对象链由对应函数对象的`[[scope]]`对象链组成的, 并且在该链的首部添加了一个属于该EC的活动对象.



