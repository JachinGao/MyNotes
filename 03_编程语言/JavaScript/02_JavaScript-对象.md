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

##### 第一种：使用Object关键字创建

```javascript
var obj = new Object();

obj.name = "张三";
obj.age = 18;
obj.gender = "male";

console.log(obj);

//输出
{name: "张三", age: 18, gender: "male"}
```

##### 第二种：使用对象字面量{}

```javascript
var person = {
    name: "李四",
    age: 18
}

console.log(person);

//输出
{name: "李四", age: 18}
```

> 字面量 / Object构造函数，原理都是一样，都是JS内置的对象Object。但是它们都是暴露在外面，代码冗余度高，也不清晰，而且无法复用。



##### 第三种：使用工厂模式创建对象

```js
function createObject(name, age) {
	const obj = new Object();
	obj.name = name;
	obj.age = age;
	obj.saiHi = function () {
		console.log(`hello,${obj.name}`);
	}
	return obj;
}
let obj1 = createObject('laocao', 22);
```

> 优点：相对于字面量或者直接调用Object，工厂模式解决了代码封装和冗余的问题，可以生成大量对象。
>
> 缺点：无法解决对象类型识别问题，因为都是由Object创建的，类型根本无法判断。



##### 第四种：用function(函数)来模拟class (无参构造函数、有参构造函数)

```javascript
//创建一个对象，相当于new一个类的实例
function Person(){
}
var personOne = new Person();//定义一个function，如果有new关键字去"实例化",那么该function可以看作是一个类
personOne.name = "dylan";
personOne.hobby = "coding";
personOne.work = function(){
	alert(personOne.name+" is coding now...");
}
personOne.work();
```

```javascript
//可以使用有参构造函数来实现，这样定义更方便，扩展性更强（推荐使用）
function Pet(name,age,hobby){
   this.name = name;//this作用域：当前对象
   this.age = age;
   this.hobby = hobby;
   this.eat = function(){
      alert("我叫"+this.name+",我喜欢"+this.hobby+",也是个吃货");
   }
}
var maidou = new Pet("麦兜",5,"睡觉");//实例化/创建对象 
maidou.eat();//调用eat方法(函数)
```

**对比工厂模式，我们可以发现以下区别：**

1. 没有显示地创建对象；
2. 直接将属性和方法赋给了this对象；
3. 没有return语句；
4. 可以识别的对象的类型。对于检测对象类型，我们应该使用instanceof操作符，我们来进行自主检测。

> 缺点：不同实例之间所访问构造函数其实不是同一块内存中存储，由于new的作用，每次实例化对象都会在内存中重新创建一个新对象。这样一来，假如不同实例都需要访问一个功能相同的方法就很不合适，极大浪费了内存。

##### 第五种：原型创建对象

我们在创建一个函数（构造函数）的时候，内部会包含一个很特殊的属性：prototype。这个属性存放着一个指针，指向的是这个构造函数的原型对象，里面包含了所有实例可以共享的属性和方法。

使用原型创建对象的方式，我们无论创建多个实例对象，可以让所有对象实例共享它所包含的属性和方法。

```js
function Person(){}
Person.prototype.name = 'Nike';
Person.prototype.age = 20;
Person.prototype.jbo = 'teacher';
Person.prototype.sayName = function(){
 	alert(this.name);
};
var person1 = new Person();
person1.sayName();
```

当为对象实例添加一个属性时，这个属性就会**屏蔽**原型对象中保存的同名属性。

