# Activity应用视图的创建过程



- 一 创建Context对象
- 二 创建Window对象
- 三 创建View对象
- 四 创建WindowState对象
- 五 创建Surface对象



## 1. Context对象的创建

perforLaunchActivity()调用，在Activity创建前，会创建一个Context对象；

然后调用attach()方法，关联context信息；