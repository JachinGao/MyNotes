## JavaScript基础



### 一、JS编写位置

1. 可以编写到标签的onclick属性中，也可以在超链接中；（不推荐，存在耦合）

   ```html
   <!DOCTYPE html>
   <html lang="en">
   <head>
       <meta charset="UTF-8">
       <title>Title</title>
   </head>
   <body>
   <button onclick="alert('按钮已被点击');">点我一下</button>
   <a href="javascript:alert('超链接已经被点击')">超链接点击</a>
   </body>
   </html>
   ```

2. 放在script标签中

   ```html
   <head>
       <meta charset="UTF-8">
       <title>Title</title>
       <script type="text/javascript">
           alert("script中的代码");
       </script>
   </head>
   ```

3. 将js编写到外部js文件中，然后通过script标签引入；

    ```html
    //script标签一旦用于引入外部文件，就不能再编写代码，即使编写浏览器也会忽略
    <head>
        <meta charset="UTF-8">
        <title>Title</title>
       <script type="text/javascript" src="js/demo_js.js"></script>
    </head>
    ```
   
    

### 二、注释/格式

1. 注释

   ```html
   //单行注释
   
   /*
   	多行注释
   */
   ```

2. js严格区分大小写；
3. js中每条语句分号(;)结尾，不写会自动添加，但会消耗一些系统资源，而且有时候会加错位置；
4. js会忽略多个空格和换行；





## 基本语法

### 一、字面量和变量

1. 字面量，都是不可改变的值，可以直接使用；在开发中，通常都是使用变量去保存一个字面量；
2. js中，使用var关键字来声明一个变量；

```html
var a = 2234;
console.log(a);
```



### 二、标识符

1. JS中所有可以自主命名的都可以称为是标识符，如变量名、函数名、属性名。
2. 标识符准守如下规则：
   * 标识符可以含有字母、数字、_、$
   * 标识符不能以数字开头
   * 标识符不能是ES中的关键字或保留字；（例如：var、static、boolean）
   * 标识符一般采用驼峰命名法

> js底层保存时采用Unicode编码，所以所有utf-8中含有的内容都可以作为标识符；



### 三、数据类型

在js中有六种数据类型；

| 名字      | 类型   |
| --------- | ------ |
| String    | 字符串 |
| Number    | 数值   |
| Boolean   | 布尔值 |
| Null      | 空值   |
| Undefined | 未定义 |
| Object    | 对象   |

前五种是基本类型，最后一种Object是引用数据类型；



#### 1. 字符串

* JS中字符串需要使用引号引用起来；
* 双引号或单引号都可以，但是不要混用；
* 引号不能嵌套，双引号里不能放双引号，单引号里不能放单引号；
* 在字符串中，我们可以使用\作为转义字符，表示一些特殊符号时，可以使用\进行转义；



#### 2. Number

> 可以使用运算符typeof来检查一个变量的类型；

```html
var str = "hello" + ", world";
console.log(str);
console.log(typeof str);

// 输出
// hello, world
// string
```

* JS中所有数值都是Number类型，包括整数和浮点数（小数）；
* JS中可以表示数字的最大值，Number.MAX_VALUE，如果使用Number表示的数值超过最大值，会返回一个Infinity；Infinity表示正无穷，-Infinity表示负无穷，使用typeof检查Infinity返回Number；
* **NaN**表示不是一个数值，typeof返回也是Number；



#### 3. 布尔值

* 主要用来做逻辑判断：true表示真，false表示假；
* 使用typeof检查一个布尔值时，会返回boolean；



#### 4. Null和Undefined

1. Null类型的值只有一个，就是null，用来表示一个为空的对象；

2. typeof检查Null值时，会返回object；
3. 当声明一个变量，但是未给变量赋值时，它的值就是undefined；



### 四、强制类型转换

 强制类型转换主要是将其他数据类型，转换为String、Boolean、Number；

#### 1. 转换为String

1. 调用被转换数据类型的toString()方法；

   * 该方法不会影响到原变量，它会将转换的结果返回;
   * null和undefined这两个值没有toString()方法，如果调用他们的方法，会报错；

   ```html
   var a = 123;
   var b = a.toString();
   
   console.log("a的类型：" + typeof a);
   console.log(a.toString());
   console.log("b的类型：" + typeof b);
   
   // 输出
   // a的类型：number
   // 123
   // b的类型：string
   ```

2. 调用String函数

   * 对于Number和Boolean实际上就是调用的toString()方法；
   * 但是对于null和undefined，就不会调用toString()方法，它会将 null 直接转换为 "null"，将 undefined 直接转换为 "undefined"；

   ```html
   var a = 123;
   var b = String(a);
   
   console.log("a的类型：" + typeof a);
   console.log(a.toString());
   console.log("b的类型：" + typeof b);
   
   // 输出
   // a的类型：number
   // 123
   // b的类型：string
   ```

   

#### 2. 转换为Number

1. 使用Number()函数;

   * 如果字符串中有非数字的内容，则转换为NaN；
   * 如果字符串是空串或是空格的字符，则转换为0；
   * boolean转数字，true→1，false→0；
   * null→数字0；
   * undefined → 数字NaN；

   ```html
   var a = "123";
   var b = Number(a);
   
   console.log("a的类型：" + typeof a);
   console.log(a.toString());
   console.log("b的类型：" + typeof b);
   
   // 输出
   // a的类型：string
   // 123
   // b的类型：number
   ```