```js
function Person(){}
Person.prototype.name = 'Nike';
Person.prototype.age = 20;
Person.prototype.jbo = 'teacher';
Person.prototype.sayName = function(){
 	alert(this.name);
};
var person1 = new Person();
var person2 = new Person();
person1.name ='Greg';
alert(person1.name); //'Greg' --来自实例
alert(person2.name); //'Nike' --来自原型
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





### 十二、this

解析器在调用函数时，每次都会向函数内部传递进一个隐含参数，改参数就是this，this指向的是一个对象，该对象叫做函数执行的上下文对象，根据函数调用方式不同，this会指向不同的对象。

* 以函数调用的形式调用时，永远都是window；
* 以对象方法形式调用时，this就是调用方法的那个对象；



### 十三、构造函数

* 构造函数就是一个普通函数，创建方式和普通函数没有区别，不同的是构造函数首字母习惯大写；

* 构造函数和普通函数调用方式不同，普通函数直接调用，构造函数使用new关键字调用；

构造函数执行流程：

1. 立刻创建一个新的对象；
2. 将新建的对象设置为函数中的this；
3. 逐行执行函数中的代码；
4. 将新建的对象作为返回值返回；

instanceof 可以检查一个对象是否是一个类的实例；

```javascript
function Person() {
    this.name = "张三";
    this.age = 18;
}

var per = new Person();
console.log(per);
console.log(per instanceof Person);

//输出
Person {name: "张三", age: 18}
true
```

想对象中添加方法，可以将方法放在全局作用域中定义，该方法只会被创建一次；（但是定义在全局定义域中，是有局限的，会污染全局作用域的命名空间）

```javascript
function Person(name, age) {
    this.name = name;
    this.age = age;
    this.sayName = fun;
}

function fun() {
    alert("我的名字是：" + this.name)
}

var per1 = new Person("张三", 18);
var per2 = new Person("李四", 20);

per1.sayName();
per2.sayName();
console.log(per1.sayName === per2.sayName);
```



### 十四、原型对象

1. 我们所创建的每一个函数，解析器都会向函数中添加一个属性prototype；

2. 当函数以构造函数的形式调用时，它所创建的对象中都会有一个隐含的属性，我们可以通过\__proto__来访问该属性；

3. 原型对象就相当于一个公共区域，所有同一个类的实例都可以访问这个原型对象，我们可以将对象中共有的内容，统一设置到原型对象中。

4. 我们访问对象的一个属性或方法时，它会现在对象自身中寻找，如果有直接使用，没有则会去原型对象中寻找。

   ```javascript
   function Person() {
   }
   
   Person.prototype.name = "原型中的名字";
   
   var p1 = new Person();
   var p2 = new Person();
   
   p1.name = "p1中的name";
   
   console.log(p1.name);
   console.log(p2.name);
   
   //输出
   p1中的name
   原型中的名字
   ```

   ```javascript
   function Person() {
   }
   
   Person.prototype.name = "原型中的名字";
   
   var p1 = new Person();
   //p1.name = "p1中的name";
   
   //in 用来检查对象中是否含有某个属性，如果对象中没有，原型中有，也会返回true；
   console.log("name" in p1);
   
   //用来检查对象自身中是否含有该属性；
   console.log(p1.hasOwnProperty("name"));
   
   //输出
   true
   false
   ```

5. 原型对象也是对象，所以它也有原型；自身没有去原型中找，原型没有去原型的原型中找，如果在Object中依然没有找到，返回undefined；

   ```javascript
   console.log(p1.hasOwnProperty("hasOwnProperty"));
   //去原型对象中找
   console.log(p1.__proto__.hasOwnProperty("hasOwnProperty"));
   /去原型对象中的原型中找；
   console.log(p1.__proto__.__proto__.hasOwnProperty("hasOwnProperty"));
   
   //输出
   false
   false
   true
   ```

   https://www.cnblogs.com/loveyaxin/p/11151586.html
   
   https://zhuanlan.zhihu.com/p/107847864
   
   https://blog.csdn.net/weixin_42429672/article/details/100710861

### 十五、toString

当我们不希望仅在控制台输出【object Object】，可以添加toString()方法；

```javascript
function Person(name, age) {
    this.name = name;
    this.age = age;
}

Person.prototype.toString = function () {
    console.log("name = " + this.name + ", age = " + this.age);
}

var p1 = new Person("张三",18);
var p2 = new Person("李四",20);

