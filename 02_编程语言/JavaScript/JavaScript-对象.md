### 一、对象简介

* Object类型，也称为对象，是JavaScript中的引用数据类型。
* 对象属于一种复合的数据类型，在对象中可以保存多个不同数据类型的属性。

#### 对象分类

1. 内建对象

   由ES标准中定义的对象，在任何ES的实现中都可以使用；比如Math、String、Mumber

2. 宿主对象

   由JS运行环境提供的对象，目前来讲主要指由浏览器提供的对象；

3. 自定义对象

   由开发人员自己创建的对象；



### 二、创建对象

##### 第一种：

```javascript
var obj = new Object();

obj.name = "张三";
obj.age = 18;
obj.gender = "male";

console.log(obj);

//输出
{name: "张三", age: 18, gender: "male"}
```

##### 第二种：

```javascript
var person = {
    name: "李四",
    age: 18
}

console.log(person);

//输出
{name: "李四", age: 18}
```



### 三、对象属性的访问

两种方式

* 对象.属性名
* 对象["属性名"]

```javascript
var person = {
    name: "李四",
    age: 18
}
console.log(person);

//访问
var str = person["name"];
console.log(str);

//赋值
person["name"] = "小明";
console.log(person["name"]);

//输出
{name: "李四", age: 18}
李四
小明
```



### 四、数据类型

* JS中变量包含两种不同数据类型：基本数据类型、引用数据类型；
* JS中一共5种基本数据类型：String、Number、Boolean、Undefined、Null；
* 基本数据类型的值无法修改，不可变；
* 当一个变量是对象时，实际上变量保存的并不是对象本身，而是对象的引用；
* 当一个变量向另一个变量复制引用类型的值时，会将对象的应用复制到变量中，并不是创建一个新的对象；



### 五、堆和栈

* JavaScript在运行时数据是保存到栈内存和堆内存当中的；
* 栈内存用来保存变量和基本类型，堆内存用来保存对象；
* 引用类型会在堆内存中保存，变量中保存的实际上对象在堆内存中的地址。



### 六、函数

* 函数也是一个对象；
* 函数中可以封装一些功能（代码），在需要时可以执行这些功能；
* 使用typeof检查一个函数对象时，会返回function；

```javascript
可以将要封装的代码以字符串的形式传递给构造函数；（不常用）
var fun = new Function("console.log(\"Hello World!\");");
fun();

//输出
Hello World!
```



#### 使用函数生命创建函数

```
function 函数名([形参1，形参2...]) {
	语句...
}
```



#### 使用函数表达式创建函数

```
var 函数名 = function([形参1，形参2...]) {
	语句...
}
```



### 七、函数的参数

* 可以在函数()中指定一个或多个形参；形参之间用‘，’隔开；

* 声明形参就相当于在函数内部声明了对应的变量；

* 在调用函数时，可以在（）中指定实参，实参将会赋值给函数中对应的形参；

* 调用函数时，解析器不会检查实参的类型，多余的实参不会被赋值；

* 实参数量小于形参数量， 则对应形参将是undefined；

  ```javascript
  function sum(a, b) {
      console.log(a + b);
  }
  
  sum(1, 2);
  
  //输出
  3
  ```

 

1. 实参可以是任何数据类型，也可以是一个对象，当参数过多时，可以将参数封装到一个对象中，然后通过对象传递；
2. 实参也可以是一个函数；
   * **fun() 调用函数**，相当于使用的函数的返回值；
   * **fun 函数对象**，相当于使用函数对象；



### 八、函数的返回值

1. 可以使用return来设置函数的返回值，return后的值作为函数的结果返回，并赋值给一个变量；
2. 如果函数不写return会返回undefined；

```javascript
function sum(a, b) {
    return a + b;
}

var result = sum(1, 2);
console.log(result);

//输出
3
```

3. 返回值类型
   * break退出当前循环；
   * continue跳过当次循环；
   * return可以结束整个函数；



### 九、立即执行函数

* 函数定义完，立即被调用，这种函数叫做立即执行函数；
* 立即执行函数往往只会执行一次；

```javascript
(function () {
    alert("我是立即执行函数..");
})();

//也可以传递参数
(function (a, b) {
    console.log(a + b);
})(100, 200);
```



### 十、对象方法

对象的属性值也可以是个函数；

```javascript
var obj = new Object();

obj.name = "张三";
obj.age = 18;
obj.gender = "male";
obj.sayName = function (){
  console.log("调用对象方法");
};

obj.sayName();

//输出
调用对象方法
```

枚举对象中的属性，使用for...in..语句；

```
//语法
for (var 变量 in 对象) {
	语句...
}
```

```javascript
for (var n in obj) {
    console.log("属性名 = " + n + ", value = " + obj[n]);
}

//输出
属性名 = name, value = 张三
属性名 = age, value = 18
属性名 = gender, value = male
属性名 = sayName, value = function () {
    console.log("调用对象方法");
}
```



### 十一、作用域

作用域指一个变量的作用范围；

1. 全局作用域：

   * 直接编写在script标签中的JS代码，都在全局作用域；
   * 全局作用域在页面打开时创建，在页面关闭时销毁；
   * 在全局作用域中有一个全局对象window，它代表浏览器窗口，由浏览器创建，我们可以直接使用；
   * 在全局作用域中，创建的变量都会作为window对象的属性保存；
   * 使用var关键字声明的变量，会在所有代码执行之前被声明；

   ```javascript
   console.log(a);
   var a = 10;
   
   //输出
   undefined
   ```

   * 使用函数声明形式创建的函数function 函数() {}，它会在所有代码执行前就被创建，所以可以在函数声明前调用；
   * 使用表达式创建的函数，不会被声明提前，所以不能在声明前调用；

   

2. 函数作用域

   * 调用函数时创建函数作用域，函数执行完毕，函数作用域销毁；
   * 每调用一次函数就会创建一个新的函数作用域，它们之间相互独立；
   * 函数作用域可以访问到全局作用域变量，但全局作用域中无法访问函数作用域的变量；





### 十三、this

解析器在调用函数时，每次都会向函数内部传递进一个隐含参数，改参数就是this，this指向的是一个对象，该对象叫做函数执行的上下文对象，根据函数调用方式不同，this会指向不同的对象。

* 以函数调用的形式调用时，永远都是window；
* 以对象方法形式调用时，this就是调用方法的那个对象；