Handler本质是一个事件驱动模型，比如在Activity、service启动回调的生命周期，view的布局等都是将事件封装成Message，然后通过handler加入到MessageQueen中依次执行。



# 一. 消息循环

应用启动入口在ActivityThread，main()方法内调用一些初始化或其他方法后，最后开始执行一个Looper死循环。

每一个线程仅维护一个Looper（一个实例），如果在子线程中使用Looper，任务结束后应当调用quit()去结束Loop循环.



```java
    public static void main(String[] args) {
        ...
        
        Looper.prepareMainLooper();
		
		...
        ActivityThread thread = new ActivityThread();
        thread.attach(false, startSeq);
		
		...
        // End of event ActivityThreadMain.
        Trace.traceEnd(Trace.TRACE_TAG_ACTIVITY_MANAGER);
        Looper.loop();  //调用loop()死循环，当消息队列内为空时，为阻塞状态；

        throw new RuntimeException("Main thread loop unexpectedly exited");
    }
```



我们进入到loop()方法，详细分析可以确认下面3点：

1. 内部一个for无限循环，只有在msg==null的时候，才会跳出循环结束loop()方法，否则会一直循环下去；
2. queue.next()不断的从消息队列中拿Message，如果队列为空拿不到的时候，就会阻塞；
3. 拿到的消息不为空后，立即调用`msg.target.dispatchMessage(msg)`，分发给handler去处理；

**Looper#loop()** 

```java
/**
     * Run the message queue in this thread. Be sure to call
     * {@link #quit()} to end the loop.
     */
    public static void loop() {
        final Looper me = myLooper();
        if (me == null) {
            throw new RuntimeException("No Looper; Looper.prepare() wasn't called on this thread."); // 循环之前首先要完成初始化
        }
        final MessageQueue queue = me.mQueue;
        
        ...

        for (;;) {
            Message msg = queue.next(); // 从队列中取消息，如果为空，就会阻塞在这个地方；
            if (msg == null) {
                // No message indicates that the message queue is quitting.
                return;
            }

			...
                
            try {
                msg.target.dispatchMessage(msg); // target为Handler实例
            } finally {
                if (traceTag != 0) {
                    Trace.traceEnd(traceTag);
                }
            }
            ...     
            msg.recycleUnchecked(); //msg重复使用
        }
    }
```



接着查看MessageQueue中的next()方法，可以得出下面结论：

`nativePollOnce(ptr, nextPollTimeoutMillis)`是一个native方法，底层使用了epoll机制，用来阻塞唤醒（具体可参考其他blog），我们核心是看nextPollTimeoutMillis这个参数值：

* nextPollTimeoutMillis = 0, 默认状态的值，此时不会阻塞，方法立即返回；
* nextPollTimeoutMillis = -1，一直阻塞，等待新加入消息的唤醒；
* nextPollTimeoutMillis > 0， 阻塞timeoutMillis，然后唤醒返回；



从next()方法中看到for循环内部，

* **当消息msg为空**（即队列头mMessages为空），则 nextPollTimeoutMillis = -1，继续下一次循环；下次循环调用`nativePollOnce(ptr, nextPollTimeoutMillis)`方法会阻塞，等待新加入消息的唤醒；

* **当消息msg不为空时，**进行now 与 msg.when 的大小对比，msg.when 实际传过来的值为`SystemClock.uptimeMillis() + delayMillis`，也就是当时发送消息的时间戳加上delay时间；

  **当now < msg.when时，**说明此时的延时时间小于delayMillis，应阻塞，阻塞时间nextPollTimeoutMillis 为msg.when与now的差值，即`nextPollTimeoutMillis = (int) Math.min(msg.when - now, Integer.MAX_VALUE);`

  在下一次的循环中，调用`nativePollOnce(ptr, nextPollTimeoutMillis)`方法阻塞nextPollTimeoutMillis 后返回，此时一定**now >= msg.when**，从队头立即返回一个msg，回到Looper#loop() 中继续执行。

**MessageQueue#next()**

