## 事件

### 一、事件对象

当事件响应函数被触发时，浏览器每次都会将一个事件对象作为实参 传递进响应函数；

* onmousemove()：跟随鼠标移动

```javascript
<html>
<head>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
    <title>Insert title here</title>
    <style type="text/css">

        #areaDiv {
            border: 1px solid black;
            width: 300px;
            height: 50px;
            margin-bottom: 10px;
        }

        #showMsg {
            border: 1px solid black;
            width: 300px;
            height: 20px;
        }

    </style>
    <script type="text/javascript">

        window.onload = function () {
            var areaDiv = document.getElementById("areaDiv");
            var showMsg = document.getElementById("showMsg");

            //在IE8及以下的浏览器中，是将事件对象作为window对象的属性保存的；
            areaDiv.onmousemove = function (event) {
                //适配浏览器
                event = event || window.event;
                var x = event.pageX;
                var y = event.pageY;
                showMsg.innerHTML = "x=" + x + " y=" + y;
            };
        }

    </script>
</head>
<body>

<div id="areaDiv"></div>
<div id="showMsg"></div>

</body>
</html>
```



### 二、跟随鼠标移动

代码示例

```javascript
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>

    <style>
        #box1 {
            width: 100px;
            height: 100px;
            background-color: red;
            position: absolute;
        }
    </style>

    <script type="text/javascript">
        window.onload = function () {
            var box1 = document.getElementById("box1");
            document.onmousemove = function (ev) {
                /**
                 * 获取滚动条滚动距离
                 * chrome认为浏览器的滚动条是body的，可以通过body.scrollTop来获取，
                 * 火狐等浏览器认为浏览器滚动条是html的；
                 */

                var st = document.body.scrollTop ||
                    document.documentElement.scrollTop;

                var sl = document.body.scrollLeft ||
                    document.documentElement.scrollLeft;
                //pageX和pageY在I8中不支持，考虑兼容时需考虑其他方案；
                var left = ev.clientX;
                var top = ev.clientY;

                box1.style.left = left + sl + "px";
                box1.style.top = top + st + "px";
            };
        };
    </script>

</head>
<body style="height: 2500px">
<div id="box1"></div>

</body>
</html>
```



### 三、事件冒泡

1. 冒泡就是事件向上传导，当后台元素上的事件被触发时，其祖先元素的相同事件也会被触发;

2. 如果不希望发生事件冒泡，可以通过事件对象来取消冒泡；
3. 将事件对象的cancelBubble设置为true，即可取消冒泡；

```javascript
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style type="text/css">
        #box1 {
            width: 200px;
            height: 200px;
            background-color: yellowgreen;
        }

        #s1 {
            width: 80px;
            height: 80px;
            background-color: yellow;
        }
    </style>

    <script type="text/javascript">
        window.onload = function () {
            var s1 = document.getElementById("s1");
            s1.onclick = function (ev) {
                alert("我是span的单击响应函数");
                //取消冒泡
                ev = ev || window.event;
                ev.cancelBubble = true;
            }

            var box = document.getElementById("box1");
            box.onclick = function (ev) {
                alert("我是box单击响应函数");
                ev = ev || window.event;
                ev.cancelBubble = true;
            }

            document.body.onclick = function () {
                alert("我是body单击响应函数");
            }
        }
    </script>
</head>

<body>
<div id="box1">
    我是box
    <span id="s1">我是span</span>
</div>
</body>
</html>
```



### 四、事件的委派

1. 指将事件统一绑定给元素的共同祖先元素，这样当后代元素上的事件触发时，会一直冒泡到祖先元素，从而通过祖先元素的响应函数来处理事件；
2. 事件委派利用了冒泡，通过委派可以减少事件绑定的次数，提高程序性能；

```javascript
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <script type="text/javascript">
        window.onload = function () {
            var btn = document.getElementById("btn1");
            btn.onclick = function () {
                var u1 = document.getElementById("u1");
                var li = document.createElement("li");
                li.innerHTML = "<a href='javascript:' class='link'>新建超链接</a>";
                u1.appendChild(li);
            };

            var u1 = document.getElementById("u1");
            u1.onclick = function (ev) {
                ev = ev || window.event;
                if (ev.target.className === "link") {
                    alert("单击响应函数");
                }
            };
        };

    </script>

</head>
<body>
<button id="btn1">添加按钮</button>
<ul id="u1">
    <li><a href="javascript:" class="link">超链接一</a></li>
    <li><a href="javascript:" class="link">超链接二</a></li>
    <li><a href="javascript:" class="link">超链接三</a></li>

</ul>
</body>
</html>
```



### 五、事件的绑定

1. 使用 对象.onclick = function(){} 的形式绑定函数，它只能同时为一个元素的一个事件绑定一个相应函数，不能绑定多个，如果绑定了多个，则后面事件会覆盖掉前面的事件；

2. 使用addEventListener可以为一个元素的相同事件同时绑定多个相应函数；当事件触发时，相应函数会按照函数的绑定顺序依次执行；
3. 在IE8中，需要使用attachEvent()来绑定事件；

```javascript
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>

    <script type="text/javascript">
        window.onload = function () {
            var btn = document.getElementById("btn_01");
            /**
             * 参数：
             * 1.事件的字符串，不要on
             * 2.回调函数，当事件触发时该函数被调用；
             * 3.是否在捕获阶段触发事件，需要一个布尔值，一般都传false；
             */
            btn.addEventListener("click", function () {
                alert("事件一");
            }, false);

            btn.addEventListener("click", function () {
                alert("事件二");
            }, false);

            btn.addEventListener("click", function () {
                alert("事件三");
            }, false);
        };
    </script>
</head>
<body>
<button id="btn_01">按钮</button>
</body>
</html>
```

