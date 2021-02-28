# Activity的启动

Activity启动就是调用startActivity()方法，创建Activity的过程。



## 一、startActivity的调用过程
1. **Activity内有startActivity()方法，直接调用**

   

2. **调用Instrumentation.execStartActivity**

   Instrumentation主要用来监控应用程序和系统的交互；

   

3. **通过ActivityManagerProxy代理，调用AMS中的startActivity()**

   在AMS进程做些准备工作，配置堆栈信息。

   

4. **调用schedulePauseActivity#ApplicationThreadProxy**

   使用代理回调当前Activity所在的进程，并回调onPause()方法，然后通知ActivityManagerService，这个Activity已经进入Paused状态，可以启动新的Activity了。



5. **调用activityPaused()，回到AMS进程；**

   这里主要是调用Process.start接口来创建一个新的进程，新的进程会导入android.app.ActivityThread类，并且执行它的main函数，每一个应用程序都有一个ActivityThread实例。

   即将要启动的Activity就是在这个ActivityThread实例中运行，并执行main()函数。

   在main()函数中，调用attach()方法，启动looper无线循环。

   

6. **在attch()中，进行attachApplication()远程方法调用，进入到AMS进程**

   做一些启动的配置工作，然后通过applicationThread代理，调用scheduleLaunchActivity()，回到当前Activity进程，创建Activity对象。



7. **scheduleLaunchActivity()#ApplicationThread** 

   使用handler方法，发送H.LAUNCH_ACTIVITY消息，在Instrumentation内，通过classLoader创建真正的Activity对象，然后调用attach()方法初始化；

   继而调用onCreate()，onStart(), onResume();



## 二、调用时序图

### 涉及的相关类

**package android.app**

> android.app.Activity; 
> android.app.Instrumentation; 
>
> android.app.ActivityManagerNative; 
> android.app.ActivityManagerProxy; //在ActivityManagerNative.java文件中
>
> android.app.ActivityThread; 
> android.app.ActivityThread.ApplicationThread;   //ActivityThread内部类
> android.app.ActivityThread.H;   //ActivityThread内部类

**package com.android.server.am**

> com.android.server.am.ActivityManagerService;
>
> com.android.server.am.ActivityStarter;
> com.android.server.am.ActivityStackSupervisor;
>
> com.android.server.am.ActivityRecord;
> com.android.server.am.ActivityStack

**package android.os**

>  android.os.Process;



![](./resFiles/binder/start_activity.svg)