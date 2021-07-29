生成器函数实例

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





### Promise

基本语法

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

#### then方法

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

