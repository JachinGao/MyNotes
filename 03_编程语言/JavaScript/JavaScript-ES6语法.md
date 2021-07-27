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

