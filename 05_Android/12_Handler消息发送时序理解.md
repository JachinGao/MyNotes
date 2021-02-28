## 1. Handler消息先后发送的场景

**step1，** 消息无延时，直接发送，在消息真正处理的地方耗时**3s**（执行3s睡眠），然后打印log结束任务；

**step2，**消息延时发送**1s**， 在消息真正处理的地方无耗时，直接打印log结束任务；

**那么发送的两个消息，那个log会先被执行？**

```java
private Handler handler = new Handler(Looper.getMainLooper()) {
        @Override
        public void handleMessage(Message msg) {
            switch (msg.what) {
                case CASE_1:
                    SystemClock.sleep(3000);
                    long millis = SystemClock.uptimeMillis();
                    Log.e(TAG, "case 1, time =  " + (millis - msg.arg1));
                    break;
                case CASE_2:
                    long curTime = SystemClock.uptimeMillis();
                    Log.e(TAG, "case 2, time =  " + (curTime - msg.arg1));
                    break;
            }
        }
    };
```

```java
            @Override
            public void onClick(View v) {
				//第一条消息先被发送
                long currentTime = SystemClock.uptimeMillis();
                Message msg1 = Message.obtain();
                msg1.what = CASE_1;
                msg1.arg1 = (int) currentTime;
                handler.sendMessage(msg1);

				//第二条消息
                Message msg2 = Message.obtain();
                msg2.what = CASE_2;
                msg2.arg1 = (int) currentTime;
                handler.sendMessageDelayed(msg2, 1000);
            }
```

点击按钮，打印结果如下：

```java
com.example.handlertest E/MainActivity: onClick: 
com.example.handlertest E/MainActivity: handleMessage: case 1, time =  2001
com.example.handlertest E/MainActivity: handleMessage: case 2, time =  2003
```

就是说，虽然第二个消息只是延时1s，但最后延时2s才被执行，即必须等到消息1完成之后，才能处理第二个消息。

**如果两个时间交换，消息1处理过程为1s，消息2延时3s，最后时序关系是怎样的呢？**

```java
//睡眠1s
SystemClock.sleep(1000);

//延时3s
handler.sendMessageDelayed(msg2, 3000);
```

执行结果如下：

```
com.example.handlertest E/MainActivity: onClick: 
com.example.handlertest E/MainActivity: handleMessage: case 1, time =  1002
com.example.handlertest E/MainActivity: handleMessage: case 2, time =  3003
```

从结果可以看出，消息1执行1s的耗时任务没有影响到消息2；消息2如期延时3s得到执行。



## 总结

1. 因为Looper中维护着一个MessageQueue队列，按照先进先出原则，先进去的会先执行；

2. 消息延时是通过SystemClock.uptimeMillis()记录前后差值的，所以消息1产生的耗时，并没有影响到消息2的延时时间； 

关于Handler机制详解可以参考 [Handler消息循环、发送和处理](https://blog.csdn.net/winter2013/article/details/105547008)