#### 1. [块级作用域绑定](#块级作用域绑定)
#### 2. [函数](#函数)
#### 3. [扩展对象的功能性](#扩展对象的功能性)
#### 4. [解构](#解构)

#### 5. [Symbol和Symbol属性](#Symbol和Symbol属性)
#### 6. [Set集合和Map集合](#Set集合和Map集合)
#### 7. [迭代器和生成器](#迭代器和生成器)
#### 8. [JavaScript中的类](#JavaScript中的类)
#### 9. [改进的数组功能](#改进的数组功能)
#### 10. [Promise与异步编程](#Promise与异步编程)
#### 11. [用模块封装代码](#用模块封装代码)


<a id="块级作用域绑定"></a>
# 块级作用域绑定

## var声明及变量提升机制

在ES6之前，在函数作用域中或者全局作用域中通过`var`关键字来声明变量，无论是在代码的哪个位置，这条声明语句都会提到最顶部来执行，这就是变量声明提升。

注意：**只是声明提升，初始化并没有提升。**

看一个例子：

```javascript
function getStudent(name){
  if(name){
    var age=25;
  }else{
    console.log("name不存在");      
  }
  console.log(age); //undefined
}
```

如果按照预想的代码的执行顺序，当`name`有值时才会创建变量`age`，可是执行代码发现，即使不传入`name`，判断语句外的输出语句并没有报错，而是输出`undefined`。

这就是变量声明提升。

## 块级声明

ES6前是没有块级作用域的，比如`{}`外可以访问内部的变量。

### let声明

- 声明变量
- 作用域限制在当前代码块
- 声明不会提升
- 禁止重声明（同一作用域不行，可以覆盖外部同名变量）

```javascript
function getStudent(name){
  if(name){
    let age=25;
    console.log(age); //25
  }else{
    console.log("name不存在");      
  }
  console.log(age); //age is not defined
}
```

和上文一样的代码，只是将`age`的命名关键字从`var`改成了`let`，在执行`getStudent()`和`getStudent("axuebin")`时都会报错。

原因：

- 在if语句内部执行之后，`age`变量将立即被销毁
- 如果`name`为空，则永远都不会创建`age`变量

### const声明

- 声明常量
- 必须初始化
- 不可更改
- 作用域限制在当前代码块
- 声明不会提升
- 禁止重声明（同一作用域不行，可以覆盖外部同名变量）

如果用`const`来声明对象，则**对象中**的值可以修改。

### 临时死区（Temporal Dead Zone）

JavaScript引擎在扫面代码发现声明变量时，遇到`var`则提升到作用域顶部，遇到`let`和`const`则放到TDZ中。当执行了变量声明语句后，TDZ中的变量才能正常访问。

## 循环中的块作用域绑定

我们经常使用for循环：

```javascript
for(var i=0;i<10;i++){
  console.log(i); //0,1,2,3,4,5,6,7,8,9
}
console.log(i) //10
```

发现了什么？

在for循环执行后，我们仍然可以访问到变量`i`。

So easy ~ 把`var`换成`let`就解决了~

```javascript
for(let i=0;i<10;i++){
  console.log(i); //0,1,2,3,4,5,6,7,8,9
}
console.log(i) //i is not defined
```

还记得当初讲闭包时setTimeout循环各一秒输出i的那个例子吗~

曾经熟悉的你 ~ 

```javascript
for(var i=0;i<10;i++){
  setTimeout(function(){
    console.log(i); //10,10,10.....
  },1000)
}
```

很显然，上面的代码输出了10次的10，`setTimeout`在执行了循环之后才执行，此时`i`已经是10了~

之前，我们这样做 ~

```javascript
for(var i=0;i<10;i++){
  setTimeout((function(i){
    console.log(i); //0,1,2,3,4,5,6,7,8,9
  })(i),1000)
}
```

现在，我们这样做 ~ 来看看把`var`改成`let`会怎样~

```javascript
for(let i=0;i<10;i++){
  setTimeout(function(){
    console.log(i); //0,1,2,3,4,5,6,7,8,9
  },1000)
}
```

nice~

## 全局块作用域绑定

在全局作用域下声明的时

- `var`会覆盖window对象中的属性
- `let`和`const`会屏蔽，而不是覆盖，用`window.`还能访问到


<hr/>

<a id="函数"></a>
# 函数

## 函数形参的返回值

ECMAScript函数不介意传递来几个参数，即使定义了一个参数，传进来10个参数都是可以的。这是因为参数在函数内部是用一个数组来表示的，也就是说函数接收到的始终是这个数组，即使数组元素为空。

在函数体内，可以通过`arguments`对象来访问这个参数数组，从而获得每一个参数。

函数参数可以分为形参和实参，在JS中，我们可以这样获取两个参数的长度：

```javascript
function foo(a,b){
  var formalParamLen=arguments.callee.length;
  var actualParamLen=arguments.length;
  console.log(formalParamLen);
  console.log(actualParamLen);
};
foo() // 2,0
foo(1) // 2,1
foo(1,2) // 2,2
foo(1,2,3) // 2,3
```

