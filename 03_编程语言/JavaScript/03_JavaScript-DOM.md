### 一、什么是DOM

DOM，全称Document Object Model文档对象模型。JS中通过DOM来对HTML文档进行操作。只要理解了DOM就可以随心所欲的操作WEB页面。

* **文档**

  文档表示的就是整个的HTML网页文档

* **对象**

  对象表示将网页中的每一个部分都转换为了一个对象。

* **模型** 

  使用模型来表示对象之间的关系，这样方便我们获取对象。



### 二、节点

1. 节点Node，是**构成我们网页的最基本的单元**，网页中的每一个部分都可以称为是一个节点。比如：html标签、属性、文本、注释、整个文档等都是一个节点。

2. 虽然都是节点，但是实际上他们的具体类型是不同的。比如：标签我们称为元素节点、属性称为属性节点、文本称为文本节点、文档称为文档节点。

   

#### 常用节点

**文档节点**：整个HTML文档；

**元素节点**：HTML文档中的HTML标签；

**属性节点**：元素的属性；

**文本节点**：HTML标签中的文本内容；



#### 1. 文档节点

浏览器已经为我们提供文档节点，对象是window属性，可以在页面中直接使用，文档节点代表的是整个网页；

```javascript
<body>
<button id="btn">示例按钮</button>
<script type="text/javascript">
    //获取到butoon对象；
    var btn = document.getElementById("btn");
    console.log(btn);

    //修改按钮文字
    btn.innerHTML = "I'm Button";
</script>
</body>
```



#### 2.元素节点

通过document对象调用；

* getElementByid() : 通过id属性获取一个元素节点对象；
* getElementsByTagName()：通过标签名获取一组元素节点对象；
* getElementsByName()：通过name属性获取一组元素节点对象；

**获取元素节点的子节点**

* 通过具体的元素节点调用;

  * getElementsByTagName() : 方法，返回当前节点的指定标签名后代节点；
  * childNodes(): 属性，表示当前节点的所有子节点;
  * firstChild(): 属性，表示当前节点的第一个子节点;
  * lastChild(): 属性，表示当前节点的最后一个子节点;



**获取父节点和兄弟节点**

* 通过具体的节点调用;
  * parentNode(): 属性，表示当前节点的父节点;
  * previousSibling(): 属性，表示当前节点的前一个兄弟节点;
  * nextSibling(): 属性，表示当前节点的后一个兄弟节点;



**示例**

  ```html
<body>
<div id="total">
    <div class="inner">
        <p>你喜欢哪个城市?</p>

        <ul id="city">
            <li id="bj">北京</li>
            <li>上海</li>
            <li>东京</li>
            <li>首尔</li>
        </ul>
        <br>
        <br>

        <p>你喜欢哪款单机游戏?</p>

        <ul id="game">
            <li id="rl">红警</li>
            <li>实况</li>
            <li>极品飞车</li>
            <li>魔兽</li>
        </ul>

        <br/>
        <br/>

        <p>你手机的操作系统是?</p>
        <ul id="phone">
            <li>IOS</li>
            <li id="android">Android</li>
            <li>Windows Phone</li>
        </ul>
    </div>

    <div class="inner">
        gender:
        <input class="hello" type="radio" name="gender" value="male"/>
        Male
        <input class="hello" type="radio" name="gender" value="female"/>
        Female
        <br>
        <br>
        name:
        <input type="text" name="name" id="username" value="abcde"/>
    </div>
</div>
</body>
  ```

1. 查找#city下所有li节点# getElementsByTagName();

  ~~~javascript
  var btn04 = document.getElementById("btn04");
  btn04.onclick = function () {
      //获取id为city的元素
      var city = document.getElementById("city");
  
      //查找city下所有li节点
      var list = city.getElementsByTagName("li");
  
      for (let i = 0; i < list.length; i++) {
          console.log(list[i].innerHTML);
      }
  }
  
  //输出
  北京
  上海
  东京
  首尔
  ~~~

  

2. 返回#city的所有子节点

