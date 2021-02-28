# 一、检测FPS具体示例

```java
private Choreographer choreographer;

//具体实现
private void init() {
    choreographer = Choreographer.getInstance();
    choreographer.postFrameCallback(new FPSFrameCallback(System.nanoTime()));
}

public class FPSFrameCallback implements Choreographer.FrameCallback {

    private static final String TAG = "FPS_TEST";
    private long mLastFrameTimeNanos = 0;
    private long mFrameIntervalNanos;

    public FPSFrameCallback(long lastFrameTimeNanos) {
        mLastFrameTimeNanos = lastFrameTimeNanos;
        mFrameIntervalNanos = (long) (1000000000 / 60.0);
    }

    @Override
    public void doFrame(long frameTimeNanos) {

        //初始化时间
        if (mLastFrameTimeNanos == 0) {
            mLastFrameTimeNanos = frameTimeNanos;
        }
        final long jitterNanos = frameTimeNanos - mLastFrameTimeNanos;
        if (jitterNanos >= mFrameIntervalNanos) {
            final long skippedFrames = jitterNanos / mFrameIntervalNanos;
            if (skippedFrames > 30) {
                Log.i(TAG, "Skipped " + skippedFrames + " frames!  "
                        + "The application may be doing too much work on its main thread.");
            }
        }
        mLastFrameTimeNanos = frameTimeNanos;
        //注册下一帧回调
        choreographer.postFrameCallback(this);
    }
}
```



# 二、源码分析



### 1. 首先获取一个Choreographer对象

```java
// Thread local storage for the choreographer.
private static final ThreadLocal<Choreographer> sThreadInstance =
        new ThreadLocal<Choreographer>() {
    @Override
    protected Choreographer initialValue() {
        Looper looper = Looper.myLooper();
        if (looper == null) {
            throw new IllegalStateException("The current thread must have a looper!");
        }
        return new Choreographer(looper);
    }
};
```



### 2. Choreographer构造函数，进行初始化操作

```java
private Choreographer(Looper looper) {
    mLooper = looper;
    //(1) 创建FrameHandle对象，用于处理Handler消息；
    mHandler = new FrameHandler(looper);
    //（2）创建FrameDisplayEventReceiver对象，用于接收接收VSYNC信号；
    mDisplayEventReceiver = USE_VSYNC ? new FrameDisplayEventReceiver(looper) : null;
    mLastFrameTimeNanos = Long.MIN_VALUE;

    mFrameIntervalNanos = (long)(1000000000 / getRefreshRate());

    mCallbackQueues = new CallbackQueue[CALLBACK_LAST + 1];
    for (int i = 0; i <= CALLBACK_LAST; i++) {
        mCallbackQueues[i] = new CallbackQueue();
    }
}
```



##### (1) 创建一个FrameHandle对象，用于处理Handler消息；

```java
private final class FrameHandler extends Handler {
    public FrameHandler(Looper looper) {
        super(looper);
    }

    @Override
    public void handleMessage(Message msg) {
        switch (msg.what) {
            case MSG_DO_FRAME:
                doFrame(System.nanoTime(), 0);
                break;
            case MSG_DO_SCHEDULE_VSYNC:
                doScheduleVsync();
                break;
            case MSG_DO_SCHEDULE_CALLBACK:
                doScheduleCallback(msg.arg1);
                break;
        }
    }
}

public interface FrameCallback {
     public void doFrame(long frameTimeNanos);
}
```



##### (2) 创建FrameDisplayEventReceiver对象，用于接收接收VSYNC信号；

FrameDisplayEventReceiver类主要实现`onsync()`方法，主要的工作都在父类DisplayEventReceiver里面完成；

```java
private final class FrameDisplayEventReceiver extends DisplayEventReceiver
        implements Runnable {
    private boolean mHavePendingVsync;
    private long mTimestampNanos;
    private int mFrame;

    public FrameDisplayEventReceiver(Looper looper) {
        super(looper);
    }

    @Override
    public void onVsync(long timestampNanos, int builtInDisplayId, int frame) {
        ...
        mTimestampNanos = timestampNanos;
        mFrame = frame;
        Message msg = Message.obtain(mHandler, this);
        msg.setAsynchronous(true);
        mHandler.sendMessageAtTime(msg, timestampNanos / TimeUtils.NANOS_PER_MS);
    }

    @Override
    public void run() {
        mHavePendingVsync = false;
        doFrame(mTimestampNanos, mFrame);
    }
}
```

