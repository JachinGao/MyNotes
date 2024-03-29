### 一、网页优点

* 不需要安装

* 无需更新

* 跨平台

网页中使用的语言： HTML、CSS、JavaScript



### 二、浏览器

浏览器负责将网页渲染成我们想要的样子；



### 三、W3C（万维网联盟，World Wide Web Consortium）

w3c的出现为了制定网页开发的标准，以使同一个页面在不同的浏览器中有相同的效果。



### 四、网页的结构

根据W3C的标准，一个网页主要由三部分组成：结构、表现和行为。

* **结构**：HTML用于描述页面的结构；
* **表现**：CSS用于控制页面中元素的样式；
* **行为**：JavaScript用于响应用户操作；



### 五、HTML (Hypertext Markup Language)

1. 它负责网页的三个要素中的结构；
2. HTML使用标签的形式来标识网页中的不同组成部分；
3. 超文本指的是超链接，使用超链接可以让我们从一个页面跳转到另一个页面；



### 六、标签

```html
<html lang="en">  //一个根标签
	<head>  //根标签包含一个head标签和一个body标签  
	</head>    
	<body>
	</body>
</html>
```



##### 属性

在开始标签或自结束标签可以设置属性；

1. 属性是一个名值对；
2. 属性用来设置标签中的内容如何显示；

```html
<h1>我的<font color="yellow">第一个</font>网页</h1>
```



### 七、文档声明

文档声明用来告诉浏览器当前网页的版本；

```html
//html5的文档声明；
<!DOCTYPE html>
```



### 八、字节编码

通过meta标签来设置网页的字符集，避免乱码问题；

```html
<meta charset="UTF-8">
```



### 九、文档使用

```html
<!DOCTYPE html>
<!--html的根标签，网页中的所有内容都要写在根元素里面-->
<html lang="en">

<!--head是网页头部，head中的内容不会再网页中直接出现，主要用来帮助浏览器或搜索引擎来解析网页-->
<head>
    <!-- meta标签用来设置网页元数据，这里meta用来设置网页的字符集，避免乱码问题   -->
    <meta charset="UTF-8">
    <!--title中的内容会显示在浏览器标题栏-->
    <title>Title</title>
</head>

<!--body是html的子元素，表示网页的主体，网页中所有的可见内容都应该写在body里-->
<body>
<!--网页的一级标题-->
<h1>我的<font color="yellow">第一个</font>网页</h1>
</body>
</html>
```



### 十、实体

在网页中编写的多个空格默认情况会自动被浏览器解析为一个空格；

1. 如果我们需要在网页中书写特殊字符，需要使用html中的实体（转义字符）；

2. 实体的语法

   &实体的名字；

   * &nbsp； 空格
   * &gt； 大于号
   * &lt； 小于号
   * &copy； 版权符号



### 十一、meta标签

1. charset：指定网页的字符集；

2. name：指定的数据的名称；

3. content：指定的数据的内容；

4. keywords：表示网站的关键字；

5. 页面重定向；

   ```html
   <meta http-equiv="refresh" content="3;url=https://www.baidu.com">
   ```

   

### 十二、语义化标签

在用html标签时，应该关注的是标签的语义，而不是它的样式；

1. 标题标签

   * h1-h6一共有六级标签
   * 一般情况下一个页面只有一个h1

2. 块元素

   * 在页面中独占一行的元素成为块元素；（block element）
   * 在网页中一般通过块元素来对页面进行布局；

3. 行内元素

   * 在页面中不会独占一行的元素称为行内元素（inline element）
   * 行内元素主要用来包裹文字；

4. p标签

   p标签标表示页面中的一个段落，也是一个块元素；

5. hgroup

   标签用来为标题分组，可以将一组相关的标题同事放入到hgroup；

6. em

   em标签用于表示语音语调的一个加重；不会独占一行；

7. strong

   strong表示强调；

8. blockquote

   表示一个长引用；

9. q

   表示一个短引用；

10. br

    用于换行；



#### 布局标签（结构化语义标签）

1. header： 表示网页的头部；
2. main： 表示网页的主体部分；
3. footer： 表示网页的底部；
4. nav： 表示网页中的导航；
5. aside： 和主体相关的其他内容；（侧边栏）
6. article： 表示一个独立的文章；
7. section： 表示一个独立的区块；
8. div： 没有语义，用来表示一个独立的区块，当前还是主要的布局元素；
9. span： 行内元素，没有任何的语义，一般用于在网页中选中文字；