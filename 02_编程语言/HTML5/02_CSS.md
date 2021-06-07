###  一、简介

CSS：层叠样式表

* 网页实际上是一个多层的结构，通过CSS可以分别为网页的每一层设置样式，而最终我们看到的只是网页的最上层；
* 简言之，CSS用来设置网页中的元素样式；



#### 1. 设置方式

##### 第一种：内联样式，行内样式

* 在标签内部通过style属性来设置元素的样式；

* 问题：内联样式只能对一个标签生效，如果多个元素使用同一个样式，维护不方便；

```html
<p style="color: red; font-size: 60px;">今天天气真不错！</p>
```



##### 第二种：将样式编写到head中的style标签里

 可以同时为多个标签设置样式，并且修改时只需要修改一处即可全部应用，内部样式表更加方便对样式进行复用；

* 问题：只能对一个页面起作用，不能跨页面复用；

```html
543// 为所有p标签设置样式

<style>
    p {
        color: green;
        font-size: 50px;
    }
</style>

<p>今天天气真不错！</p>
```



##### 第三种：外部样式表

将css样式编写到一个外部css文件中，然后通过link标签引入外部css文件；

* 将样式编写到外部css文件中，可以使用浏览器的缓存机制，从而加快网页的加载速度，提升用户体验；

```html
<head>
    <meta charset="UTF-8">
    <title>Title</title>

<link rel="stylesheet" href="style_1.css">
</head>
```





### 二、语法

在style标签内，使用css的语法；

```
选择器 声明块
p { }
```

* 选择器，通过选择器可以选中页面中的指定元素，比如 p 的作用就是选中页面中所有的p元素；

*   声明块，通过声明块来指定要为元素设置的样式；

  * 声明块由一个一个的声明组成，声明是一个名值对结构，一个样式名对应一个样式值，名和值之间以:连接，以;结尾        

  ```css
  p {
          color: green;
          font-size: 50px;
      }
  ```



#### 1. 常用选择器

##### 元素选择器

* 作用：根据标签名来选中指定的元素；
* 语法：标签名{}
* 例子：p{}  h1{}  div{}

##### id选择器

* 作用：根据元素的id属性值选中一个元素；
* 语法：#id属性值{}
* 例子：#box{} #red{}  

##### 类选择器

* 作用：根据元素的class属性值选中一组元素；一个标签可以设置多个class，多个class之间使用空格隔开；
* 语法：.class 属性值；
* 例子：.blue {}

##### 通配选择器

* 作用：选中页面中的所有元素；
* 语法: * 



#### 2. 复合选择器

##### 交集选择器

* 作用：选中同时复合多个条件的元素；
* 语法：选择器1选择器2选择器3选择器n{}
* 交集选择器中如果有元素选择器，必须使用元素选择器开头

```html
/* 将class为red的元素设置为红色（字体） */
.red{
color: red;
}

/* 将class为red的div字体大小设置为30px */
div.red{
font-size: 30px;
}

<div class="red">我是div</div>
```

##### 并集选择器

*  作用：同时选择多个选择器对应的元素
* 语法：选择器1,选择器2,选择器3,选择器n{}

```css
h1, span{
            color: green
        }

<h1>标题</h1>
<span>哈哈</span>
```



#### 3.  关系选择器

* 父元素
* 直接包含子元素的元素叫做父元素；
* 子元素
* 直接被父元素包含的元素是子元素；
* 祖先元素

  * 直接或间接包含后代元素的元素叫做祖先元素
* 后代元素
* 直接或间接被祖先元素包含的元素叫做后代元素
* 兄弟元素
  
  * 拥有相同父元素的元素是兄弟元素



##### 子元素选择器

* 作用：选中指定父元素的指定子元素；
* 语法：父元素 > 子元素；

```css
div.box > span{
     color: orange;
}
```

##### 后代元素选择器

* 作用：选中指定元素内的指定后代元素；
* 语法：祖先 后代；

```css
div span{
    color: skyblue
}
```

##### 选择下一个兄弟

* 语法：前一个 + 下一个