通过执行上面的代码可以发现，`arguments`是实参，在函数内部就可以通过`arguments[0]`等来访问。

但是 ~ `arguments`其实不是一个数组，`Array.isArray(arguments)`的结果是`false`,`typeof arguments`的结果是`object`。

好了，来看看和ES6相关的内容吧~

### 在ECMAScript5中模拟默认参数

通过上文我们知道即使定义了参数也是可以不传递的，所以严谨的做法是是为定义的参数赋予一个默认值。

在ECMAScript5中，往往这样来给参数赋予默认值：

```javascript
function getPrice(name,type){
  type=(typeof type !== "undefined") ? type : "All";
}
```

### ECMAScript6中的默认参数值

在ES6中，就简单了~

```javascript
function getPrice(name,type="All"){
  console.log(name+":"+type);
  console.log(arguments.length)
}
getPrice("apple"); // apple:All
getPrice("apple","China"); // apple:China
```

效果是一样哒~


### 默认参数值对arguments对象的影响

在在ECMAScript5中，比如通过`var param1 = arguments[0]`获取了第一个参数之后对这个`param1`进行修改，则连着`arguments[0]`也会被修改，这是不安全的。

```javascript
function getPrice(name){
  console.log(name); // ES5
  name="ES6";
  console.log(name); //ES6
  console.log(arguments[0]); ES6
}
```

在ES6中，通过赋予默认参数，这种情况就不会发生了：

```javascript
function getPrice(name="ES5"){
  console.log(name); // ES5
  name="ES6";
  console.log(name); //ES6
  console.log(arguments[0]); //undefined
}
```

## 处理无命名参数

### 不定参数

之前说过，可以通过`arguments`来检查函数的实参，在ES6中，通过引入不定参数(rest parmaeters)的特性来解决这个问题。

在函数的参数命名前加三个点（`...`）就表明这是一个不定参数，该参数是一个数组。与`arguments`不同的是，不定参数不包括`...`之前传入的参数。

- 不定参数之后不能有其他参数
- 不能用于对象字面量的setter中

```javascript
function foo(a,...array){
  console.log(arguments.length); // 4
  console.log(array.length); // 3
  console.log(arguments[0]); // 1
  console.log(array[0]); // 2
}
foo(1,2,3,4);
```

不管有没有使用不定参数，`arguments`都是包含所有传入的参数。

## 增强的Function构造函数

允许在`Function`构造函数中使用不定参数：

```javascript
var foo=new Function("...args","return args");
foo(1,2,3) //[1,2,3]
```

## name属性

`name`属性用来获取函数的名称。

```javascript
function getJob(){
  console.log("Give me a job!")
}
console.log(getJob.name) // getJob
```

如果是这样的呢？

```javascript
var getJob=function giveMeJob(){
  console.log("Give me a job!")
}
console.log(getJob.name) // giveMeJob
```

发现输出的是giveMeJob，这是因为函数表达式赋予了一个名字，这个名字比本身被赋值的变量的权重高。

特殊情况：

- 通过`bind()`函数创建的函数
- 通过`Function`构造函数创建的函数

```javascript
var foo(){};
console.log(foo.bind().name); //bound foo
console.log((new Function()).name); //anonymous
```

## 明确函数的多重用途

### 块级函数

ECMAScript5的严格模式中，当在代码块内部声明函数的时候程序会抛出错误：

```javascript
"use strict";
if(true){
  function doSomething(){
    //todo
  }
}
//这样会报错
```

ECMAScript 6 中，会将`doSomething()`函数视作一个块级声明，从而可以在定义该函数的代码块内访问和调用它。

```javascript
"use strict";
//ES6
if(true){
  console.log(typeof doSomething); //function
  function doSomething(){
    //todo
  }
  doSomething() //undefined
}
```

如果需要函数提升至代码顶部，则选择块级函数；否则，用let表达式来声明函数。

## 箭头函数

- 没有`this``super``arguments``new.target`绑定
- 不能通过`new`关键字调用
- 没有原型
- 不可以改变`this`的绑定
- 不支持`arguments`对象
- 不支持重复的命名参数

没有参数时：

```javascript
（）=>"hello world"
//相当于
function(){
  return "hello world";
}
```

一个参数时：

```javascript
value=>value
//相当于
function(value){
  return value;
}
```

函数体内多条语句：

```javascript
（value1，value2）=>{return value1+value2}
//相当于
function(value1，value2){
  return value1+value2;
}
```

返回对象：

```javascript
（id,name）=>({id:id,name:name})
（id,name）=>({id,name}) //ES6中可以缩写
//相当于
function(id，name){
  return {
    id:id,
    name:name
  }
}
```

立即执行函数表达式：

```javascript
((name)=>{
  return {
    getName:()=>name
  }
})("axuebin")
```

## 尾调用优化

> 尾调用指的是函数作为另一个函数的最后一条语句被调用。

```javascript
function doSomething(){
  return doSomethingElse(); // 尾调用
}
```

在ES5中，尾调用的实现其实是：创建一个新的栈帧，将其推入调用栈来表示函数调用，在循环调用的中，每一个未用完的栈帧都会被保存在内中，这是不好的~