```javascript
var btn05 = document.getElementById("btn05");
btn05.onclick = function () {
    //获取id为city的元素
    var city = document.getElementById("city");
    //childNodes属性会获取包括文本节点在内的所有节点，DOM标签间的空白也会当成文本节点；
    var cns = city.childNodes;
    console.log(cns.length);
}

//输出：9
```



3. 返回#phone的第一个子节点

```javascript
var btn06 = document.getElementById("btn06");
btn06.onclick = function () {
    //获取id为city的元素
    var phone = document.getElementById("phone");
    //childNodes属性会获取包括文本节点在内的所有节点，DOM标签间的空白也会当做文本节点；
    var fir = phone.firstChild;
    console.log(fir);
}
//输出：#text
```



4. 返回#bj的父节点

```javascript
//定义统一函数
function myClick(idStr, fun) {
    var btn = document.getElementById(idStr);
    btn.onclick = fun;
}
```

```javascript
myClick("btn07", function () {
    var bj = document.getElementById("bj");
    var parentNode = bj.parentNode;
    //显示标签里面的内容；
    alert(parentNode.innerHTML);

    //与innerHTML不同的是，它会自动将html去除；
    alert(parentNode.innerText);
});
```



5. 返回#android的前一个兄弟节点

```javascript

myClick("btn08", function () {
    var bj = document.getElementById("android");
    var previousSibling = bj.previousSibling;
    alert(previousSibling.innerHTML);
});
```



6. 读取#username的value属性值

```javascript
myClick("btn09", function () {
    var um = document.getElementById("username");
    //文本框value属性值，就是文本框中填写的内容；
    alert(um.value);

    um.value = "今天天气不错";
    alert("修改完成");
});
```



7. 设置#username的value属性值

```javascript
myClick("btn10", function () {
    var um = document.getElementById("username");
    //设置value属性值
    um.value = "今天天气不错";
});
```





### 三、事件

事件，就是文档或浏览器窗口中发生的一 些特定的交互瞬间。

1. JavaScript与HTML之间的交互是通过事 件实现的。

2. 对于 Web 应用来说，有下面这些代表性的 事件：点击某个元素、将鼠标移动至某个 元素上方、按下键盘上某个键，等等。

```javascript
<button id="btn" onclick="alert('点击按钮')">示例按钮</button>
```

上面写法耦合，不推荐使用；

可以为按钮的对应事件绑定处理函数的形式来响应事件；

```javascript
<body>
<button id="btn">示例按钮</button>
<script type="text/javascript">
    //获取到butoon对象；
    var btn = document.getElementById("btn");

    btn.onclick = function (){
        alert("按钮被点击");
    }
</script>
</body>
```





### 四、文档的加载

浏览器在加载一个页面时，是按照自上而下的顺序加载的，读到一行就运行一行。

如果将script标签写到页面上边，在代码执行时，页面还没有被加载，页面还没有加载DOM对象，执行script代码时会导致获取不到DOM对象；

onload事件会在整个页面加载完成后才触发，为window绑定一个onload事件，该事件对应的响应函数将会在页面加载完成后执行，这样可以确保我们的代码执行时所有的DOM对象已经加载完毕。

```javascript
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Hello</title>

    <script type="text/javascript">
        window.onload = function () {
            var btn = document.getElementById("btn");
            btn.onclick = function () {
                alert("按钮被点击了");
            }
        }
    </script>

</head>
<body>
<button id="btn">示例按钮</button>
</body>
</html>
```



### 五、图片切换示例