console.log(p1.toString());
console.log(p2.toString());
```

 

### 十六、数组

数组也是对象，和普通对象一样，也是存储一些值；

普通对象使用字符串作为属性名，而数组使用数字作为索引操作元素；

```javascript
//创建数组；
var arr = new Array();

//向数组添加元素；
arr[0] = 10;
arr[1] = 20;
```

```javascript
//使用字面量来创建数组；
var arr = [1,2,3,4];

//创建一个长度为10的数组
var arr = new Array(10);

//数组中的元素可以是任意的数据类型,也可以是对象；
var arr = [1,true,"hello",null];
```

#### 数组常用方法

* push(): 向数组末尾添加一个或多个元素，并返回数组的新的长度；

  ```javascript
  var arr = ["张三", "李四"];
  var result = arr.push("王五", "赵六");
  console.log("result:" + result);
  
  //输出
  result:4
  ```

* pop(): 删除数组的最后一个元素，并将被删除的元素作为返回值返回；

  ```javascript
  var arr = ["张三", "李四"];
  var result = arr.push("王五", "赵六");
  console.log("result:" + result);
  
  var val = arr.pop();
  console.log("result:" + val);
  
  //输出
  result:4
  result:赵六
  ```

* unshift() : 向数组开头添加一个或多个元素，并返回数组长度；原数组其他元素会依次调整；

* shift(): 可以删除数组第一个元素，并将被删除的元素作为返回值返回；

  ```javascript
  var arr = ["张三", "李四"];
  var result = arr.push("王五", "赵六");
  console.log(arr);
  
  arr.unshift("hello");
  console.log(arr);
  arr.shift();
  console.log(arr);
  
  //输出
  ["张三", "李四", "王五", "赵六"]
  ["hello", "张三", "李四", "王五", "赵六"]
  ["张三", "李四", "王五", "赵六"]
  ```

  

#### 数组遍历

  * 使用for循环
  * 使用foreach。foreach()方法需要一个函数作为参数，该函数为回调函数；每次执行，浏览器会将遍历到的元素以实参的形式传递进来，我们可以定义形参，读取这些内容；
    * 第一个参数：正在遍历的元素；
    * 第二个参数：当前元素的索引；
    * 第三个参数：遍历的数组；

  ```javascript
  var arr = ["张三", "李四", "王五", "赵六"];
  
  for (let i = 0; i < arr.length; i++) {
      console.log("val:" + arr[i]);
  }
  
  arr.forEach(function (a, b, c) {
      console.log("val" + a + ", index:" + b + ", arr:" + c);
  });
  
  //输出
  val:张三
  val:李四
  val:王五
  val:赵六
  val:张三, index:0, arr:张三,李四,王五,赵六
  val:李四, index:1, arr:张三,李四,王五,赵六
  vail王五, index:2, arr:张三,李四,王五,赵六
  val:赵六, index:3, arr:张三,李四,王五,赵六
  ```

  

#### slice和splice

**slice**

1. slice() 可以用来从数组提取指定元素；

2. 将截取的元素封装到一个新的数组中作为返回值返回；
3. 该方法不会改变原数组，只是将截取到的元素封装到一个新的数组中返回；

4. 参数：
   * 截取开始位置索引，包含开始索引；
   * 截取结束位置索引，不包含借宿索引；

5. 索引可以传递一个负值，表示截取到倒数第几个元素；

```javascript
var arr = ["张三", "李四", "王五", "赵六"];
console.log("所有元素：" + arr);

var slice = arr.slice(0, 2);
console.log("截取元素：" + slice);

//输出
所有元素：张三,李四,王五,赵六
截取元素：张三,李四
```



**splice**

1. 可以用于删除数组中的指定元素，并将被删除元素作为返回值返回；
2. 该方法会影响到原数组，会将指定元素从原数组中删除；
3.  参数：
   * 参数1：表示开始位置索引；
   * 参数2：表示删除数量；
   * 第三个及以后参数：传递的一些新的参数，插入位置为开始位置索引前面；

```javascript
var arr = ["张三", "李四", "王五", "赵六"];
console.log("所有元素：" + arr);