### ES6中的优化

缩减了尾调用栈的大小，如果满足以下条件，就清楚并重用当前调用栈帧：

- 尾调用不访问当前栈帧的变量
- 函数内部，尾调用是最后一条语句
- 尾调用的结果当做函数值返回

就是说，要确保返回的函数调用后不执行其他操作。



<hr/>

<a id="扩展对象的功能性"></a>
# 扩展对象的功能性

## 对象类别

- 普通对象：具有JavaScript对象所有的默认内部行为
- 特异对象：具有某些与默认行为不符的内部行为
- 标准对象
- 内建对象：脚本开始执行时存在于JavaScript执行环境中的对象，所有标准对象都是内建对象。

## 对象字面量语法扩展

### 属性初始值的简写

当一个对象的属性与本地变量同名时，不必要再写冒号和值，只要写属性名就好。

```javascript
function createPerson(name,age){
  return{
    name,
    age   
  }
}
```

JavaScript引擎会在可访问作用域中查找其同名变量。

### 对象方法的简写语法

定义对象的时候，为对象添加方法可以省略冒号和`function`关键字：

```javascript
var person={
  name:"axuebin",
  sayName:function(){
    console.log(this.name)
  }
}
// ES6
var person={
  name:"axuebin",
  sayName(){
    console.log(this.name)
  }
}
```

### 可计算属性名

之前，我们如果要通过变量来获取对象的属性，就必须要用`[属性名]`的形式。

但是在定义对象的时候，属性名必须是已知的。

ES6中，可在对象字面量中使用可计算属性名称，语法也是`[属性名]`的形式：

```javascript
let lastName="lastName";
let person={
  "firstName":"xue",
  [lastName]:"bin"
}
console.log(person);
```

## 新增方法

### Object.js()方法

弥补全等运算符的不准确运算，接受两个参数，如果两个参数**类型相同**且具有**相同的值**，则返回true。


```javascript
console.log(+0===-0); //true
console.log(Object.is(+0,-0)); //false
console.log(NaN===NaN); //false
console.log(Object.is(NaN,NaN)); //true
```

### Object.assign()方法

这个方法可以接受任意数量的源对象，并按指定的顺序将属性复制到接受对象中，如果同名，后面的会覆盖前面的。

## 重复的对面字面量属性

如果对象声明了重复的属性名，不会报错，会选取最后的一个值。

## 自有属性枚举顺序

 ES6规定了对象自有属性被枚举时的返回顺序，规则如下：

- 所有数字键按升序排序
- 所有字符串键按照它们被加入对象的顺序排序
- 所有symbol键按照它们被加入对象的顺序排序

## 增强对象原型

### 改变对象的原型

通过构造函数或者`Object.create()`方法创建对象时就指定了原型，对象原型在实例化后就保持不变。

`Object.getPrototypeOf()`返回任意指定对象的原型。

ES6中：

`Object.setPrototypeOf()`改变任意指定对象的原型，接受两个参数：被改变原型的对象、替代第一个参数原型的对象。

### 简化原型访问的Super引用

Super引用相当于指向对象原型的指针，实际上就是`Object.getPrototypeOf(this)`的值，其实就是调用原型的同名方法。

`super`必须要使用简写方法的对象中使用。

用`super`调用原型上的方法，此时的`this`绑定会自动设置成当前作用域的`this`。


<hr/>

<a id="解构"></a>
# 解构

## 如何使用解构功能

### 对象解构

利用对象字面量的语法来实现解构：

```javascript
let person={
  name:"axuebin",
  age:25
}
let {name,age}=person
console.log(name) // axuebin
console.log(age) // 25
```

通过上述方式就可以直接从`person`对象中读取相应值。

注意：使用`var`、`let`、`const`来解构声明变量，必须初始化。

### 解构赋值

```javascript
let person={
  name:"axuebin",
  age:25
}
let name="a";
let age=1;
({name,age}=person)
console.log(name) // axuebin
console.log(age) // 25
```

赋值的时候必须要用一对`()`将结构赋值语句包裹起来。

### 默认值

使用解构赋值表达式时，如果指定的变量名称在对象中不存在，就会被赋值成`undefined`，或者像函数的默认值一样可以直接赋值，当不存在的时候就使用默认值。

### 为非同名局部变量赋值

使用不同命名的局部变量来存储对象属性的值：

```javascript
let person={
  name:"axuebin",
  age:25
}
let {name:localName,age:localAge}=person;
console.log(localName) // axuebin
console.log(localAge) // 25
```

`name:localName`的含义是：读取名为`name`的属性并将其值存储在变量`localName`中。

## 数组解构

使用数组字面量来解构，且解构操作全部在数组内完成。

在数组解构的语法中，通过值在数组中的位置进行选取，可以直接省略元素。

```javascript
let colors=["red","green","blue"];
let [,,thirdColor]=colors;
console.log(thirdColor);// blue
```

### 解构赋值

数组的解构赋值不需要用`()`包起来，就直接用`[]`即可。

```javascript
let colors=["red","green","blue"];
let firstColor="white";
let lastColor="black";
[firstColor,lastColor]=colors;
console.log(firstColor); // red
console.log(lastColor); // green
```