```javascript
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>

    <style type="text/css">
        * {
            margin: 0;
            padding: 0;
        }

        #outer {
            width: 500px;
            margin: 50px auto;
            padding: 10px;
            background-color: yellowgreen;
            text-align: center;
        }
    </style>

    <script type="text/javascript">

        window.onload = function () {
            //获取两个按钮
            var prev = document.getElementById("prev");
            var next = document.getElementById("next");

            //获取img标签
            var img = document.getElementsByTagName("img")[0];

            //创建一个数组，用来保存图片的路径
            var imgArr = ["img/1.jpg", "img/2.jpg", "img/3.jpg", "img/4.jpg", "img/5.jpg"];

            //创建一个变量，来保存当前正在显示的图片的索引
            var index = 0;

            //获取id为info的p元素
            var info = document.getElementById("info");
            //设置提示文字
            info.innerHTML = "一共 " + imgArr.length + " 张图片，当前第 " + (index + 1) + " 张";

            //分别为两个按钮绑定单击响应函数
            prev.onclick = function () {
                index--;
                //判断index是否小于0
                if (index < 0) {
                    index = imgArr.length - 1;
                }

                img.src = imgArr[index];

                //当点击按钮以后，重新设置信息
                info.innerHTML = "一共 " + imgArr.length + " 张图片，当前第 " + (index + 1) + " 张";
            };

            next.onclick = function () {
                index++;
                if (index > imgArr.length - 1) {
                    index = 0;
                }

                //切换图片就是修改img的src属性
                //要修改一个元素的属性 元素.属性 = 属性值
                img.src = imgArr[index];

                //当点击按钮以后，重新设置信息
                info.innerHTML = "一共 " + imgArr.length + " 张图片，当前第 " + (index + 1) + " 张";
            };
        };
    </script>

</head>

<body>
<div id="outer">
    <p id="info"></p>
    <img src="img/1.jpg" alt="冰棍"/>
    <button id="prev">上一张</button>
    <button id="next">下一张</button>
</div>
</body>
</html>
```



### 六、节点的修改

**创建节点**

* document.createElement(标签名)

**删除节点**

* 父节点.removeChild(子节点)

**替换节点**

* 父节点.replaceChild(新节点 , 旧节点)

**插入节点**

