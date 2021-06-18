### 一、Flex简介

1. flex(弹性盒、伸缩盒)，是CSS中的又一种布局手段，它主要用来代替浮动来完成页面的布局；

2. flex可以使元素具有弹性，让元素可以跟随页面的大小的改变而改变；

3. **弹性容器**

   * 要使用弹性盒，必须先将一个元素设置为弹性容器；(通过 display 来设置弹性容器)
   * display:flex  设置为块级弹性容器；
   * display:inline-flex 设置为行内的弹性容器；

4. **弹性元素**

   弹性容器的子元素是弹性元素（弹性项）；

   弹性元素可以同时是弹性容器；

### 二、弹性元素的属性

1. flex-grow 指定弹性元素的伸展的系数；
   * 当父元素有多余空间的时，子元素如何伸展；
   * 父元素的剩余空间，会按照比例进行分配；
2. flex-shrink 指定弹性元素的收缩系数；
   * 当父元素中的空间不足以容纳所有的子元素时，如果对子元素进行收缩；

##### 使用flex创建导航条

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <link rel="stylesheet" href="css/reset.css">
    <style>
        
        .nav {
            width: 1210px;
            height: 48px;
            line-height: 48px;
            margin: 50px auto;
            background-color: #E8E7E3;
            display: flex;
        }

        .nav li {
            flex-grow: 1;
        }

        .nav a {
            text-align: center;
            display: block;
            color: #808080;
            text-decoration: none;
            font-size: 16px;
        }

        .nav a:hover {
            background-color: #636363;
            color: #ffffff;
        }
    </style>
</head>
<body>
<ul class="nav">
    <li><a href="#">HTML/CSS</a></li>
    <li><a href="#">Browser side</a></li>
    <li><a href="#">Server Side</a></li>
    <li><a href="#">Programming</a></li>
    <li><a href="#">XML</a></li>
    <li><a href="#">web building</a></li>
    <li><a href="#">Reference</a></li>
</ul>
</body>
</html>
```