利用数组的解构赋值，我们可以很轻松的交换数组中的值。

```javascript
let [a,b]=[1,2];
console.log(a); // 1
[a,b]=[b,a];
console.log(a); // 2
```

### 默认值

可以在数组解构赋值表达式中为数组的任意位置添加默认值，当指定位置的值不存在或者为`undefined`时使用默认值。

```javascript
let name=["xue"];
let [firstName,secondName="bin"]=name;
console.log(firstName); // xue
console.log(secondName); // bin
```

### 不定元素

在数组中可以使用`...`将数组中的其余元素赋值给一个变量：

```javascript
let colors=["red","green","blue"];
let [firstColor,...restColors]=colors;
console.log(firstColor); // red
console.log(restColors); // ["green","blue"]
```

这个可以用来对数组进行复制：

```javascript
let colors=["red","green","blue"];
let [...colorsClone]=colors;
console.log(colorsClone); // ["red","green","blue"]
```

注意：不定元素必须为最后一个条目。


<hr/>

<a id=""></a>
# Symbol和Symbol属性

## 新建Symbol

```javascript
let firstName=Symbol();
let person={};
person[firstName]="axuebin";
console.log(person[firstName]);//axuebin
```

`Symbol`函数接受一个可选参数，添加一段文本描述。

```javascript
let firstName=Symbol("first name");
```

`typeof`可以用来检测`Symbol`类型。

## Symbol共享体系

ES6提供一个可以随时访问的全局Symbol注册表。

如果想创建一个可共享的`Symbol`,要使用`Symbol.for()`方法。接受一个参数表示即将创建的`Symbol`的字符串标识符。

这个方法会在全局Symbol注册表中搜索对应的`Symbol`是否存在，如果存在则返回；否则，就创建一个新的`Symbol`，注册并返回。

## Symbol与类型强制转换


<hr/>


<a id="Set集合和Map集合"></a>
# Set集合和Map集合

Set集合是一种无重复的列表，通常的做法是检测给定的值在某个集合中是否存在。

Map集合内含多组键值对，集合中每个元素分别存放着可访问的键名和它对应的值，常被用于缓存频繁取用的数据。

## ES6中的Set集合

ES6新增的Set类型是一种有序列表，其中含有一些相互独立的非重复值，通过Set集合可以快速访问其中的数据。

### 创建Set集合并添加元素

调用`new Set()`创建Set集合，调用`add()`方法向集合中添加元素，通过`size`获取元素数量。

```javascript
let set =new Set();
set.add(5);
console.log(set); // {5}
set.add(5);
console.log(set); // {5}
set.add(6);
console.log(set); // {5,6}
console.log(set.size); // 2
```

可以通过数组来初始化Set集合，过滤其中重复的值，这就为数组的去重提供了一个方法。

```javascript
//方法一
function unique(array){
  return Array.from(new Set(array))
}
//方法二
function unique(array){
  return [...new Set(array)]
}
```

通过`has()`方法可以检测Set集合中是否存在某个值

### 移除元素

调用`delete()`方法可以移除Set集合中的某一个元素，调用`clear()`方法会移除集合中的所有元素。

### Set集合的forEach()方法

`forEach`的回调函数接受以下3个参数：

- Set集合中下一次索引的位置
- 与第一个参数一样的值
- 被遍历的Set集合本身

### 将Set集合转换为数组

用展开运算符`...`可以将数组中的元素分解为各自独立的函数参数。

```javascript
let set=new Set([1,1,2,3,4,5]);
let array=[...set]
console.log(array); // [1, 2, 3, 4, 5]
```

### Weak Set集合

在Set中，如果存入一个对象，即使这个对象被回收了，Set中还保持着对这个对象的引用，如果希望对象回收后，Set中的引用也被清除，就需要用到`Weak Set`集合（弱引用Set集合）。

Weak Set集合直接利用构造函数创建，支持`add()`,`has()`,`delete()`三个方法。

```javascript
let set =new WeakSet();
let key={};
set.add(key);
console.log(set.has(key)); // true
set.delete(key);
console.log(set.has(key)); // false
set.add(key);
key=null;
console.log(set.has(key)); // false
```

## ES6中的Map集合

ES6中，Map类型是一种储存着许多键值对的有序列表，其中的键名和对应的值支持所有的数据类型。

通过`set(key,value)`方法往Map集合中添加新的元素，通过`get(key)`获取元素，如果key不存在，则返回`undefined`。

### Map集合支持的方法

- `has()`：检测键名在Map集合中是否存在
- `delete()`：移除指定键名和值
- `clear()`：清空Map集合
- `size`：包含键值对数量

### Map集合的Foreach方法

`forEach`的回调函数接受以下3个参数：

- Map集合中下一次索引的位置
- 值对应的键名
- 被遍历的Map集合本身

### Weak Map集合

<hr/>


<a id="迭代器和生成器"></a>
# 迭代器和生成器

## 循环语句的问题

多个循环嵌套需要追踪多个变量，代码的复杂度会大大增加，迭代器旨在消除这种复杂性并减少循环中的错误。

