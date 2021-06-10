### 一、BOM简介

BOM：浏览器对象模型；

1. BOM可以使我们通过JS来操作浏览器；

2. 在BOM中为我们提供了一组对象，用来完成对浏览器的操作；

3. BOM对象

   这个BOM对象在浏览器中都是作为window对象的属性保存的，可以通过window对象来使用，也可以直接使用；

   * window
     * 代表整个浏览器的窗口，同时window也是网页中的全局对象；
   * navigator
     * 代表当前浏览器的信息，通过该对象可以来识别不同的浏览器；
   * location
     * 代表当前浏览器的地址栏信息，通过location可以获取地址栏信息，或者操作浏览器跳转页面；
   * history
     * 代表浏览器的历史记录，可以通过该对象来操作浏览器的历史记录；
     * 由于隐私原因，该对象不能获取到具体的历史记录，只能操作浏览器向前或向后翻页；
   * screen
     * 代表用户屏幕的信息，通过该对象可以获取到用户的显示器相关信息；

   

### 二、Navigator

navigator 对象包含了浏览器的版本、浏览 器所支持的插件、浏览器所使用的语言等 各种与浏览器相关的信息。

我们有时会使用navigator的userAgent属 性来检查用户浏览器的版本。



### 三、History

history 对象保存着用户上网的历史记录， 从窗口被打开的那一刻算起。

* **go()：**使用 go() 方法可以在用户的历史记录中任意跳 转，可以向后也可以向前。

* **back()：**向后跳转

* **forward()：**向前跳转



### 四、Location

location对象提供了与当前窗口中加载的文档有关的信息，还提供了一些导航功能。

```javascript
<!DOCTYPE html>
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <script type="text/javascript">
        window.onload = function () {
            var btn = document.getElementById("btn1");
            btn.onclick = function () {
                location = "https://www.baidu.com";
            };

            var btn2 = document.getElementById("btn2");
            btn2.onclick = function () {
                //用来跳转到其他页面，作用和直接修改location一样；
                location.assign("https://www.baidu.com");
            };

            var btn3 = document.getElementById("btn3");
            btn3.onclick = function () {
                //和刷新按钮一样
                location.reload(true);
            };

            var btn4 = document.getElementById("btn4");
            btn4.onclick = function () {
                //可以使用一个新的页面替换当前页面，不会生成历史记录，不能使用回退按钮回退；
                location.replace("https://www.baidu.com");
            };


        };
    </script>
</head>
<body>
<button id="btn1">按钮</button>
<button id="btn2">按钮2</button>
<button id="btn3">按钮3</button>
<button id="btn4">按钮4</button>

</body>
</html>
```





### 五、定时器

setInterval():  每隔一段时间执行指定代码;

* 需要两个参数：
  * 要执行的代码；
  * 间隔的时间（单位毫秒）

clearInterval(): 取消间隔调用；

setTimeout():  延时调用，隔一段时间再执行，而且只会执行一次；

clearTimeout()：关闭延时调用；

```javascript
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>

    <script type="text/javascript">
        window.onload = function () {
            //获取count
            var count = document.getElementById("count");
            var num = 1;

            var timer = setInterval(function () {
                count.innerHTML = num;
                num++;

                if (num === 11) {
                    clearInterval(timer);
                }
            }, 1000);
        };

    </script>
</head>
<body>
<h1 id="count">1</h1>
</body>
</html>
```