##### 选择下边所有的兄弟

* 语法：兄 ~ 弟


```css
p + span{
    color: red;
}

p ~ span{
    color: red;
}
```



#### 4. 属性选择器

* [属性名] 
  * 选择含有指定属性的元素
* [属性名=属性值] 
  * 选择含有指定属性和属性值的元素
* [属性名^=属性值] 
  * 选择属性值以指定值开头的元素
* [属性名$=属性值] 
  * 选择属性值以指定值结尾的元素
* [属性名*=属性值] 
  * 选择属性值中含有某值的元素的元素

```css
p[title] {
color: orange;
}

p[title=abc] {
color: orange;
}

p[title^=abc] {
color: orange;
}

p[title$=abc] {
color: orange;
}

p[title*=e] {
color: orange;
}

p[title*=e] {
color: orange;
}

<p title="abc">少小离家老大回</p>
<p title="abcdef">乡音无改鬓毛衰</p>
<p title="helloabc">儿童相见不相识</p>
```



#### 5. 伪类选择器

* 伪类用来描述一个元素的特殊状态；
  
  * 比如：第一个子元素、被点击的元素、鼠标移入的元素...
  
* 伪类一般情况下都是使用:开头
  * :first-child 第一个子元素
  
  * :last-child 最后一个子元素
  
  * :nth-child() 选中第n个子元素
    * 特殊值：
    * n 第n个 n的范围0到正无穷
    * 2n 或 even 表示选中偶数位的元素
    * 2n+1 或 odd 表示选中奇数位的元素
    
  * **以上这些伪类都是根据所有的子元素进行排序**
  
    ```css
    <style>
            ul > li:nth-child(even) {
                color: red;
            }
    </style>
    
    <ul>
        <li>第一个</li>
        <li>第二个</li>
        <li>第三个</li>
        <li>第四个</li>
        <li>第五个</li>
        <li>第六个</li>
    </ul>    
    ```
  
* 下面几个伪类的功能和上述的类似，不通点是他们是在同类型元素中进行排序；

  * :first-of-type
  * :last-of-type
  * :nth-of-type()

* :not() 否定伪类

  * 将符合条件的元素从选择器中去除

    ```css
    <style>
            ul > li:not(:nth-of-type(3)) {
                color: #3a84ff;
            }
    </style>
    ```

    

##### a标签伪类

1. :link 用来表示没访问过的链接（正常的链接）

   ```css
   a:link{
      color: red;
   }
   ```

2. :visited 用来表示访问过的链接

   * 由于隐私的原因，所以visited这个伪类只能修改链接的颜色

   ```css
   a:visited{
       color: orange; 
       /* font-size: 50px;   */
   }
   ```

3. :hover 用来表示鼠标移入的状态

   ```css
    a:hover{
         color: aqua;
         font-size: 50px;
   }
   ```

4. :active 用来表示鼠标点击

   ```css
   a:active{
       color: yellowgreen;             
   }
   ```



#### 6. 伪元素选择器

伪元素，表示页面中一些特殊的并不真实的存在的元素（特殊的位置），伪元素使用 :: 开头；

* ::first-letter 表示第一个字母
* ::first-line 表示第一行
* ::selection 表示选中的内容
* ::before 元素的开始 
* ::after 元素的最后

                    - before 和 after 必须结合content属性来使用

```css
<style>
        p::first-letter {
            font-size: 50px;
        }

        p::first-line {
            background-color: #3a84ff;
        }

        p::selection {
            background-color: #22aa00;
        }

        div::before {
            content: '『';
        }

        div::after {
            content: '』';
        }
</style>
```



#### 7. 样式的继承

* 样式的继承，我们为一个元素设置的样式同时也会应用到它的后代元素上；

* 继承是发生在祖先和后代之间的；
* 继承的设计是为了方便我们的开发，利用继承我们可以将一些通用的样式统一设置到共同的祖先元素上，这样只需设置一次即可让所有的元素都具有该样式；
* 注意：并不是所有的样式都会被继承，比如 背景相关的，布局相关等的这些样式都不会被继承。