## 什么是迭代器

> 迭代器是一种特殊对象，它具有一些专门为迭代过程设计的专有接口，所有的迭代器对象都有一个`next()`方法，每次调用都返回一个结果对象。

结果对象有两个属性：

- value：表示下一个将要返回的值
- done：当没有更多可返回值的时候返回true

当最后一个值被返回之后再调用`next()`，迭代器会返回`undefined`。

可以用ES5来编写一个迭代器：

```javascript
function createIterator(items){
  var i=0;
  return{
    next:function(){
      var done=(i>=items.length);
      var value=!done?items[i++]:undefined;
      return{
        done:done,
        value:value
      }
    }
  }
}
var iterator=createIterator([1,2,3]);
console.log(iterator.next()); // "{value:1,done:false}"
console.log(iterator.next()); // "{value:2,done:false}"
console.log(iterator.next()); // "{value:3,done:false}"
console.log(iterator.next()); // "{value:undefined,done:true}"
```

当最后一个值返回之后，无论执行几次`iterator.next()`，都只是会返回`"{value:undefined,done:true}"`。

## 什么是生成器

> 生成器是一种返回迭代器的函数，通过`function`关键字后的星号`*`来表示，函数中会用到新的关键字`yield`。

```javascript
function *createIterator(items){
  for(let i=0;i<items.length;i++){
    yield items[i];
  }
}
let iterator=createIterator([1,2,3]);
console.log(iterator.next()); // "{value:1,done:false}"
console.log(iterator.next()); // "{value:2,done:false}"
console.log(iterator.next()); // "{value:3,done:false}"
console.log(iterator.next()); // 
```

每次遇到`yield`语句就会停止，每次调用`next()`方法，循环会继续运行并执行下一条`yield`语句。

### 生成器函数表达式

通过函数表达式来创建生成器，只需在`function`关键字和小括号中间添加一个星号(`*`)即可。

注意：不能用箭头函数来创建生成器。

## 可迭代对象和for-of循环

在ES6中，所有的集合对象（数组，Set和Map）和字符串都是可迭代对象，这些对象中都有默认的迭代器。

可以通过`for of`来遍历这些对象。

`for-of`循环每执行一次都会调用可迭代对象的`next()`方法。并将返回的结果对象的`value`属性存储在一个变量中，直到返回对象的`done`属性值为`true`。

```javascript
let values=[1,2,3];
for(let item of values){
  console.log(item); // 1,2,3
}
```

### 访问默认迭代器

可以通过`Symbol.iterator`来访问对象默认的迭代器，这样就可以用来检测对象是否为可迭代对象：

```javascript
function isIterable(object){
  return typeof object[Symbol.iterable]==="function";
}
```

### 创建可迭代对象

默认情况下，开发者定义的对象都是不可迭代对象，但如果给`Symbol.iterator`属性添加一个生成器，则可以将其变为可迭代对象。

```javascript
let c={
  items:[1,2,3]
}
for(let item of c){
  console.log(item) //c[Symbol.iterator] is not a function
}

let c={
  items:[1,2,3],
  *[Symbol.iterator](){
    for(let item of this.items){
      yield item;
    }
  }
}
for(let item of c){
  console.log(item) // 1,2,3
}
```

## 内建迭代器

### 集合对象迭代器

在ES6中有三种类型的集合对象：数组、Map集合与Set集合，都内建了以下三种迭代器：

- entries()：多个键值对
- values()：集合的值
- keys()：集合的键名

#### entries()迭代器

```javascript
let array=[1,2,3];
let set=new Set([1,2,3]);
let map=new Map([["name","axuebin"],["age","25"]]);
console.log(array.entries()) // Array Iterator {}
for(let item of array.entries()){
  console.log(item) // [0,1],[1,2],[2,3]
}
for(let item of set.entries()){
  console.log(item) // [1,1],[2,2],[3,3]
}
for(let item of map.entries()){
  console.log(item) // ["name","axuebin"],["age","25"]
}
```

输出`array.entries()`是`Array Iterator {}`，说明是输出一个迭代器，并不是直接是值，还需要通过`for-of`来循环遍历元素。

#### values()迭代器

```javascript
let array=[1,2,3];
let set=new Set([1,2,3]);
let map=new Map([["name","axuebin"],["age","25"]]);
for(let item of array.values()){
  console.log(item) // array.values(...)[Symbol.iterator] is not a function
}
for(let item of set.values()){
  console.log(item) // 1,2,3
}
for(let item of map.values()){
  console.log(item) // axuebin,25
}
```

在对数组元素执行`values()`时报错了。。不知道为什么。。

#### keys()迭代器

```javascript
let array=[1,2,3];
let set=new Set([1,2,3]);
let map=new Map([["name","axuebin"],["age","25"]]);
for(let item of array.keys()){
  console.log(item) // 0,1,2
}
for(let item of set.keys()){
  console.log(item) // 1,2,3
}
for(let item of map.keys()){
  console.log(item) // name,age 
}
```

#### 不同集合类型的默认迭代器

- 数组、Set集合：values()
- Map集合：entries()