```java
Message next() {
   
    ...
    
    int nextPollTimeoutMillis = 0;
    
    for (;;) {
    ...

        nativePollOnce(ptr, nextPollTimeoutMillis);
		
        synchronized (this) {
            // Try to retrieve the next message.  Return if found.
            final long now = SystemClock.uptimeMillis();
            Message prevMsg = null;
            Message msg = mMessages;
            
            ...
                
            if (msg != null) {
                if (now < msg.when) {
                    // Next message is not ready.  Set a timeout to wake up when it is ready.
                    nextPollTimeoutMillis = (int) Math.min(msg.when - now, Integer.MAX_VALUE);
                } else {
                    // Got a message.
                    mBlocked = false;
                    if (prevMsg != null) {
                        prevMsg.next = msg.next;
                    } else {
                        mMessages = msg.next;
                    }
                    msg.next = null;
                    return msg;
                }
            } else {
                // No more messages.
                nextPollTimeoutMillis = -1;
            }
          ...
}
```





# 二、消息发送

通过调用Handler内部的`handler.sendMessage()` 、`handler.sendMessageDelayed()`等方法发送消息，最终都会调用**sendMessageAtTime()**;

```java
public boolean sendMessageAtTime(Message msg, long uptimeMillis) {
    MessageQueue queue = mQueue;
    if (queue == null) {
        RuntimeException e = new RuntimeException(
                this + " sendMessageAtTime() called with no mQueue");
        Log.w("Looper", e.getMessage(), e);
        return false;
    }
    return enqueueMessage(queue, msg, uptimeMillis);
}

private boolean enqueueMessage(MessageQueue queue, Message msg, long uptimeMillis) {
        msg.target = this;
        if (mAsynchronous) {
            msg.setAsynchronous(true);
        }
        return queue.enqueueMessage(msg, uptimeMillis);
}
```

最后是调用**Message#enqueueMessage(msg, uptimeMillis)**将消息加入队列；

虽然说是消息队列，**其内部维护的实际是一个单链表数据结构**，mMessages就是链表的头元素：获取msg时，从队列头获取；添加msg时，遍历链表，比较smg的延时时间，插入合适的位置。

具体插入msg的代码如下所示：

```java
Message mMessages;

boolean enqueueMessage(Message msg, long when) {
    if (msg.target == null) {  //Handler不能为空
        throw new IllegalArgumentException("Message must have a target.");
    }
    if (msg.isInUse()) {  //复用的msg不能出在被使用中
        throw new IllegalStateException(msg + " This message is already in use.");
    }

    synchronized (this) {
        
        msg.markInUse();
        msg.when = when;
        Message p = mMessages;
        boolean needWake;
        if (p == null || when == 0 || when < p.when) {
            // New head, wake up the event queue if blocked.
            msg.next = p;    //队列为空，msg直接作为队头；
            mMessages = msg;
            needWake = mBlocked;
        } else {
            // Inserted within the middle of the queue.  Usually we don't have to wake
            // up the event queue unless there is a barrier at the head of the queue
            // and the message is the earliest asynchronous message in the queue.
            needWake = mBlocked && p.target == null && msg.isAsynchronous();
            Message prev;
            for (;;) {
                prev = p;
                p = p.next;
                if (p == null || when < p.when) {  // 比较延时时间，按照从小到大的顺序，插入的合                                                    // 适的位置，然后退出当前单链表的遍历；
                    break;
                }
                if (needWake && p.isAsynchronous()) {
                    needWake = false;
                }
            }
            msg.next = p; // invariant: p == prev.next
            prev.next = msg;
        }

        // We can assume mPtr != 0 because mQuitting is false.
        if (needWake) {
            nativeWake(mPtr);
        }
    }
    return true;
}
```



# 三、消息处理

在前面消息循环解析中知道，不管有没有延时，只要从队列中获取到msg，就会调用`msg.target.dispatchMessage(msg)`方法，返回到Handler中处理；
        

```java
public static void loop() {
			...
            msg.target.dispatchMessage(msg); // target为Handler实例
            ...     
        }
    }
```

**Handler#dispatchMessage**

```java
/**
 * Handle system messages here.
 */
public void dispatchMessage(Message msg) {
    if (msg.callback != null) {
        handleCallback(msg);
    } else {
        if (mCallback != null) {
            if (mCallback.handleMessage(msg)) {
                return;
            }
        }
        handleMessage(msg);
    }
}
```

不管是实现接口方法，或是重载handleMessage(msg)方法，都可以进行处理。

此时，整理Handler机制分析完毕。



### 补充

#### ThreadLocal

如何在Handler内部获取到当前线程的Looper？

ThreadLocal可以在不同的线程中互不干扰的存储并提供数据，通过ThreadLocal可以轻松获取每个线程的Looper。