```javascript
//可以统一定义绑定函数
function bind(obj, eventStr, callback) {

    if (obj.addEventListener) {
        //大部分浏览器兼容
        obj.addEventListener(eventStr, callback, false);
    } else {
        //IE8及以下
        obj.attachEvent("on" + eventStr, function () {
            callback.call(obj);
        });
    }
}
```



### 六、拖拽

```javascript
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>

    <style type="text/css">
        #box1 {
            width: 100px;
            height: 100px;
            background-color: red;
            position: absolute;
        }

        #box2 {
            width: 100px;
            height: 100px;

            left: 200px;
            top: 200px;
            background-color: yellow;
            position: absolute;
        }

    </style>

    <script type="text/javascript">
        window.onload = function () {
            var box = document.getElementById("box1");
            box.onmousedown = function (ev) {
                ev = ev || window.event;
                var ol = ev.clientX - box.offsetLeft;
                var ot = ev.clientY - box.offsetTop;

                document.onmousemove = function (ev) {
                    ev = ev || window.event;
                    var left = ev.clientX - ol;
                    var top = ev.clientY - ot;

                    box.style.left = left + "px";
                    box.style.top = top + "px";
                };

                document.onmouseup = function () {
                    document.onmousemove = null;
                    document.onmouseup = null;
                }
                /**
                 * 当我们拖拽一个网页内容时，浏览器会默认去搜索引擎中搜索内容；
                 * 此时会导致拖拽功能异常，这是浏览器提供的默认行为；
                 * 如果不希望发生，可以通过return false 来取消默认行为；
                 */
                return false;
            };
        };
    </script>
</head>
<body>

我是一段文字
<div id="box1"></div>
<div id="box2"></div>
</body>
</html>
```





### 七、鼠标滚轮事件

```javascript
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>

    <style type="text/css">
        #box1 {
            width: 100px;
            height: 100px;
            background-color: red;
        }
    </style>

    <script type="text/javascript">
        window.onload = function () {
            var box = document.getElementById("box1");

            //火狐不支持onmousewheel属性，需要使用DOMMouseScroll
            box.onmousewheel = function (event) {
                event = event || window.event;
                /**
                 * wheelDelta 获取鼠标滚轮滚动方向
                 * 向上滚120 向下滚-120
                 *
                 * 在火狐中使用event.detail获取滚动方向
                 * 向上滚-3 向下滚3
                 */
                if (event.wheelDelta > 0 || event.detail < 0) {
                    box.style.height = box.clientHeight - 10 + "px";
                } else {
                    box.style.height = box.clientHeight + 10 + "px";
                }

                //适配火狐
                bind(box, "DOMMouseScroll", box.onmousewheel);
                event.preventDefault() && event.preventDefault();

                /**
                 * 当滚轮滚动时，如果浏览器有滚动条，滚动条会随之滚动，这是浏览器的默认行为；
                 * 如果不希望发生，则可以取消默认行为；
                 */
                return false;
            };
        }

        function bind(obj, eventStr, callback) {

            if (obj.addEventListener) {
                //大部分浏览器兼容
                obj.addEventListener(eventStr, callback, false);
            } else {
                //IE8及以下
                obj.attachEvent("on" + eventStr, function () {
                    callback.call(obj);
                });
            }
        }
    </script>
</head>
<body style="height: 2000px">
<div id="box1"></div>
</body>
</html>
```



### 八、键盘事件

键盘事件一般都会绑定给一些可以获取到焦点的对象；

* onkeydown：按键被按下，如果一直按着不松手，则事件会一直触发；为防止误操作，第一次和第二次之间间隔会稍微长些；
* onkeyup： 按键被松开；

```javascript
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>

    <script type="text/javascript">
        window.onload = function () {

            var input = document.getElementsByTagName("input")[0];
            input.onkeydown = function (event) {
                console.log(event.key);

                //使文本框不能输入数字
                if (event.key >= '0' && event.key <= '9') {
                    return false;
                }

                //在文本框输入内容，属于onkeydown的默认行为；
                //如果取消默认行为(false)，则输入内容不会出现在文本框中；
                return true;
            };

        };
    </script>
</head>
<body>
<input type="text"/>
</body>
</html>
```



上下左右按键控制方块移动；

```javascript
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>

    <style type="text/css">
        #box1 {
            width: 20px;
            height: 20px;
            background-color: red;
            position: absolute;
        }
    </style>

    <script type="text/javascript">
        //使用键盘控制div
        window.onload = function () {
            var box = document.getElementById("box1");

            document.onkeydown = function (event) {
                event = event || window.event;
                console.log(event.keyCode);

                //当用户按了ctrl，加速
                var speed = 10;
                if (event.ctrlKey) {
                    speed = 30;
                }

                switch (event.keyCode) {
                    case 37:
                        box.style.left = box.offsetLeft - 10 - speed + "px";
                        break;
                    case 39:
                        box.style.left = box.offsetLeft + 10 + speed + "px";
                        break
                    case 38:
                        box.style.top = box.offsetTop - 10 - speed + "px";
                        break;
                    case 40:
                        box.style.top = box.offsetTop + 10 + speed + "px";
                        break;
                }
            }
        };
    </script>
</head>
<body>
<div id="box1"></div>
</body>
</html>
```