所以直接用`for-of`对集合进行循环遍历即可。

### NodeList迭代器

> DOM标准中有一个NodeList类型，document对象中的所有元素都通过这个类型来表示。

```javascript
var divs=document.getElementsByTagName("div");
for(let div of divs){
  console.log(div.id);  
}
```

## 高级迭代器功能

> 未完待续~



<hr/>

<a id="JavaScript中的类"></a>
# JavaScript中的类

## ECMAScript5中的近类结构

在ES5中是利用原型来实现近类结构，先是创建一个构造函数，然后定义另一个方法并赋值给构造函数的原型。

```javascript
function Person(name){
  this.name=name;
}
Person.prototype.sayName=function(){
  console.log(this.name);
}
var person1=new Person("axuebin");
person1.sayName(); // axuebin
console.log(person1 instanceof Person); //true
console.log(person1 instanceof Object); //true
```

## 类的声明

### 基本的类声明语法

使用`class`关键字，紧跟着类的名字，语法类似对象字面量的简写模式，各个属性间不用使用`,`分隔。

比如，上文的写法可以用ES6改成这样：

```javascript
class Person{
  constructor(name){
    this.name=name;
  }
  sayName(){
    console.log(this.name);
  }
}
let person1=new Person("axuebin");
person1.sayName(); // axuebin
console.log(person1 instanceof Person);
console.log(person1 instanceof Object);
```

私有属性是实例中的属性，不会出现在原型上，且只能在类的构造函数或方法中创建，这里的`name`就是私有属性。

### 为何使用类语法

类与自定义类型之间的差异：

- 类声明不能被提升
- 类声明中的所有代码将自动运行在严格模式下
- 类中的所有方法都是不可枚举的
- 使用`new`之外的方式调用类的构造函数会导致程序抛出错误
- 在类中修改类名会导致程序报错

## 类表达式

### 基本的类表达式语法

```javascript
let Person=class{
  construtor(name){
    this.name=name;
  }
  sayName(){
    console.log(this.name);
  }
};
let person1=new Person("axuebin");
```

### 命名类表达式

```javascript
let Person=class Person2{
  construtor(name){
    this.name=name;
  }
  sayName(){
    console.log(this.name);
  }
};
let person1=new Person("axuebin");
```

在这之中类表达式被命名为`Person2`,但是`Person2`是存在于类定义中，相当于内部定义了一个类，然后return了。

## 作为一等公民的类

> 一等公民：一个可以传入函数，可以从函数返回，并且可以赋值给变量的值。

JavaScript中函数是一等公民。

ES6中，将类也设计成一等公民。

## 可计算成员名称

类方法和访问其属性支持使用可计算名称，就是用方括号包裹一个表达式即可使用可计算名称：

```javascript
let methodName="sayName";
class Person{
  construtor(name){
    this.name=name;
  }
  [methodName](){
    console.log(this.name);
  }
};
let person1=new Person("axuebin");
person1.sayName(); // axuebin
```

## 生成器方法

在类中，也可以通过在方法名前附加一个星号(`*`)来定义生成器。

```javascript
class Person{
  *createIterator(){
    yield 1;
    yield 2;
    yield 3;
  }
};
let person1=new Person();
let iterator=person1.createIterator(); 
```

如果类是表示值的集合的，可以为它定义一个默认迭代器，通过`Symbol.iterator`定义生成器方法即可：

```javascript
class Collection(){
  constructor(){
    this.items=[];
  }
  *[Symbol.iterator](){
    yield *this.items.values();
  }
}
let collection=new Collection();
collection.items.push(1);
collection.items.push(2);
collection.items.push(3);
for(let x of collection){
  console.log(x); // 1,2,3
}
```

## 静态成员

成员名前加`static`关键字，类中所有方法和访问器属性都可以定义。

注意：不可在实例中访问静态成员，必须在类中访问。

## 继承与派生类

ES6中可以用`extends`实现类的继承：

```javascript
class Rectangle{
  constructor(length,width){
    this.length=length;
    this.width=width;
  }
  getArea(){
    return this.length*this.width;  
  }
}
class Square extends Rectangle{
  constructor(length){
    super(length,length);
  }
}
var square=new Square(3);
console.log(square.getArea()); // 9
console.log(square instanceof Square); // true
console.log(square instanceof Rectangle); // true
```

通过`extends`关键字实现继承，并在构造函数中通过`super()`调用父类构造函数并传入相应参数。

如果在派生类中指定了构造函数，则必须要调用`super()`，如果不适用构造函数则会自动调用。

### 类方法遮蔽

派生类中的方法总会覆盖基类中的同名方法，但可以通过`super.方法名()`来调用父类的方法。

### 静态成员继承

如果基类有静态成员，那么这些静态成员在派生类中也可使用。

### 内建对象的继承

### Symbol.species属性

## 在类的构造函数中使用new.target

<hr/>

<a id="改进的数组功能"></a>
# 改进的数组功能

## 创建数组

创建数组的方式之前有两种：

- 调用Array构造函数
- 数组字面量

## Array.of()

`Array`构造函数有时候的结果和预料的不太一样：