* 父节点.appendChild(子节点)
* 父节点.insertBefore(新节点 , 旧节点）



#### 示例代码

```html
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01//EN" "http://www.w3.org/TR/html4/strict.dtd">
<html>
<head>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
    <title>Untitled Document</title>
    <link rel="stylesheet" type="text/css" href="style/css.css"/>
    <script type="text/javascript">

        function myClick(idStr, fun) {
            var btn = document.getElementById(idStr);
            btn.onclick = fun;
        }

        window.onload = function () {
            //创建“广州”节点，添加到city下
            myClick("btn01",function (){
              var city = document.getElementById("city");
              //用于创建一个元素节点对象；
              //它需要一个标签名作为参数，将会根据该标签名创建元素节点对象，并将创建好的对象作为返回值返回
              var li = document.createElement("li");

              //用于创建一个文本节点对象
              //需要一个文本内容作为参数，将会根据该内容创建文本节点，并将新的节点返回；
              var text = document.createTextNode("广州");

              //父节点中添加一个新的子节点；
              li.appendChild(text);
              city.appendChild(li);
            });

            //将“广州”节点插入到bj前面
            myClick("btn02",function (){
                var li = document.createElement("li");
                var text = document.createTextNode("广州");
                li.appendChild(text);

                var bj = document.getElementById("bj");
                var city = document.getElementById("city");
                city.insertBefore(li,bj);
            });

            //使用“广州”节点替换bj节点
            myClick("btn03",function (){
                var li = document.createElement("li");
                var text = document.createTextNode("广州");
                li.appendChild(text);

                var bj = document.getElementById("bj");
                var city = document.getElementById("city");
                city.replaceChild(li,bj);
            });

            //删除bj节点
            myClick("btn04",function (){
                var bj = document.getElementById("bj");
                var city = document.getElementById("city");
                city.removeChild(bj);

                //bj.parentNode.removeChild(bj);
            });

            //读取city内的HTML代码
            myClick("btn05",function (){
                var city = document.getElementById("city");
                alert(city.innerHTML);
            });

            //设置bj内的HTML代码,添加一个广州标签；
            myClick("btn06",function (){
                var city = document.getElementById("city");
                city.innerHTML += "<li>广州</li>";
            });
        };


    </script>
</head>
<body>
<div id="total">
    <div class="inner">
        <p>你喜欢哪个城市?</p>

        <ul id="city">
            <li id="bj">北京</li>
            <li>上海</li>
            <li>东京</li>
            <li>首尔</li>
        </ul>
        <br>
        <br>
    </div>
</div>
<div id="btnList">
    <div>
        <button id="btn01">创建“广州”节点，添加到city下</button>
    </div>
    <div>
        <button id="btn02">将“广州”节点插入到bj前面</button>
    </div>
    <div>
        <button id="btn03">使用“广州”节点替换bj节点</button>
    </div>
    <div>
        <button id="btn04">删除bj节点</button>
    </div>
    <div>
        <button id="btn05">读取city内的HTML代码</button>
    </div>
    <div>
        <button id="btn06">设置bj内的HTML代码</button>
    </div>
</div>
</body>
</html>
```



### 七、获取style属性



#### 1. 读取内联样式

通过style属性设置和读取的都是内联样式，无法读取样式表中的样式；

```javascript
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>样式</title>

    <style type="text/css">
        #box1 {
            width: 200px;
            height: 200px;
            background-color: red;
        }
    </style>

    <script type="text/javascript">
        window.onload = function () {
            var box1 = document.getElementById("box1");

            var btn1 = document.getElementById("btn01");
            btn1.onclick = function () {
                //通过JS修改元素样式
                //语法：元素.style.样式名 = 样式值;
                box1.style.width = "400px";
                box1.style.height = "400px";
                box1.style.backgroundColor = "yellow";
            };

            var btn2 = document.getElementById("btn02");
            btn2.onclick = function (){
                //读取样式值
                alert(box1.style.height);
            };
        };
    </script>
</head>

<body>
<div id="box1"></div>
<button id="btn01">按钮1</button>
<button id="btn02">读取样式值</button>
</body>
</html>
```



#### 2. 读取样式表样式

* 通过currentStyle可以获取样式表样式值，但只有IE浏览器支持；
* 在其他浏览器中可以使用getComputedStyle()方法获取当前元素的样式；
  * 该方法为window方法，可以直接使用；
  * 该方法会返回一个对象，对象中封装了当前元素对应的样式；

```javascript
var btn2 = document.getElementById("btn02");
btn2.onclick = function () {
    var style = getStyle(box1, "width");
    alert(style);
};

function getStyle(obj, name) {
    if (window.getComputedStyle) {
        //其他浏览器
        return getComputedStyle(obj, null)[name];
    } else {
        //IE浏览器
        return obj.currentStyle[name];
    }
}
```

#### 3. 样式的其他属性

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>样式</title>

    <style type="text/css">
        #box1 {
            width: 200px;
            height: 200px;
            background-color: red;
            padding: 10px;
            border: 10px solid yellow;
        }

        #box2 {
            width: 200px;
            height: 300px;
            background-color: #ffa;
            overflow: auto;
        }

        #box3 {
            width: 150px;
            height: 600px;
            background-color: rebeccapurple;
        }

    </style>

    <script type="text/javascript">
        window.onload = function () {
            var box1 = document.getElementById("box1");
            var btn1 = document.getElementById("btn01");

            var box2 = document.getElementById("box2");
            var box3 = document.getElementById("box3");

            btn1.onclick = function () {
                // 1.获取可见高度,包括内容区和内边距，不包括边框；
                // 2.属性只读，不可修改；
                console.log(box1.clientHeight); //输出220

                //获取整个元素的高度，包括边框；
                console.log(box1.offsetHeight); //输出240

                //offsetTop 当前元素相对于其定位父元素的垂直偏移量
                console.log(box1.offsetTop);

                //scrollHeight：获取元素整个滚动高度
                console.log("box2: scrollHeight:" + box2.scrollHeight); //600
                console.log("box2: clientHeight:" + box2.clientHeight); //300;

                //scrollTop：获取垂直滚动条滚动的距离
                console.log("box2: scrollTop:"+box2.scrollTop);
            };

            //scrollHeight - scrollTop = clientHeight 表示滚动到底部
            box2.onscroll = function (){
              if (box2.scrollHeight-box2.scrollTop === box2.clientHeight){
                  alert("滑动到底部");
              }
            };
        };

    </script>

</head>
<body>
<div id="box1"></div>
<br/>
<br/>
<div id="box2">
    <div id="box3"></div>
</div>

<br/>
<br/>
<button id="btn01">按钮1</button>
</body>
</html>
```

