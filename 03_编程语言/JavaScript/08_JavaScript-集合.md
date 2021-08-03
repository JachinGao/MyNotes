### 一、数组

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



#### 1. 数组常用方法

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

  

#### 2. 数组遍历

##### (1) 使用for循环

```js
  var arr = ["张三", "李四", "王五", "赵六"];
  
  for (let i = 0; i < arr.length; i++) {
      console.log("val:" + arr[i]);
  }
```

##### (2) 使用foreach

foreach()方法需要一个函数作为参数，该函数为回调函数；每次执行，浏览器会将遍历到的元素以实参的形式传递进来，我们可以定义形参，读取这些内容；

* 第一个参数：正在遍历的元素；
* 第二个参数：当前元素的索引；
* 第三个参数：遍历的数组；

  ```javascript
  var arr = ["张三", "李四", "王五", "赵六"];
  
  arr.forEach(function (a, b, c) {
      console.log("val" + a + ", index:" + b + ", arr:" + c);
  });
  
  //输出
  val:张三, index:0, arr:张三,李四,王五,赵六
  val:李四, index:1, arr:张三,李四,王五,赵六
  vail王五, index:2, arr:张三,李四,王五,赵六
  val:赵六, index:3, arr:张三,李四,王五,赵六
  ```



#### 3. slice()和splice()

##### slice

* slice() 可以用来从数组提取指定元素；

* 将截取的元素封装到一个新的数组中作为返回值返回；

* 该方法不会改变原数组，只是将截取到的元素封装到一个新的数组中返回；

* 参数

  * 截取开始位置索引，包含开始索引；
  * 截取结束位置索引，不包含借宿索引；
* 索引可以传递一个负值，表示截取到倒数第几个元素；
  

```javascript
var arr = ["张三", "李四", "王五", "赵六"];
console.log("所有元素：" + arr);

var slice = arr.slice(0, 2);
console.log("截取元素：" + slice);

//输出
//所有元素：张三,李四,王五,赵六
//截取元素：张三,李四
```

##### splice

1. 可以用于删除数组中的指定元素，并将被删除元素作为返回值返回；
2. 该方法会影响到原数组，会将指定元素从原数组中删除；
3. 参数：
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



#### 4. concat()

可以连接两个或多个数组，并将新的数组返回；

该方法不会对原数组产生影响；

```js
var arr1 = ["张三", "李四"];
var arr2 = ["王五", "赵六"];
var arr3 = ["Hello", "World"];

var concat = arr1.concat(arr2,arr3);
console.log(concat);

//输出
 ["张三", "李四", "王五", "赵六", "Hello", "World"]
```



#### 5. join()

该方法可以将数组转换为一个字符串；

在join()中可以指定一个字符串作为参数，作为数组中元素的连接符；(不指定的话，默认为逗号)

```javascript
var arr = ["张三", "李四", "王五", "赵六"];
var str = arr.join("&&&");
console.log(str);

//输出
张三&&&李四&&&王五&&&赵六
```



#### 6. reverse() 

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



#### 6. sort()

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





### 二、Set

ES6 提供了新的数据结构 Set。它类似于数组，但是成员的值都是唯一的，没有重复的值。

集合实现iterator接口，可以使用扩展运算符和for...of..进行遍历。



#### 1. 常用api

size：返回集合元素个数；

add：增加一个新元素，返回当前集合；

delete：删除元素，返回boolean值；

has：检测集合中是否包含某个元素，返回boolean值；

```
const s = new Set();

[2,3,5,4,5,2,2].forEach(x => s.add(x));
// Set结构不会添加重复的值

for(let i of s) {
    console.log(i);
}
```

https://blog.csdn.net/DurianPudding/article/details/88116732?utm_medium=distribute.pc_relevant_t0.none-task-blog-2%7Edefault%7EBlogCommendFromMachineLearnPai2%7Edefault-1.control&depth_1-utm_source=distribute.pc_relevant_t0.none-task-blog-2%7Edefault%7EBlogCommendFromMachineLearnPai2%7Edefault-1.control