```javascript
let items=new Array(2); // [undefined,undefined]
let items=new Array("2"); // ["2"]
let items=new Array(1,2); // [1,2]
let items=new Array(3,"2"); // [3,"2"]
```

给`Array`构造函数传入不同的值，数组的结果就不同。

- 如果只传入一个数字类型，则会当成是数组长度
- 如果传入多个值，则会当成数组元素

这样的特性会让人困惑。

ES6引入`Array.of()`来解决这个问题，无论是有多少个参数，无论参数是什么类型，`Array.of()`方法总会创建一个包含所有参数的数组。

```javascript
let items=Array.of(2); // [2]
let items=Array.of("2"); // ["2"]
let items=Array.of(1,2); // [1,2]
let items=Array.of(3,"2"); // [3,"2"]
```

### Array.from()

`Array.from()`方法可以接受可迭代对象或者类数组对象作为第一个参数，最终返回一个数组。

`Array.from()`方法还可以传入第二个参数，可以是一个回调函数，表示对数组的进一步操作。

#### 用Array.from()转换可迭代对象

`Array.from()`方法可以处理类数组对象和可迭代对象，也就是能将所有含有`Symbol.iterator`属性的对象转换为数组。

## 为所有数组添加的新方法

### find()、findIndex()

`find()`和`findIndex()`都接受两个参数：回调函数、可选参数（指定回调函数中this的值）。

执行回调函数时，传入的参数分别是：

- 数组中的某个元素
- 该元素在数组中的索引
- 数组本身

如果找到了立即返回结果并停止搜索。

`find()`返回查找到的值，`findIndex()`返回查找到值的索引。

### fill()

`fill()`方法可以用指定的值填充一至多个数组元素。

`fill()`有两个参数：填充的值、开始索引值、结束索引值。

如果只传入一个参数，则会用这个值重写数组：

```javascript
let arr=[1,2,3,4];
arr.fill(1);
console.log(arr); // [1,1,1,1]
```

如果传入两个参数，则会把`arr.length`当做结束索引：

```javascript
var arr=[1,2,3,4];
arr.fill(1,2);
console.log(arr); // [1,2,1,1]
```

如果传入三个参数，则开始于第二个参数，结束于第三个参数：

```javascript
var arr=[1,2,3,4];
arr.fill(1,1,3);
console.log(arr); // [1,1,1,1]
```

### copyWithin()

从数组中赋值元素的值。调用`copyWith()`方法时传入两个参数：开始填充的索引位置，开始复制值的索引位置，比如：

```javascript
let arr=[1,2,3,4];
arr.copyWithin(2,0);
console.log(arr); // [1,2,1,2]
```

默认情况下，会一直复制直到数组末尾的值。

第三个参数：停止复制的索引位置。

如果引入第三个参数，就会手动控制停止复制，比如：

```javascript
let arr=[1,2,3,4];
arr.copyWithin(2,0,1);
console.log(arr); // [1,2,1,4]
```

## 定性数组

> 定性数组是一种用于处理数值类型数据的专用数组。

### 数值数据类型

JavaScript是用64个比特来存储一个浮点形式的数据。

定型数组支持存储和操作以下8种不同的数值类型：

- int8
- uint8
- int16
- uint16
- int32
- uint32
- float32
- float64

如果用普通的数字来存储8位整数，就会浪费56个比特，这也就是定型数组的一个用例，更有效的地利用比特。

在使用定型数组之前，需要创建一个数组缓冲区存储这些数据。

### 数组缓冲区

数组缓冲区是所有定型数组的根基，它是一段可以包含特定数量字节的内存地址。可以通过`ArrayBuffer`构造函数来创建数组缓冲区：

```javascript
let buffer=new ArrayBuffer(10); //分配10个字节
```

创建后可以通过`byteLength`属性来查看缓冲区中的比特数量：

```javascript
console.log(buffer.byteLength); //10
```

### 通过视图操作数组缓冲区


这部分之后先不看了...


<hr/>

<a id="Promise与异步编程"></a>
# Promise与异步编程

`Promise`既可以像事件和回调函数一样指定稍后执行的代码，也可以明确指示代码是否成功执行。

为了让代码可读性更强，也更容易调试，可以链式地编写`Promise`。

## 异步编程的背景知识

JavaScript引擎是基于单线程（Single-threaded）事件循环的概念构建的，同一个时刻只允许一个代码块在执行。

因此，即将运行的代码被放在一个任务队列（job queue）中。每当JavaScript引擎中的一段代码结束执行，事件循环（event loop）会执行队列中的下一个任务。

`event loop`是JavaScript引擎中的一段程序，负责监控代码执行并管理任务队列。

## Promise的基础知识

### Promise的生命周期

- pending：进行中，操作尚未完成，未处理（unsettled）
- settled：已处理，异步操作执行结束
- fulfilled：异步操作成功完成
- rejected：出错，异步操作未能完成

内部属性`[[PromiseState]]`用来表示三种状态：`pending`,'fulfilled','refjected'。这个属性不对外暴露，所以只含有通过`then()`来根据不同状态执行不同的操作。

