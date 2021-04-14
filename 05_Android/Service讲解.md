# HandlerThread 

* HandlerThread本质上是一个线程类，它继承了Thread；
* HandlerThread有自己的内部Looper对象，可以进行looper循环；
* 通过获取HandlerThread的looper对象传递给Handler对象，可以在handleMessage方法中执行异步任务。
* 创建HandlerThread后必须先调用HandlerThread.start()方法，Thread会先调用run方法，创建Looper对象。



### HandlerThread常规使用

**1.创建实例对象**

```java
//传入参数的作用主要是标记当前线程的名字，可以任意字符串。
HandlerThread handlerThread = new HandlerThread("downloadImage");
```

**2. 启动HandlerThread线程**

```java
//必须先开启线程
handlerThread.start();
```

创建完HandlerThread并启动了线程。那么怎么将一个耗时的异步任务投放到HandlerThread线程中去执行呢？

**3.构建循环消息处理机制**

```java
//该callback运行于子线程
class ChildCallback implements Handler.Callback {
        @Override
        public boolean handleMessage(Message msg) {
            //在子线程中进行相应的网络请求

            //通知主线程去更新UI
            mUIHandler.sendMessage(msg1);
            return false;
        }
}
```



**4.构建异步handler**

```java
//子线程Handler
Handler childHandler = new Handler(handlerThread.getLooper(),new ChildCallback());
```

第3步和第4步是构建一个可以用于异步操作的handler，并将前面创建的HandlerThread的Looper对象以及Callback接口类作为参数传递给当前的handler，这样当前的异步handler就拥有了HandlerThread的Looper对象，由于HandlerThread本身是异步线程，因此Looper也与异步线程绑定，从而handlerMessage方法也就可以异步处理耗时任务了，这样我们的Looper+Handler+MessageQueue+Thread异步循环机制构建完成。





# IntentService



### IntentService概述

* 本质是一种特殊的Service,继承自Service并且本身就是一个抽象类；
* 它可以用于在后台执行耗时的异步任务，当任务完成后会自动停止；
* 它拥有较高的优先级，不易被系统杀死（继承自Service的缘故），因此比较适合执行一些高优先级的异步任务
* 它内部通过HandlerThread和Handler实现异步操作；
* 创建IntentService时，只需实现onHandleIntent和构造方法，onHandleIntent为异步方法，可以执行耗时操作；



### IntentService的常规使用

```java
//HelloService
public class HelloService extends IntentService {
    public static final String TAG = "HelloService";
    public static final String LOAD_PICTURE = "load_picture";

    /**
     * Creates an IntentService.  Invoked by your subclass's constructor.
     *
     * @param name Used to name the worker thread, important only for debugging.
     */
    public HelloService(String name) {
        super(name);
    }

    public HelloService() {
        super("Hello Service");
    }

    @Override
    public void onCreate() {
        super.onCreate();
        Log.e(TAG, "onCreate: ");
    }

    @Override
    public void onDestroy() {
        super.onDestroy();
        Log.e(TAG, "onDestroy: ");
    }

    @Override
    public int onStartCommand(@Nullable Intent intent, int flags, int startId) {
        Log.e(TAG, "onStartCommand: ");
        return super.onStartCommand(intent, flags, startId);
    }

    @Override
    protected void onHandleIntent(@Nullable Intent intent) {
        // Normally we would do some work here, like download a file.
        // For our sample, we just sleep for 5 seconds.
        try {
            Thread.sleep(1000);
            String stringExtra = intent.getStringExtra(LOAD_PICTURE);
            Log.e(TAG, "onHandleIntent: thread:" + Thread.currentThread().getName() + ", stringExtra = 						" + stringExtra);
            
        } catch (InterruptedException e) {
            // Restore interrupt status.
            Thread.currentThread().interrupt();
        }
    }
}

```

调用

```java
Log.e(TAG, "click thread = " + Thread.currentThread().getName());
Intent intent = new Intent(ServiceActivity.this, HelloService.class);

for (int i = 0; i < 5; i++) {
    String picStr = "加载图片:" + (i + 1);
    intent.putExtra(HelloService.LOAD_PICTURE, picStr);
    startService(intent);
}
```

```java
//输出
E/tag: click thread = main
E/HelloService: onCreate: 
E/HelloService: onStartCommand: 
E/HelloService: onStartCommand: 
E/HelloService: onHandleIntent: thread:IntentService[Hello Service], stringExtra = 加载图片:1
E/HelloService: onHandleIntent: thread:IntentService[Hello Service], stringExtra = 加载图片:2
E/HelloService: onHandleIntent: thread:IntentService[Hello Service], stringExtra = 加载图片:3
E/HelloService: onHandleIntent: thread:IntentService[Hello Service], stringExtra = 加载图片:4
E/HelloService: onHandleIntent: thread:IntentService[Hello Service], stringExtra = 加载图片:5
E/HelloService: onDestroy: 
```