var splice = arr.splice(0, 2, "Hello", "World");
console.log("splice:" + splice);
console.log("arr:" + arr);

//输出
所有元素：张三,李四,王五,赵六
splice:张三,李四
arr:Hello,World,王五,赵六
```



#### concat()

可以连接两个或多个数组，并将新的数组返回；

该方法不会对原数组产生影响；

```javascript
var arr1 = ["张三", "李四"];
var arr2 = ["王五", "赵六"];
var arr3 = ["Hello", "World"];

var concat = arr1.concat(arr2,arr3);
console.log(concat);

//输出
 ["张三", "李四", "王五", "赵六", "Hello", "World"]
```



#### join()

该方法可以将数组转换为一个字符串；

在join()中可以指定一个字符串作为参数，作为数组中元素的连接符；(不指定的话，默认为逗号)

```javascript
var arr = ["张三", "李四", "王五", "赵六"];
var str = arr.join("&&&");
console.log(str);

//输出
张三&&&李四&&&王五&&&赵六
```



#### reverse() 

1. 该方法用来反转数组；
2. 该方法会直接修改原数组；

```javascript
var arr = ["张三", "李四", "王五", "赵六"];
var reverse = arr.reverse();
console.log(reverse);
console.log(arr);

//输出
 ["赵六", "王五", "李四", "张三"]
 ["赵六", "王五", "李四", "张三"]
```



#### sort()

1. 用来对数组中的元素进行排序；
2. 也会影响原数组，默认按照Unicode编码进行排序；
3. 我们可以再sort中添加回调函数，来指定排序规则；
   * 如果返回一个大于零的值，则元素会交换位置；
   * 如果返回一个小于零的值，则元素位置不变；
   * 返回零，默认相等，不交换位置；

```javascript
var arr = [1,2,3,4,11];

var sort = arr.sort();
console.log(sort);

arr.sort(function (a,b){
    if (a>b){
        return 1;
    }else if (a<b){
        return -1;
    }else {
        return 0;
    }
});
console.log(arr);

//输出
[1, 11, 2, 3, 4]
[1, 2, 3, 4, 11]
```



### 十七、call()和apply()

1. 这两个方法都是函数对象的方法，需要通过函数对象来调用；
2. 当对函数调用call()和apply()都会调用函数执行；
3. 调用call()和apply()可以将一个对象指定为第一个参数；
   * 此时这个对象将会成为函数执行时的this；
4. call() 方法可以将实参在对象之后传递；
5. apply() 方法需要将实参封装在一个数组中统一传递；

```javascript
var obj1 = {
    name: "张三",
    sayName: function () {
        console.log(this.name);
    },

    sayNum: function (a, b) {
        console.log("a:" + a, "b:" + b);
    }
};

var obj2 = {
    name: "李四"
};

function fun() {
    console.log("name:" + this.name);
}

fun.call(obj1);
fun.call(obj2);
obj1.sayName.call(obj2);
obj1.sayNum.call(obj2, 1, 2);
obj1.sayNum.apply(obj2, [1, 2]);

//输出
name:张三
name:李四
李四
a:1 b:2
a:1 b:2
```



### 十八、arguments

在调用函数时，浏览器每次都会传递两个隐含的参数：

1. 函数的上下文对象 this；
2. 封装实参的对象arguments；
   * arguments是一个类数组对象；它也可以通过索引操作数据，也可以获取长度；
   * 在调用函数时，我们所传递的实参都会在arguments中保存；
   * arguments.length 可以用来获取实参的长度；
   * 我们即使不定义形参，也可以通过arguments使用实参； 

```javascript
function fun(a, b) {
    console.log("args_length:" + arguments.length);
}

function fun2() {
    console.log("args_length:" + arguments.length);
}

fun(1, 2);
fun2(1, 2, 3);