2. **parseInt()** 可以将一个字符串中的有效整数内容去出来，然后转为Number，**parseFloat()**作用类似，不同的是它可以获得有效的小数。

   ```js
   var a = "123567a567px";
   //parseInt()可以将一个字符串中的有效的整数内容去出来，然后转换为Number
   a = parseInt(a); //输出1234567
   	
   //parseFloat()作用和parseInt()类似，不同的是它可以获得有效的小数    
   a = "123.456.789px";
   a = parseFloat(a); //输出123.456
   			
   //如果对非String使用parseInt()或parseFloat(),它会先将其转换为String然后再操作
   a = true;
   a = parseInt(a); //输出NaN
   ```

3. 转为其他进制数字

   * JS中，如果需要表示16进制数字，则需要以0x开头；
   * 如果需要8进制数字，则以0开头；
   * 如果需要2进制数字，则以0b开头；

   ```js
   var a = 0x10;
   var b = 070;
   var c = 0b010;
   
   //可以在parseInt()中传递一个第二个参数，来指定数字的进制
   var d = parseInt(a,10);
   ```

#### 3. 转换为Boolean

* 使用Boolean()函数；
* 除了0和NaN，其余都是true；
* 字符空串是false；
* null和undefined会转换为false；
* 对象也会转为true；

```js
var a = null; //false
a = Boolean(a);
			
a = undefined; //false
a = Boolean(a);
```



### 五、算数运算符

运算符也叫操作符，通过运算符可以**对一个或多个值**进行运算； 比如，typeof就是运算符；

1. 对非Number类型的值进行运算时，会将这些值转换为Number运算；

2. 任何值和NaN做运算，都得NaN；

3. 任何值和字符串做加法运算，都会先转换为字符串，然后再和字符串做拼串处理；

   ```javascript
   var a = "123" + 456;
   var b = "123" + true;
   var c = "123" + NaN;
   
   console.log(a);
   console.log(b);
   console.log(c);
   
   //输出
   123456
   123true
   123NaN
   ```

4. 任何值做- * /运算时都会自动转换为Number进行运算；

   ```javascript
   var a = 100 - "1";
   var b = 2 * 2;
   var c = 2* "8";
   var d = 2 * null;
   var e = 2 * undefined;
   
   console.log(a);
   console.log(b);
   console.log(c);
   console.log(d);
   console.log(e);
   
   //输出
   99
   4
   16
   0
   NaN
   ```

   

### 一元运算符

**定义：**只需要一个操作数；

1. 可以对一个其他数据类型使用+，来将其转换为Number类型，原理和Number()一样；



### 自增和自减

a++和++a，都会立即使原变量的值增加1；

不同的是a++是先使用，后增加；++a是先增加，后使用；



### 逻辑运算符

JS中为我们提供了三种逻辑运算符；对于非布尔值进行与或运算时，会先将其转换为布尔值，然后再运算，并且返回原值；

* 与：如果两个值都为true，则返回后面的；
* 与：如果两个值中有false，则返回第一个false；
* 或：如果第一个值为true，则返回第一个值；
* 或：如果第一个值为false，则返回第二个值；

```javascript
var a = 1 && 2 && 3 && 4;
var b = NaN && false && 3 && 4;
var c = 1 || 2 || 3 || 4;
var d = NaN || false || 3 || 4;

console.log(a);
console.log(b);
console.log(c);
console.log(d);

//输出
4
NaN
1
3
```





### 赋值运算符

可以将符号右侧的值赋值给符号左侧的变量；



### 关系运算符

通过关系运算符，可以比较两个值之间的大小关系；

* 如果关系成立返回true；

* 如果关系不成立返回false；

比较两个字符串时，比较的是字符串的字符编码；（一位一位的比较，如果对应位置一样，则比较下一位）



### Unicode编码表

在字符串中使用转义字符输入Unicode编码；(\u四位编码)

```javascript
console.log("\u0034");
```



### 相等运算符

* 一个等号是赋值操作；
* ==先转换类型再比较；
* ===先判断类型，如果不是同一类型直接为false；

用来比较两个值是否相等，类型不同会做类型转换；

```javascript
console.log( “1” == 1);  //true
```

* ===：全等，用来判断两个值是否相等，它和相等类似，不同的是它不会做自动类型转换。如果两个值类型不同，直接返回false；

* !==：不全等，和不等类似，类型不同直接返回true；

  

### 条件运算符

也叫做三元运算符；

语法：  条件表达式？语句1：语句2；



### 运算符的优先级

在JS中，有一个运算符表格，在表中越靠上，优先级越高；

* 逻辑与的优先级大于或；



### If语句

```
if (条件表达式){
	语句。。。
} else if (条件表达式) {
	语句。。。
}
```



### Switch语句

```javascript
switch(条件表达式) {
	case 表达式:
		语句..
		break;
	case 表达式:
		语句..
		break;
	default:
		语句..
		break；
}
```



### while循环

1. 对条件表达式进行求值判断，如果值为true，则执行循环体；

2. 循环体执行完之后，继续对表达式判断，如果为true，继续执行，否则终止循环。

   > 可以使用break终止循环。

```javascript
while(条件表达式) {
	语句...
}
```



### for循环

```java
for(初始化表达式; 条件表达式; 更新表达式) {
	语句...
}
```



### break和continue

break关键字用来退出switch或循环语句；

continue关键字用来跳过当次循环；