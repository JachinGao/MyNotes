### 一、let

1. 变量不能重复声明

   ```
   // let star = '罗志祥';
   // let star = '小猪';
   ```

2. 块级作用域 

   ```
   {
       let girl = '周扬青';
   }
   console.log(girl); //读取不到girl
   ```

3. 不存在变量提升

   ```
   // console.log(song);
   // let song = '恋爱达人';
   ```

4. 不影响作用域链

   ```
   {
       let school = '尚硅谷';
       function fn(){
             console.log(school);
       }
       fn();
   }
   ```

   

### 二、const

1. 一定要赋初始值

   ```
   const A; //非法
   ```

2. 一般常量使用大写(潜规则)

3. 常量的值不能修改

4. 块级作用域

5. 对于数组和对象的元素修改, 不算做对常量的修改, 不会报错

   ```
   const TEAM = ['UZI','MXLG','Ming','Letme'];
   TEAM.push('Meiko');
   ```

   

### 三、解构赋值

ES6 允许按照一定模式从数组和对象中提取值，对变量进行赋值；

1. 数组的解构

   ```
   const F4 = ['小沈阳','刘能','赵四','宋小宝'];
   let [xiao, liu, zhao, song] = F4;
   console.log(xiao);
   console.log(liu);
   console.log(zhao);
   console.log(song);
   ```

2. 对象的解构

   ```js
   const zhao = {
          name: '赵本山',
          age: '不详',
          xiaopin: function(){
          console.log("我可以演小品");
      }
   
   let {name, age, xiaopin} = zhao;
   console.log(name);
   console.log(age);
   console.log(xiaopin);
   xiaopin();
   ```

   

### 四、模板字符串

ES6 引入新的声明字符串的方式 『``』

1. 声明

   ```
   let str = `我也是一个字符串哦!`;
   console.log(str, typeof str);
   ```

2. 内容中可以直接出现换行符

   ```js
    let str = `<ul>
               <li>沈腾</li>
               <li>玛丽</li>
               <li>魏翔</li>
               <li>艾伦</li>
               </ul>`;
   ```

3. 变量拼接

   ```js
   let lovest = '魏翔';
   let out = `${lovest}是我心目中最搞笑的演员!!`;
   console.log(out);
   ```



### 五、简化对象写法

ES6 允许在大括号里面，直接写入变量和函数，作为对象的属性和方法，这样的书写更加简洁。

```js
let name = '尚硅谷';
let change = function(){
     console.log('我们可以改变你!!');
}

const school = {
       name,
       change,
       improve(){
          console.log("我们可以提高你的技能");
       }
 }

console.log(school);
```



### 六、箭头函数

ES6 允许使用「箭头」（=>）定义函数

```js
//声明一个函数
let fn = function(){
    
}

let fn = (a,b) => {
     return a + b;
}

//调用函数
let result = fn(1, 2);
console.log(result);
```

1. this 是静态的. this 始终指向函数声明时所在作用域下的 this 的值

   ```js
   function getName(){
       console.log(this.name);
   }
   
   let getName2 = () => {
       console.log(this.name);
   }
   
   //设置 window 对象的 name 属性
   window.name = '尚硅谷';
   
   const school = {
         name: "ATGUIGU"
   }
   
    //call 方法调用
   getName.call(school);  //ATGUIGU
   getName2.call(school); //尚硅谷
   ```

2. 不能作为构造实例化对象

   ```js
   //不能这么定义
   let Person = (name, age) => {
         this.name = name;
         this.age = age;
   }
   
   let me = new Person('xiao',30);
   console.log(me);
   ```

3. 不能使用 arguments 变量

   ```js
   let fn = () => {
        console.log(arguments);  //获取不到arguments
   }
   
   fn(1,2,3);
   ```

4. 箭头函数的简写

   ```js
   //(1) 省略小括号, 当形参有且只有一个的时候
   let add = n => {
     return n + n;
   }
   console.log(add(9));
   
   //(2) 省略花括号, 当代码体只有一条语句的时候, 此时 return 必须省略,而且语句的执行结果就是函数的返回值
   let pow = n => n * n;
   console.log(pow(8));
   ```



### 七、函数参数的默认值设置

ES6 允许给函数参数赋值初始值；

1. 形参初始值 具有默认值的参数, 一般位置要靠后(潜规则)

   ```js
   function add(a,b,c=10) {
        return a + b + c;
   }
   
   let result = add(1,2);
   console.log(result);
   ```

2. 与解构赋值结合

   ```js
   function connect({host="127.0.0.1", username,password, port}){
          console.log(host)
          console.log(username)
          console.log(password)
          console.log(port)
   }
   
   connect({
         host: 'atguigu.com',
         username: 'root',
         password: 'root',
         port: 3306
   })
   ```

   

### 八、rest参数

 ES6 引入 rest 参数，用于获取函数的实参，用来代替 arguments;

```js
// ES5 获取实参的方式
function date(){
    console.log(arguments);
}
date('白芷','阿娇','思慧');

// rest 参数
function date(...args){
    console.log(args); // filter some every map 
}
date('阿娇','柏芝','思慧');
```