`then()`方法接受两个参数：成功时的函数，失败时的函数。

```javascript
promise.then(function(a){
  //完成
},function(err){
  //拒绝
})
```

如果只要成功的，后面一个参数就直接省略；而如果只要失败的，就要给第一个参数的位置赋个`null`。

### 创建未完成的Promise

用`Promise`构造函数可以创建新的`Promise`，构造函数只接受一个参数：包含初始化Promise代码的执行器函数。

执行器接受两个参数：`resolve()`和`reject()`。

```javascript
function a(){
  return new Promise(function(resolve,reject){
    //...  
  })
}
let promise=a();
a.then(function(){
  //完成
},function(err){
  //拒绝
})
```

## 串联Promise

每次调用`then()`方法或`catch()`方法时实际上创建并返回了另一个`Promise`，这就可以用来将多个`Promise`串联起来实现复杂的异步特性：

```javascript
let p1=new Promise(function(resolve,reject){
  resolve(1);
})
p1.then(function(value){
  console.log(value);
}).then(function(){
  console.log(3);
})
```

当执行玩`p1.then()`之后返回了一个Promise,可以继续调用它的`then()`方法，只有当第一个Promise被解决之后才会调用第二个Promise的`then()`方法。

### 捕获错误

Promise链可以用来捕获处理程序中可能发生的错误：

```javascript
let p1=new Promise(function(resolve,reject){
  resolve(1);
})
p1.then(function(value){
  throw new Error("boom!");
}).catch(function(error){
  console.log(error.message); // boom!
})
```

在Promise链的末尾留有一个拒绝处理程序可以确保能够正确处理所有可能发生的错误。

### Promise链的返回值

Promise链的另一个重要特性是可以给之后的Promise传递数据。

## 响应多个Promise

可以使用`Promise.all()`和`Promise.race()`两个方法来监听多个Promise。

### Promise.all()

接受一个参数并返回一个Promise，该参数是一个含有多个受监视Promise的可迭代对象。

只有可迭代对象中所有的Promise都被完成后才算是整个Promise被完成。

```javascript
let p1=new Promise();
let p2=new Promise();
let p3=new Promise();
let p4=Promise.all([p1,p2,p3]);
p4.then();
```

### Promise.race()

`race`和`all`的区别是，`race`接受的多个Promise中只要有一个Promise被完成，整个返回的Promise就可以完成。

## 基于Promise的异步任务执行

> 未完待续~


<hr/>
<a id="用模块封装代码"></a>
# 用模块封装代码

## 什么是模块

模块：

- 模块是自动运行在严格模式下并没有办法退出运行的JavaScript代码。

- 模块顶部创建的变量尽在模块的顶级作用于中存在，不是全局共享。

- 模块必须导出一些外部代码可以访问的元素。

脚本：

- 任何不是模块的JavaScript代码。

模块最重要的就是仅导入和导出需要的元素，而不是将所有东西都放到一个文件中。

## 导出的基本语法

可以用`export`关键字将一部分已发布的代码暴露给其他模块。

```javascript
export var name="axuebin";
export let age=25;
export function findJob(){
  console.log("FE");
}
```

## 导入的基本语法

从模块中导出的元素可以通过`import`关键字在另一个模块中访问。

```javascript
import {identifier1,identifier2} from "example.js";
```

大括号表示从给定模块导入的绑定，关键字`from`表示从哪个模块导入给定的绑定。

### 导入单个绑定

```javascript
import {findJob} from "./aboutMe.js";
findJob(); // FE
```

### 导入多个绑定

```javascript
import {name,age} from "./abountMe.js";
console.log(name); // axuebin
console.log(age); // 25
```

### 导入整个模块

```javascript
import * as abountMe from "./abountMe.js";
console.log(abountMe.name); // axuebin
console.log(abountMe.age); // 25
```

注意：只能在代码顶部使用`import`和`export`。

## 导出和导入时重命名

- 要使用不同的名称导出一个函数，则使用`as`关键字指定一个名称

```javascript
function sum(num1,num2){
  return num1+num2;
}
export {sum as add};
```

在导入这个模块时，就必须使用`add`：

```javascript
import {add} from "./example.js";
```

- 导入时也一样

```javascript
import {sum as add} from "./example.js";
console.log(add(1,2)); // 3
```

## 模块的默认值

模块的默认值是通过`default`关键字来指定的单个变量、函数或者类，只能为每个模块设置一个默认的导出值。

### 导出的默认值

```javascript
//1
export default function(num1,num2){
  return num1+num2;
}
//2
function sum(num1,num2){
  return num1+num2;
}
export default sum;
```

### 导入默认值

```javascript
import sum from "./example.js";
console.log(sum(1,2)); // 3
```

这里在导入的时候并没有使用`{}`包裹起来，这用于表示模块导出的任何默认函数。

我们还可以同时导入默认值和非默认值：

```javascript
//导出
export let name="axuebin";
export default function(){
  console.log("FE");
}

//导入
import findJob,{name} from "./aboutMe.js";
console.log(findJob()); // FE
console.log(name); // axuebin
```

注意：默认值必须在非默认值前。