`scheduleVsync()`方法用于请求VSNYC信号， Native方法接收到VSYNC信息处理后会调用java层`dispatchVsync()`方法，最终调用到FrameDisplayEventReceiver的`onVsync`方法

```java
public abstract class DisplayEventReceiver {
    
    /**
     * Called when a vertical sync pulse is received.
     * The recipient should render a frame and then call {@link #scheduleVsync}
     * to schedule the next vertical sync pulse.
     *
     * @param timestampNanos The timestamp of the pulse, in the {@link System#nanoTime()}
     * timebase.
     * @param builtInDisplayId The surface flinger built-in display id such as
     * {@link SurfaceControl#BUILT_IN_DISPLAY_ID_MAIN}.
     * @param frame The frame number.  Increases by one for each vertical sync interval.
     */
    public void onVsync(long timestampNanos, int builtInDisplayId, int frame) {
    }

    /**
     * Schedules a single vertical sync pulse to be delivered when the next
     * display frame begins.
     */
    public void scheduleVsync() {
        if (mReceiverPtr == 0) {
            Log.w(TAG, "Attempted to schedule a vertical sync pulse but the display event "
                    + "receiver has already been disposed.");
        } else {
            nativeScheduleVsync(mReceiverPtr);
        }
    }

    // Called from native code.
    @SuppressWarnings("unused")
    private void dispatchVsync(long timestampNanos, int builtInDisplayId, int frame) {
        onVsync(timestampNanos, builtInDisplayId, frame);
    }
}
```

### 3. postFrameCallback()方法

```java
//The callback runs once then is automatically removed.

public void postFrameCallback(FrameCallback callback) {
    postFrameCallbackDelayed(callback, 0);
}
```

```java
public void postFrameCallbackDelayed(FrameCallback callback, long delayMillis) {
    if (callback == null) {
        throw new IllegalArgumentException("callback must not be null");
    }

    postCallbackDelayedInternal(CALLBACK_ANIMATION,
            callback, FRAME_CALLBACK_TOKEN, delayMillis);
}
```

```java
private void postCallbackDelayedInternal(int callbackType,
        Object action, Object token, long delayMillis) {
 
    synchronized (mLock) {
        final long now = SystemClock.uptimeMillis();
        final long dueTime = now + delayMillis;
        mCallbackQueues[callbackType].addCallbackLocked(dueTime, action, token);

        if (dueTime <= now) {
            scheduleFrameLocked(now);
        } else {
            Message msg = mHandler.obtainMessage(MSG_DO_SCHEDULE_CALLBACK, action);
            msg.arg1 = callbackType;
            msg.setAsynchronous(true);
            mHandler.sendMessageAtTime(msg, dueTime);
        }
    }
}
```

```java
private void scheduleFrameLocked(long now) {
    if (!mFrameScheduled) {
        mFrameScheduled = true;
        if (USE_VSYNC) {
           //当前线程是UI线程；        
            if (isRunningOnLooperThreadLocked()) {
                scheduleVsyncLocked();
            } else {
                Message msg = mHandler.obtainMessage(MSG_DO_SCHEDULE_VSYNC);
                msg.setAsynchronous(true);
                mHandler.sendMessageAtFrontOfQueue(msg);
            }
        } else {
            final long nextFrameTime = Math.max(
                    mLastFrameTimeNanos / TimeUtils.NANOS_PER_MS + sFrameDelay, now);
            if (DEBUG_FRAMES) {
                Log.d(TAG, "Scheduling next frame in " + (nextFrameTime - now) + " ms.");
            }
            Message msg = mHandler.obtainMessage(MSG_DO_FRAME);
            msg.setAsynchronous(true);
            mHandler.sendMessageAtTime(msg, nextFrameTime);
        }
    }
}
```

最终调用scheduleVsync()方法；

```java
private void scheduleVsyncLocked() {
    mDisplayEventReceiver.scheduleVsync();
}
```

接收到VSYNC信号后，最终会回调onVsunc()方法；通过handler消息，然后调用run()方法；在run()方法内，又调用了doFrame()方法。

```java
@Override
public void onVsync(long timestampNanos, int builtInDisplayId, int frame) {
        ...
        mTimestampNanos = timestampNanos;
        mFrame = frame;
        Message msg = Message.obtain(mHandler, this);
        msg.setAsynchronous(true);
        mHandler.sendMessageAtTime(msg, timestampNanos / TimeUtils.NANOS_PER_MS);
}
    
@Override
public void run() {
    mHavePendingVsync = false;
    doFrame(mTimestampNanos, mFrame);
}
```