注意：rest 参数必须要放到参数最后

```js
function fn(a,b,...args){
     console.log(a);
     console.log(b);
     console.log(args);
}
```



### 九、spread扩展运算符

『...』 扩展运算符能将『数组』转换为逗号分隔的『参数序列』

```js
//声明一个数组 ...
const tfboys = ['易烊千玺','王源','王俊凯'];
 
// 声明一个函数
function chunwan(){
       console.log(arguments);
}

chunwan(...tfboys);// 等于调用chunwan('易烊千玺','王源','王俊凯')
```

##### 应用

1. 数组的合并

   ```js
   const kuaizi = ['王太利','肖央'];
   const fenghuang = ['曾毅','玲花'];
   // const zuixuanxiaopingguo = kuaizi.concat(fenghuang);  concat实现
   const zuixuanxiaopingguo = [...kuaizi, ...fenghuang];
   console.log(zuixuanxiaopingguo);
   ```

2. 数组的克隆

   ```js
   const sanzhihua = ['E','G','M'];
   const sanyecao = [...sanzhihua];//  ['E','G','M']
   console.log(sanyecao);
   ```

   



### 十、Symbol 



#### 1. 简介

ES6 引入了一种新的原始数据类型 Symbol ，表示独一无二的值，最大的用法是用来定义对象的唯一属性名。ES6 数据类型除了 Number 、 String 、 Boolean 、 Object、 null 和 undefined ，还新增了 Symbol 。



#### 2. 基本用法

Symbol 函数栈不能用 new 命令，因为 Symbol 是原始数据类型，不是对象。可以接受一个字符串作为参数，为新创建的 Symbol 提供描述，用来显示在控制台或者作为字符串的时候使用，便于区分。

```js
let sy = Symbol("KK");
console.log(sy);   // Symbol(KK)
typeof(sy);        // "symbol"
 
// 相同参数 Symbol() 返回的值不相等
let sy1 = Symbol("kk"); 
sy === sy1;       // false
```



#### 3.使用场景

##### (1) 作为属性名

由于每一个 Symbol 的值都是不相等的，所以 Symbol 作为对象的属性名，可以保证属性不重名。；

```js
let sy = Symbol("key1");
 
// 写法1
let syObject = {};
syObject[sy] = "kk";
console.log(syObject);    // {Symbol(key1): "kk"}
 
// 写法2
let syObject = {
  [sy]: "kk"
};
console.log(syObject);    // {Symbol(key1): "kk"}
 
// 写法3
let syObject = {};
Object.defineProperty(syObject, sy, {value: "kk"});
console.log(syObject);   // {Symbol(key1): "kk"}
```

Symbol **作为对象属性名时不能用.运算符，要用方括号**。因为.运算符后面是字符串，所以取到的是字符串 sy 属性，而不是 Symbol 值 sy 属性。

```js
let syObject = {};
syObject[sy] = "kk";
 
syObject[sy];  // "kk"
syObject.sy;   // undefined
```



##### (2) 定义常量

Symbol 的值是唯一的，不会出现相同值得常量。

```js
//用字符串不能保证常量是独特唯一的
const COLOR_RED = "red";
const COLOR_YELLOW = "yellow";
const COLOR_BLUE = "blue";

//使用Symbol定义常量，这样就可以保证这一组常量的值都不相等
const COLOR_RED = Symbol("red");
const COLOR_YELLOW = Symbol("yellow");
const COLOR_BLUE = Symbol("blue");
```



##### (3) Symbol.for()

Symbol.for() 类似单例模式，首先会在全局搜索被登记的 Symbol 中是否有该字符串参数作为名称的 Symbol 值，如果有即返回该 Symbol 值，若没有则新建并返回一个以该字符串参数为名称的 Symbol 值，并登记在全局环境中供搜索。

```js
let yellow = Symbol("Yellow");
let yellow1 = Symbol.for("Yellow");
yellow === yellow1;      // false
 
let yellow2 = Symbol.for("Yellow");
yellow1 === yellow2;     // true
```



##### (4) Symbol.keyFor()

Symbol.keyFor() 返回一个已登记的 Symbol 类型值的 key ，用来检测该字符串参数作为名称的 Symbol 值是否已被登记。

```js
let yellow1 = Symbol.for("Yellow");
Symbol.keyFor(yellow1);    // "Yellow"
```



### 十一、迭代器

ES6 创造了一种新的遍历命令 for...of 循环，Iterator 接口主要供 for...of 消费。

ES6 规定，默认的 Iterator 接口部署在数据结构的Symbol.iterator属性上。

原生具备 Iterator 接口的数据结构如下：

* Array
* Map
* Set
* String
* TypedArray
* 函数的 arguments 对象
* NodeList 对象

```js
//声明一个数组
const xiyou = ['唐僧','孙悟空','猪八戒','沙僧'];

//使用 for...of 遍历数组
for(let v of xiyou){
     console.log(v);
}
```

