## 动画



### 一、过渡

通过过渡可以指定一个属性发生变化时的切换方式；

1.  transition-property：指定要执行过渡的属性 ；

   * 多个属性间使用,隔开；
   * 如果所有属性都需要过渡，则使用all关键字；
   * 大部分属性都支持过渡效果，注意过渡时必须是从一个有效数值向另外一个有效数值进行过渡；

   ```css
   transition-property: height , width;
   ```

2.  transition-duration: 指定过渡效果的持续时间；

   ```css
   transition-duration: 100ms, 2s;
   ```

3. transition-timing-function: 过渡的时序函数

   * ease 默认值，慢速开始，先加速，再减速
   * linear 匀速运动
   * ease-in 加速运动
   * ease-out 减速运动
   * ease-in-out 先加速 后减速
   * cubic-bezier() 来指定时序函数
   * steps() 分步执行过渡效果（分几步执行）
     * end ， 在时间结束时执行过渡(默认值)；
     * start ， 在时间开始时执行过渡；

4. transition-delay: 过渡效果的延迟，等待一段时间后在执行过渡  

   ```css
   transition-delay: 2s; 
   ```

5.  transition 可以同时设置过渡相关的所有属性；

   * 只有一个要求，如果要写延迟，则两个时间中第一个是持续时间，第二个是延迟；

   ```css
    transition:2s margin-left 1s cubic-bezier(.24,.95,.82,-0.88);
   ```

   

### 二、动画

动画和过渡类似，都是可以实现一些动态的效果，不同的是过渡需要在某个属性发生变化时才会触发，动画可以自动触发动态效果；

1.  设置动画效果，必须先要设置一个关键帧，关键帧设置了动画执行每一个步骤；

   ```css
   @keyframes test {
               /* from表示动画的开始位置 也可以使用 0% */
               from {
                   margin-left: 0;
                   background-color: orange;
               } 
   
               /* to动画的结束位置 也可以使用100%*/
               to {
                   background-color: red;
                   margin-left: 700px;
               }
   }
   ```

2.  animation-name: 要对当前元素生效的关键帧的名字;

   ```css
   animation-name: test;
   ```

3. animation-duration: 动画的执行时间;

4. animation-delay: 2s;

5. animation-iteration-count 动画执行的次数；

   * infinite 无限执行；

6. animation-direction  指定动画运行的方向；

   * normal 默认值  从 from 向 to运行 每次都是这样 ；
   * reverse 从 to 向 from 运行 每次都是这样；
   * alternate 从 from 向 to运行 重复执行动画时反向执行；
   * alternate-reverse 从 to 向 from运行 重复执行动画时反向执行；

7. animation-play-state: 设置动画的执行状态 

   * running 默认值 动画执行
   * paused 动画暂停

8. animation-fill-mode: 动画的填充模式

   * none 默认值 动画执行完毕元素回到原来位置
   * forwards 动画执行完毕元素会停止在动画结束的位置
   * backwards 动画延时等待时，元素就会处于开始位置
   * both 结合了forwards 和 backwards



### 三、变形

变形就是指通过CSS来改变元素的形状或位置，变形不会影响到页面的布局；

1. transform 用来设置元素的变形效果；

2. 平移：（平移元素，百分比是相对于自身计算的）

   * translateX() 沿着x轴方向平移

   * translateY() 沿着y轴方向平移

   * translateZ() 沿着z轴方向平移

     ```css
     transform: translateX(100%);
     ```

3. 实现居中对齐

   ```css
   .box3 {
        background-color: orange;
        position: absolute;
        /* 
              这种居中方式，只适用于元素的大小确定
        top: 0;
        left: 0;
        bottom: 0;
        right: 0;
        margin: auto; */
   
        left: 50%;
        top: 50%;
        transform: translateX(-50%) translateY(-50%);     
   }
   ```