//输出
args_length:2
args_length:3
```

arguments中有一个属性叫做callee，就是当前正在指向的函数对象；

```javascript
function fun(a, b) {
    console.log("args_length:" + arguments.length);
    console.log(arguments.callee);
}

fun(1, 2);

//输出
args_length:2
ƒ fun(a, b) {
    console.log("args_length:" + arguments.length);
    console.log(arguments.callee);
}
```



### 十九、Date对象

在JS中，使用Date对象来表示一个时间；

```javascript
var d = new Date();
console.log(d);

//输出
Sat May 15 2021 17:17:52 GMT+0800 (中国标准时间)
```



### 二十、Math对象

Math对象和其它对象不同，它不是一个构造函数；它属于一个工具类，里面封装了数学运算的属性和方法；



### 二十一、包装类

JS中为我们提供了三个包装类，通过这三个包装类可以将基本数据类型的数据转为对象；

* String(): 可以将基本数据类型字符串转为String对象；
* Number(): 可以将基本数据类型的数字转为Number对象；
* Boolean(): 可以将基本数据类型的布尔值转为Boolean对象；



### 二十二、字符串

在底层，字符串是以字符数组的形式保存的；

```javascript
var str = "HelloWorld";
console.log(str[1]);

//获取长度
console.log(str.length);

//根据索引获取char
console.log(str.charAt(0));  //输出:H

//返回的是Unicode编码
console.log(str.charCodeAt(0)); //输出：72

//从Unicode编码获取对应的char；
console.log(String.fromCharCode(72)); //输出：H

//字符串拼接
console.log(str.concat(", Jack"));  //输出：HelloWorld, Jack

//返回指定字符的索引
console.log(str.indexOf("l")); //输出：2

//从后往前找
console.log(str.lastIndexOf("l")); //输出：8

//截取指定位置，不会影响原字符串；
console.log(str.slice(0,5)); //输出：Hello

//将字符串按照规则拆分为数组；
var str2 = "Jay,John,Bob";
console.log(str2.split(",")); //输出：["Jay", "John", "Bob"]
```



### 二十三、正则表达式

用于定义一些字符串的规则， 

* 计算机可以根据正则表达式检查该字符串是否符合规则；
* 将字符串中符合规则的内容提取出来；

#### 创建

```
//语法
var 变量 = new RegExp("正则表达式","匹配模式");

//使用字面量创建正则表达式
var 变量 = /正则表达式/匹配模式;
```

1. **匹配模式**

   在构造函数中，可以传递一个匹配模式作为第二个参数；

   * i 忽略大小写；
   * g 全局匹配模式；

2. **test()**

   使用test()方法可以用来检测一个字符串是否符合正则表达式的规则；

   * 符合返回true，否则返回false；

```javascript
//使用 | 表示或的意思
var reg = /a|b/;
console.log(reg.test("a")); //true

//使用[]也是或的关系，[ab] == a|b;
// [a-z] 任意小写字母
// [A-Z] 任意大写字母
// [0-9] 任意数字
reg = /[abcd]/;
console.log(reg.test("c"));  //true
```

```javascript
//量词： 设置一个内容出现的次数
// {n} 正好出现n次；
// {m,n} 出现m-n次；
// {m,} m次往上;
// +:至少一个，相当于{1,}
// * 0个，或多个，相当于{0，}
// ? 0个或一个，相当于{0,1}
var reg = /a{3}/;
console.log(reg.test("abb"));  //输出：false
console.log(reg.test("aaab")); //输出：true

//检查一个字符串是否以a开头
// ^ 表示开头
reg = /^a/;
console.log(reg.test("abc")); //true

//检查一个字符串是否以a结尾
// $ 表示结尾
reg = /a$/;
console.log(reg.test("ca"));  //true

reg = /^a$/; //即是开头，又是结尾；
console.log(reg.test("aaa")); //false
console.log(reg.test("a"));   //true

reg = /^a|a$/; //以a开头，或者以a结尾;
console.log(reg.test("aaa")); //true
```

```javascript
/**
 * 检查一个字符串中是否含有.
 * . 有特殊意义，表示任意字符
 *
 * 在正则表示是中使用\作为转义字符；
 */