```js
const xiyou = ['唐僧','孙悟空','猪八戒','沙僧'];
let iterator = xiyou[Symbol.iterator]();

//调用对象的next方法
console.log(iterator.next());  //{value:"唐僧",value:false}
console.log(iterator.next());  //{value:"孙悟空",value:false}
console.log(iterator.next());  //{value:"猪八戒",value:false}
console.log(iterator.next());  //{value:"沙僧",value:false}
console.log(iterator.next());  //{value:"undefined",value:true}
```


#### 自定义迭代器

1. 创建一个指针对象，指向当前数据结构的起始位置；
2. 第一次调用对象的next方法，指针自动指向数据结构的第一个成员；
3. 接下来不断调用 next 方法，指针一直往后移动，直到指向最后一个成员；
4. 每调用next方法返回一个包含 value 和 done 属性的对象；

```js
const banji = {
            name: "终极一班",
            stus: [
                'xiaoming',
                'xiaoning',
                'xiaotian',
                'knight'
            ],
            [Symbol.iterator]() {
            //索引变量
            let index = 0;
            //
            let _this = this;
            return {
                next: function () {
                    if (index < _this.stus.length) {
                        const result = { value: _this.stus[index], done: false };
                           //下标自增
                           index++;
                           //返回结果
                           return result;
                     }else{
                          return {value: undefined, done: true};
                     }
                  }
              };
       }
}

//遍历这个对象 
for (let v of banji) {
        console.log(v);
}
```



### 十二、生成器函数

generator又名生成器函数，它是一个崭新的函数类型，它和标准的普通函数完全不同。通过显式的调用生成器函数，能对应的产生一个新的值。通过多次调用后，产生一组值的序列，直到生成器告诉我们无法在产生新的值了。

每当生成器函数产生一个新值后，它的执行状态会被保留，直到下次请求到来，它就会从上次离开的位置恢复执行。


#### 1. 定义generator函数

```js
// 通过在function后面添加星号*来定义生成器函数
function* myGenerator() {
    // 使用关键字yield产生值
    yield "first";
    yield "second";
    yield "third";
}

// 生成迭代器gen
let gen = myGenerator();

console.log(gen.next()); //{value:"first",done:false} 
console.log(gen.next()); //{value:"second",done:false}
console.log(gen.next()); //{value:"third",done:false}
console.log(gen.next()); //{value:"undefined",done:true}
```

#### 2. 生成器函数的参数传递

我们可以通过yield来从生成器中产生多个值并返回。而且还可以向生成器发送值。

当我们调用next方法获取一个值时，我们可以对值进行一系列的操作，然后在处理完成时，再把计算结果传递给生成器。

```js
function * gen(arg){
console.log(arg); //AAA
let one = yield 111; //yield返回iterator.next()中的实参
console.log(one); //BBB
let two = yield 222;
console.log(two); //CCC
let three = yield 333;
console.log(three); //DDD
}

//执行获取迭代器对象
let iterator = gen('AAA');
console.log(iterator.next()); //{value:"111",done:false} 
//next方法可以传入实参
console.log(iterator.next('BBB')); //{value:"222",done:false} 
console.log(iterator.next('CCC')); //{value:"333",done:false} 
console.log(iterator.next('DDD')); //{value:"undefined",done:true} 
```



#### 3. 生成器函数实例

```js
function getUsers(){
	setTimeout(()=>{
		let data = '用户数据';
		//调用 next 方法, 并且将数据传入
		iterator.next(data);
	}, 1000);
}

function getOrders(){
	setTimeout(()=>{
		let data = '订单数据';
		iterator.next(data);
	}, 1000)
}

function getGoods(){
	setTimeout(()=>{
		let data = '商品数据';
		iterator.next(data);
	}, 1000)
}

function * gen(){
	let users = yield getUsers();
    console.log(users)
    let orders = yield getOrders();
    console.log(orders)
    let goods = yield getGoods();
    console.log(goods)
}
//调用生成器函数
let iterator = gen();
iterator.next();

//输出
//用户数据
//订单数据
//商品数据
```





### 十三、Promise

#### 1. 基本语法

```js
//实例化 Promise 对象
 const p = new Promise(function(resolve, reject){
       setTimeout(function(){
             //let data = '数据库中的用户数据';
             //resolve(data);

             let err = '数据读取失败';
             reject(err);
       }, 1000);
 });

//调用 promise 对象的 then 方法
p.then(function(value){ //调用ressolve方法时，回调该方法
     console.log(value);
}, function(reason){  //调用reject方法时，回调该方法
     console.error(reason);
})
```



#### 2. then方法

then方法的返回结果是 Promise 对象, 对象状态由回调函数的执行结果决定；

如果回调函数中返回的结果是 非 promise 类型的属性, 状态为成功, 返回值为对象的成功的值

```js
//创建 promise 对象
const p = new Promise((resolve, reject)=>{
      setTimeout(()=>{
            resolve('用户数据');
         }, 1000)
});

const result = p.then(value => {
    console.log(value);
    //1. 非 promise 类型的属性
    // return 'iloveyou';
    //2. 是 promise 对象
    // return new Promise((resolve, reject)=>{
    //     // resolve('ok');
    //     reject('error');
    // });
    //3. 抛出错误
    // throw new Error('出错啦!');
    throw '出错啦!';
}, reason => {
    console.warn(reason);
});
```