4. 被选中有平移阴影效果

   ```css
   .box4, .box5{
   	width: 220px;
   	height: 300px;
   	background-color: #fff;
   	float: left;
   	margin: 0 10px;
   	transition:all .3s;
   }
   
   .box4:hover,.box5:hover{
   	transform: translateY(-4px);
   	box-shadow: 0 0 10px rgba(0, 0, 0, .3)
   }
   
   <body>
       <div class="box4"></div>
       <div class="box5"></div>
   </body>
   ```



#### Z轴平移

1. z轴平移，调整元素在z轴的位置，正常情况就是调整元素和人眼之间的距离，距离越大，元素离人越近。

2. z轴平移属于立体效果（近大远小），默认情况下网页是不支持透视，如果需要看见效果，必须要设置网页的视距。

   ```css
   html{
       /* 设置当前网页的视距为800px，人眼距离网页的距离 */
       perspective: 800px;
   }
   ```

3. 放大、缩小效果

   ```css
   .box1{
       width: 200px;
       height: 200px;
       background-color: #bfa;
       margin: 200px auto;
       transition:2s;
   }
   
   body:hover .box1{
        transform: translateZ(600px);
   }
   ```

   



### 四、旋转

通过旋转可以使元素沿着x y 或 z旋转指定的角度；

* rotateX()
* rotateY()
* rotateZ()

1. 延z轴旋转

   ```css
   transform: rotateZ(.25turn);
   ```

2. 旋转并平移

   ```css
   transform: rotateY(180deg) translateZ(400px);
   ```

3. 是否显示元素背面

   ```css
   backface-visibility: hidden;
   ```

##### 旋转练习

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        html {
            perspective: 800px;
        }

        .cube {
            width: 200px;
            height: 200px;
            background-color: #bbffaa;
            margin: 100px auto;
            transform-style: preserve-3d;
            transform: rotateX(45deg) rotateZ(45deg);
            animation: rotate 5s infinite linear;
        }

        .cube > div {
            width: 200px;
            height: 200px;
            position: absolute;
        }

        img {
            vertical-align: top;
        }

        .box1 {
            transform: rotateY(90deg) translateZ(100px);
        }

        .box2 {
            transform: rotateY(-90deg) translateZ(100px);
        }

        .box3 {
            transform: rotateX(-90deg) translateZ(100px);
        }

        .box4 {
            transform: rotateX(90deg) translateZ(100px);
        }

        .box5 {
            transform: rotateY(180deg) translateZ(100px);
        }

        .box6 {
            transform:translateZ(100px);
        }

        @keyframes rotate {
            from {
                transform: rotateX(0) rotateZ(0);
            }

            to {
                transform: rotateX(360deg) rotateZ(360deg);
            }
        }



    </style>
</head>
<body>
<div class="cube">
    <div class="box1">
        <img src="./img/14/1.jpg" alt="">
    </div>

    <div class="box2">
        <img src="./img/14/2.jpg" alt="">
    </div>

    <div class="box3">
        <img src="./img/14/3.jpg" alt="">
    </div>

    <div class="box4">
        <img src="./img/14/4.jpg" alt="">
    </div>

    <div class="box5">
        <img src="./img/14/5.jpg" alt="">
    </div>

    <div class="box6">
        <img src="./img/14/6.jpg" alt="">
    </div>
</div>
</body>
</html>
```



### 五、缩放

1. 对元素进行缩放的函数；（拉长的是轴）

   * scaleX() 水平方向缩放
   * scaleY() 垂直方向缩放
   * scale() 双方向的缩放

2. 图片放大

   ```css
.img-wrapper{
               width: 200px;
            height: 200px;
               border: 1px red solid;
               overflow: hidden;
   }
   
   img{
       transition: .2s;
   }
   
   .img-wrapper:hover img{
         transform:scale(1.2);
   }
   ```
   
   
   
   

### 六、变形的原点

默认值为center

```css
transform-origin:20px 20px;
```