var reg = /\./;
console.log(reg.test("ab")); //false
console.log(reg.test("a.")); //true

/**
 * \w 任意字母、数字、下划线
 * \W 除了字母、数字、下划线
 */
reg = /\w/;
console.log(reg.test("a")); //true
console.log(reg.test("1")); //true
console.log(reg.test("_")); //true

reg = /\W/;
console.log(reg.test("a")); //false
console.log(reg.test("1")); //false
console.log(reg.test("_")); //false
console.log(reg.test("@")); //true

/**
 * \d 任意数字
 * \D 任意非数字
 */
reg = /\d/;
console.log(reg.test("123")); //true
console.log(reg.test("abc")); //false

reg = /\D/;
console.log(reg.test("123")); //false
console.log(reg.test("abc")); //true

/**
 * \s 空格
 * \S 除了空格
 */
reg = /\s/;
console.log(reg.test("ab c")); //true

reg = /\S/;
console.log(reg.test("abc")); //true

/**
 *  \b 单词边界
 *  \B 除了单词边界
 */
reg = /\bchild\b/;
console.log(reg.test("Hello children")); //false
console.log(reg.test("Hello child")); //true
```

```javascript
/**
 * 手机号正则匹配
 * 1. 以1开头
 * 2. 第二位3-9任意数字
 * 3. 三位以后任意数字9个
 */
var phoneReg = /^1[3-9][0-9]{9}$/;
console.log(phoneReg.test("18651891111")); //true
console.log(phoneReg.test("11012345678")); //false
```

```javascript
/**
 * 电子邮件正则表达式
 *      hello        .hello        @    abc          .com              .cn                结尾
 * 开头 任意字母下划线  .任意字母下滑线  @   任意字母数字    .任意字母（2-5位）   .任意字母（2-5位）
 * ^    \w{3,}      (\.\w+)*      @    [a-z0-9]+     (\.[A-Za-z]{2,5}){1,2}               $
 */
var emailReg = /^\w{3,}(\.\w+)*@[a-z0-9]+(\.[A-Za-z]{2,5}){1,2}$/;
console.log("email:" + emailReg.test("abc@163.com"));  //true
console.log("email:" + emailReg.test("  abc@163.com")); //false
console.log("email:" + emailReg.test("f#45bc@163.com")); //false
console.log("email:" + emailReg.test("agc@1^3.com"));  //false
```



#### 字符串中的一些方法

1. **split()**

   拆分字符串；

   * 将一个字符串拆分为一个数组；
   * 方法中可以传递一个正则表达式作为参数，此时方法将会根据正则表达式拆分字符串；

2. **match()**

   可以根据正则表达式，从一个字符串中将符合条件的内容提取出来；

   * 默认情况，我们match只会找到第一个符合要求的内容，找到后就会停止检索；
   * 也可以设置为全局匹配模式，这样会匹配到所有的内容；

3. **search()**

   搜索字符串中是否含有指定内容；

   * 如果搜索到指定内容，则会返回到第一次出现的索引，没有搜索到则会返回-1；
   * 可以接受一个正则表达式作为参数，根据正则表达式去检索字符串；

4. **replace()**

   可以将字符串中指定内容替换为新的内容；

   * 默认只会替换第一个，使用g参数，替换全局；

```javascript
//split()拆分
var str = "123a456b789c000";
console.log(str.split(/[a-z]/)); //输出：["123", "456", "789", "000"]

//search() 搜索字符串中是否含有指定内容;
console.log(str.search("4[0-9]6")); //输出:4 （索引）

//match() 可以根据正则表达式，从一个字符串中将符合条件的内容提取出来；
console.log(str.match(/[a-z]/gi)); //输出：["a", "b", "c"]

//replace()替换
console.log(str.replace(/[a-z]/gi,"@@")); //输出：123@@456@@789@@000
```







