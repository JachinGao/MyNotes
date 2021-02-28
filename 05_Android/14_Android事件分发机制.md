## Android事件分发机制笔记

> 目录
>
> 1. 事件是如何传到Activity中去的
> 2. 事件在Activity中的传递
> 3. 事件在ViewGroup中的传递
> 4. 事件在View中的传递
> 5. 示例



### 1.  事件是如何传到Activity中去的

在Activity中的dispatchTouchEvent()方法中，调用Thread.dumpStack()，查看调用栈；

```java
java.lang.Exception: Stack trace
at java.lang.Thread.dumpStack(Thread.java:1346)
at com.example.motionevent.MainActivity.dispatchTouchEvent(MainActivity.java:17)
at android.support.v7.view.WindowCallbackWrapper.dispatchTouchEvent(WindowCallbackWrapper.java:69)
at com.android.internal.policy.DecorView.dispatchTouchEvent(DecorView.java:563)
at android.view.View.dispatchPointerEvent(View.java:12788)
at android.view.ViewRootImpl$ViewPostImeInputStage.processPointerEvent(ViewRootImpl.java:5675)
at android.view.ViewRootImpl$ViewPostImeInputStage.onProcess(ViewRootImpl.java:5470)
at android.view.ViewRootImpl$InputStage.deliver(ViewRootImpl.java:4963)
at android.view.ViewRootImpl$InputStage.onDeliverToNext(ViewRootImpl.java:5016)
at android.view.ViewRootImpl$InputStage.forward(ViewRootImpl.java:4982)
at android.view.ViewRootImpl$AsyncInputStage.forward(ViewRootImpl.java:5119)
at android.view.ViewRootImpl$InputStage.apply(ViewRootImpl.java:4990)
at android.view.ViewRootImpl$AsyncInputStage.apply(ViewRootImpl.java:5176)
at android.view.ViewRootImpl$InputStage.deliver(ViewRootImpl.java:4963)
at android.view.ViewRootImpl$InputStage.onDeliverToNext(ViewRootImpl.java:5016)
at android.view.ViewRootImpl$InputStage.forward(ViewRootImpl.java:4982)
at android.view.ViewRootImpl$InputStage.apply(ViewRootImpl.java:4990)
at android.view.ViewRootImpl$InputStage.deliver(ViewRootImpl.java:4963)
at android.view.ViewRootImpl.deliverInputEvent(ViewRootImpl.java:7742)
at android.view.ViewRootImpl.doProcessInputEvents(ViewRootImpl.java:7682)
at android.view.ViewRootImpl.enqueueInputEvent(ViewRootImpl.java:7643)
at android.view.ViewRootImpl$WindowInputEventReceiver.onInputEvent(ViewRootImpl.java:7853)
at android.view.InputEventReceiver.dispatchInputEvent(InputEventReceiver.java:197)
at android.os.MessageQueue.nativePollOnce(Native Method)
at android.os.MessageQueue.next(MessageQueue.java:325)
at android.os.Looper.loop(Looper.java:142)
at android.app.ActivityThread.main(ActivityThread.java:6942)
at java.lang.reflect.Method.invoke(Native Method)
at com.android.internal.os.Zygote$MethodAndArgsCaller.run(Zygote.java:327)
at com.android.internal.os.ZygoteInit.main(ZygoteInit.java:1374)
```

简单的说，就是触摸事件通过Android Handler消息传递机制，将消息发送给WindowInputEventReceiver，进行如上系列的方法调用，最后事件传到 DecorView，调用`dispatchTouchEvent(ev)`方法；

**DecorView#dispatchTouchEvent**

```java
@Override
public boolean dispatchTouchEvent(MotionEvent ev) {
    final Window.Callback cb = mWindow.getCallback();
    return cb != null && !mWindow.isDestroyed() && mFeatureId < 0
            ? cb.dispatchTouchEvent(ev) : super.dispatchTouchEvent(ev);
}
```

这里，`cb.dispatchTouchEvent(ev)`实际就是调用Activity的dispatchTouchEvent()方法。







### 2. 事件在Activity中的传递

**Activity#dispatchTouchEvent**

```java
public boolean dispatchTouchEvent(MotionEvent ev) {
    if (ev.getAction() == MotionEvent.ACTION_DOWN) {
        onUserInteraction();
    }
    if (getWindow().superDispatchTouchEvent(ev)) {
        return true;
    }
    return onTouchEvent(ev);
}
```

`getWindow()` 返回PhoneWindow对象；

**PhoneWindow#superDispatchTouchEvent(ev)：**

```java
@Override
public boolean superDispatchTouchEvent(MotionEvent event) {
    return mDecor.superDispatchTouchEvent(event);
}
```

其中，mDecor为DecorView实例；

**DecorView#superDispatchTouchEvent(ev)**

```java
public boolean superDispatchTouchEvent(MotionEvent event) {
    return super.dispatchTouchEvent(event);
}
```

DecorView继承自FrameLayout，而FrameLayout的父类就是ViewGroup，所以最终调用的实际是ViewGroup中的`dispatchTouchEvent(ev)`;

> 总结
>
> getWindow().superDispatchTouchEvent(ev)的返回值为true时，表示分发的事件已被子View消费，过程结束；
>
> 当为false时，表示子View没有消费事件，Activity#onTouchEvent(ev)会被调用；





###  3.  事件在ViewGroup中的传递

**ViewGroup#dispatchTouchEvent(ev)**

```java
 @Override
    public boolean dispatchTouchEvent(MotionEvent ev) {
      
            // Check for interception.
            final boolean intercepted;
            if (actionMasked == MotionEvent.ACTION_DOWN
                    || mFirstTouchTarget != null) {
                final boolean disallowIntercept = (mGroupFlags & FLAG_DISALLOW_INTERCEPT) != 0;
                if (!disallowIntercept) {
                    //ViewGroup事件分发时，判断是否拦截
                    intercepted = onInterceptTouchEvent(ev);
                    ev.setAction(action); // restore action in case it was changed
                } else {
                    intercepted = false;
                }
            } else {
                // There are no touch targets and this action is not an initial down
                // so this view group continues to intercept touches.
                intercepted = true;
            }
           ...
           
            if (!canceled && !intercepted) {
                            if (dispatchTransformedTouchEvent(ev, false, child, 																	idBitsToAssign)) {
                                // Child wants to receive touch within its bounds
                                alreadyDispatchedToNewTouchTarget = true;
                                break;
                            }
                        if (preorderedList != null) preorderedList.clear();
            }

            // Dispatch to touch targets.
            if (mFirstTouchTarget == null) {
                // No touch targets so treat this as an ordinary view.
                handled = dispatchTransformedTouchEvent(ev, canceled, null,
                        TouchTarget.ALL_POINTER_IDS);
            } else {
                while (target != null) {
                    final TouchTarget next = target.next;
                    if (alreadyDispatchedToNewTouchTarget && target == newTouchTarget) {
                        handled = true;
                    } else {
                        final boolean cancelChild = resetCancelNextUpFlag(target.child)
                                || intercepted;
                        if (dispatchTransformedTouchEvent(ev, cancelChild,
                                target.child, target.pointerIdBits)) {
                            handled = true;
                        }
                    }
                    predecessor = target;
                    target = next;
                }
            }
        return handled;
    }


    /**
     * Transforms a motion event into the coordinate space of a particular child view,
     * filters out irrelevant pointer ids, and overrides its action if necessary.
     * If child is null, assumes the MotionEvent will be sent to this ViewGroup instead.
     */
    private boolean dispatchTransformedTouchEvent(MotionEvent event, boolean cancel,
            View child, int desiredPointerIdBits) {
        final boolean handled;

        // Perform any necessary transformations and dispatch.
        if (child == null) {
            //chile 为空调用自身的dispatchTouchEvent
            handled = super.dispatchTouchEvent(transformedEvent);
        } else {
            final float offsetX = mScrollX - child.mLeft;
            final float offsetY = mScrollY - child.mTop;
            transformedEvent.offsetLocation(offsetX, offsetY);
            if (! child.hasIdentityMatrix()) {
                transformedEvent.transform(child.getInverseMatrix());
            }
            // child不为空，调用子view的dispatchTouchEvent
            handled = child.dispatchTouchEvent(transformedEvent);
        }

        // Done.
        transformedEvent.recycle();
        return handled;
    }

/**
  *    返回true，即事件停止往下传递（需手动设置，即复写onInterceptTouchEvent）
  *    返回false = 不拦截（默认）
  */
    public boolean onInterceptTouchEvent(MotionEvent ev) {
        if (ev.isFromSource(InputDevice.SOURCE_MOUSE)
                && ev.getAction() == MotionEvent.ACTION_DOWN
                && ev.isButtonPressed(MotionEvent.BUTTON_PRIMARY)
                && isOnScrollbarThumb(ev.getX(), ev.getY())) {
            return true;
        }
        return false;
    }
```

调用`onInterceptTouchEvent(MotionEvent ev)`

* 返回值为true（手动设置拦截或无view接收事件，点击到空白处 ），则会调用`super.dispatchTouchEvent(ev)`，即`View.dispatchTouchEvent(ev)`；

* 返回值为false（不拦截事件），则事件会向子View传递，即调用`child.dispatchTouchEvent(ev)`；

>总结
>
>1. 当前dispatchTouchEvent的返回值取决于`super.dispatchTouchEvent(ev)`和`child.dispatchTouchEvent(ev)`的返回值；
>
>2. 当返回值为true时，表示事件被当前的ViewGroup消费，或者是被子View消费；返回值为false时，表示事件没有消费；



### 4.   事件在View中的传递

**View#dispatchTouchEvent(ev)**

```java
/**
     * Pass the touch screen motion event down to the target view, or this
     * view if it is the target.
     *
     * @param event The motion event to be dispatched.
     * @return True if the event was handled by the view, false otherwise.
     */
    public boolean dispatchTouchEvent(MotionEvent event) {
        boolean result = false;
        if (onFilterTouchEventForSecurity(event)) {
            //noinspection SimplifiableIfStatement
            ListenerInfo li = mListenerInfo;
            if (li != null && li.mOnTouchListener != null
                    && (mViewFlags & ENABLED_MASK) == ENABLED
                    && li.mOnTouchListener.onTouch(this, event)) {
                result = true;
            }

            if (!result && onTouchEvent(event)) {
                result = true;
            }
        }
        return result;
    }
```

* **判断1：mOnTouchListener != null**  

​       表示mOnTouchListener变量在`View.setOnTouchListener（）`方法里赋值，监听不为空；

* **判断2：mViewFlags & ENABLED_MASK) == ENABLED**  

  该条件是判断当前点击的控件是否enable；

* **判断3：mOnTouchListener.onTouch(this, event)**  

  如果mOnTouchListener不为空，回调控件注册Touch事件时的onTouch();



如果上面3个判断全为true，  则result =true， dispatchTouchEvent(ev)返回true，事件分发结束；

如果判断条件不全成立，则result = false，继续调用`onTouchEvent(event)`；

**View#onTouchEvent(ev)**

```java
 /**
     * Implement this method to handle touch screen motion events.
     * If this method is used to detect click actions, it is recommended that
     * the actions be performed by implementing and calling performClick(). This will 		 * ensure consistent system behavior,
     * @param event The motion event.
     * @return True if the event was handled, false otherwise.
     */
    public boolean onTouchEvent(MotionEvent event) {
        final float x = event.getX();
        final float y = event.getY();
        final int viewFlags = mViewFlags;
        final int action = event.getAction();

        final boolean clickable = ((viewFlags & CLICKABLE) == CLICKABLE
                || (viewFlags & LONG_CLICKABLE) == LONG_CLICKABLE)
                || (viewFlags & CONTEXT_CLICKABLE) == CONTEXT_CLICKABLE;

        if (clickable || (viewFlags & TOOLTIP) == TOOLTIP) {
            switch (action) {
              case MotionEvent.ACTION_UP:
                 if (!mHasPerformedLongPress && !mIgnoreNextUpEvent) {
                    // This is a tap, so remove the longpress check
                            removeLongPressCallback();

                    // Only perform take click actions if we were in the pressed state
                            if (!focusTaken) {
                                // Use a Runnable and post this rather than calling
                                // performClick directly. This lets other visual state
                                // of the view update before click actions start.
                                if (mPerformClick == null) {
                                    mPerformClick = new PerformClick();
                                }
                                if (!post(mPerformClick)) {
                                    performClickInternal();//内部会调用performClick()；
                                }
                            }
                        }
                        removeTapCallback();
                    break;       
                }
            return true;
        }

        return false;
    }
```

```java
public boolean performClick() {
        final boolean result;
        final ListenerInfo li = mListenerInfo;
        if (li != null && li.mOnClickListener != null) {
            playSoundEffect(SoundEffectConstants.CLICK);
            li.mOnClickListener.onClick(this);
            result = true;
        } else {
            result = false;
        }
        return result;
    }
```

从上面的源码可以看到，View中的`onTouchEvent（ev）`包含了对点击事件的处理；





### 5.  Demo示例

现有Activity包含如下简单的层级包含布局，即Activity包含一个ViewGroup，ViewGroup包含一个View；

```xml
<com.example.motionevent.view.MyViewGroup xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:background="#00796B"
    tools:context=".MainActivity">

    <com.example.motionevent.view.MyView
        android:id="@+id/my_view"
        android:layout_width="200dp"
        android:layout_height="200dp"
        android:layout_gravity="center"
        android:background="#01579B" />

</com.example.motionevent.view.MyViewGroup>
```



> 下面从View是否消费事件，ViewGroup是否拦截、是否消费事件等 6个场景进行示例；

(1) `View#onTouchEvent(ev)` 返回**false**； `ViewGroup#onTouchEvent(ev)`返回**false**；

View和ViewGroup都不消费事件，最后交给activity消费；

```java
com.example.motionevent E/activity: dispatch: ACTION_DOWN
com.example.motionevent E/viewGroup: dispatch: ACTION_DOWN
com.example.motionevent E/view: dispatch: ACTION_DOWN
com.example.motionevent E/view: onTouchEvent: ACTION_DOWN
com.example.motionevent E/view: onTouchEvent: false
com.example.motionevent E/view: dispatch: false
com.example.motionevent E/viewGroup: onTouchEvent: ACTION_DOWN
com.example.motionevent E/viewGroup: onTouchEvent: false
com.example.motionevent E/viewGroup: dispatch: false
com.example.motionevent E/activity: onTouchEvent: ACTION_DOWN  //activity消费           
com.example.motionevent E/activity: onTouchEvent: false                              
com.example.motionevent E/activity: dispatch: false                                  
com.example.motionevent E/activity: dispatch: ACTION_MOVE                            
com.example.motionevent E/activity: onTouchEvent: ACTION_MOVE  //activity消费             
com.example.motionevent E/activity: onTouchEvent: false                              
com.example.motionevent E/activity: dispatch: false                                  
com.example.motionevent E/activity: dispatch: ACTION_UP                              
com.example.motionevent E/activity: onTouchEvent: ACTION_UP    //activity消费             
com.example.motionevent E/activity: onTouchEvent: false                              
com.example.motionevent E/activity: dispatch: false                                  

    
 //当点击到ViewGroup的空白处，没有点击到内部view时；
com.example.motionevent E/activity: dispatch: ACTION_DOWN
com.example.motionevent E/viewGroup: dispatch: ACTION_DOWN
com.example.motionevent E/viewGroup: onTouchEvent: ACTION_DOWN
com.example.motionevent E/viewGroup: onTouchEvent: false
com.example.motionevent E/viewGroup: dispatch: false
com.example.motionevent E/activity: onTouchEvent: ACTION_DOWN             
com.example.motionevent E/activity: onTouchEvent: false                   
com.example.motionevent E/activity: dispatch: false                       
com.example.motionevent E/activity: dispatch: ACTION_MOVE                 
com.example.motionevent E/activity: onTouchEvent: ACTION_MOVE             
com.example.motionevent E/activity: onTouchEvent: false                   
com.example.motionevent E/activity: dispatch: false                       
com.example.motionevent E/activity: dispatch: ACTION_UP                   
com.example.motionevent E/activity: onTouchEvent: ACTION_UP               
com.example.motionevent E/activity: onTouchEvent: false                   
com.example.motionevent E/activity: dispatch: false                       
```

(2) `View#onTouchEvent(ev)` 返回**true**； `ViewGroup#onTouchEvent(ev)`返回**false**；

View消费事件；

```
com.example.motionevent E/activity: dispatch: ACTION_DOWN
com.example.motionevent E/viewGroup: dispatch: ACTION_DOWN
com.example.motionevent E/view: dispatch: ACTION_DOWN
com.example.motionevent E/view: onTouchEvent: ACTION_DOWN    //view消费
com.example.motionevent E/view: onTouchEvent: true
com.example.motionevent E/view: dispatch: true
com.example.motionevent E/viewGroup: dispatch: true
com.example.motionevent E/activity: dispatch: true
com.example.motionevent E/activity: dispatch: ACTION_MOVE
com.example.motionevent E/viewGroup: dispatch: ACTION_MOVE
com.example.motionevent E/view: dispatch: ACTION_MOVE
com.example.motionevent E/view: onTouchEvent: ACTION_MOVE    //view消费
com.example.motionevent E/view: onTouchEvent: true
com.example.motionevent E/view: dispatch: true
com.example.motionevent E/viewGroup: dispatch: true
com.example.motionevent E/activity: dispatch: true
com.example.motionevent E/activity: dispatch: ACTION_UP
com.example.motionevent E/viewGroup: dispatch: ACTION_UP
com.example.motionevent E/view: dispatch: ACTION_UP
com.example.motionevent E/view: onTouchEvent: ACTION_UP    //view消费
com.example.motionevent E/view: onTouchEvent: true
com.example.motionevent E/view: dispatch: true
com.example.motionevent E/viewGroup: dispatch: true
com.example.motionevent E/activity: dispatch: true
```

(3) `View#onTouchEvent(ev)` 返回**false**； `ViewGroup#onTouchEvent(ev)`返回**true**；

ViewGroup消费事件；

```
com.example.motionevent E/activity: dispatch: ACTION_DOWN
com.example.motionevent E/viewGroup: dispatch: ACTION_DOWN
com.example.motionevent E/view: dispatch: ACTION_DOWN
com.example.motionevent E/view: onTouchEvent: ACTION_DOWN
com.example.motionevent E/view: onTouchEvent: false
com.example.motionevent E/view: dispatch: false
com.example.motionevent E/viewGroup: onTouchEvent: ACTION_DOWN
com.example.motionevent E/viewGroup: onTouchEvent: true   //viewgroup消费
com.example.motionevent E/viewGroup: dispatch: true
com.example.motionevent E/activity: dispatch: true
com.example.motionevent E/activity: dispatch: ACTION_MOVE
com.example.motionevent E/viewGroup: dispatch: ACTION_MOVE
com.example.motionevent E/viewGroup: onTouchEvent: ACTION_MOVE   //viewgroup消费
com.example.motionevent E/viewGroup: onTouchEvent: true
com.example.motionevent E/viewGroup: dispatch: true
com.example.motionevent E/activity: dispatch: true
com.example.motionevent E/activity: dispatch: ACTION_UP
com.example.motionevent E/viewGroup: dispatch: ACTION_UP
com.example.motionevent E/viewGroup: onTouchEvent: ACTION_UP   //viewgroup消费
com.example.motionevent E/viewGroup: onTouchEvent: true
com.example.motionevent E/viewGroup: dispatch: true
com.example.motionevent E/activity: dispatch: true
```

(4) `ViewGroup#onInterceptTouchEvent(ev)` 返回**false**；

​      `ViewGroup#onTouchEvent(ev)`返回**false**；`View#onTouchEvent(ev)` 返回**false**

VIewGroup不拦截，场景同（1）；

```
com.example.motionevent E/activity: dispatch: ACTION_DOWN
com.example.motionevent E/viewGroup: dispatch: ACTION_DOWN
com.example.motionevent E/viewGroup: onIntercept: ACTION_DOWN
com.example.motionevent E/viewGroup: onIntercept: false
com.example.motionevent E/view: dispatch: ACTION_DOWN
com.example.motionevent E/view: onTouchEvent: ACTION_DOWN
com.example.motionevent E/view: onTouchEvent: false
com.example.motionevent E/view: dispatch: false
com.example.motionevent E/viewGroup: onTouchEvent: ACTION_DOWN
com.example.motionevent E/viewGroup: onTouchEvent: false
com.example.motionevent E/viewGroup: dispatch: false
com.example.motionevent E/activity: onTouchEvent: ACTION_DOWN      //activity消费         
com.example.motionevent E/activity: onTouchEvent: false                   
com.example.motionevent E/activity: dispatch: false                       
com.example.motionevent E/activity: dispatch: ACTION_MOVE                 
com.example.motionevent E/activity: onTouchEvent: ACTION_MOVE      //activity消费       
com.example.motionevent E/activity: onTouchEvent: false                   
com.example.motionevent E/activity: dispatch: false                       
com.example.motionevent E/activity: dispatch: ACTION_UP                   
com.example.motionevent E/activity: onTouchEvent: ACTION_UP        //activity消费       
com.example.motionevent E/activity: onTouchEvent: false                   
com.example.motionevent E/activity: dispatch: false   
```

(5) `ViewGroup#onInterceptTouchEvent(ev)` 返回**true**；

​      `ViewGroup#onTouchEvent(ev)`返回**false**；

ViewGroup拦截，但是不消费，最后会交给activity消费；

```
com.example.motionevent E/activity: dispatch: ACTION_DOWN
com.example.motionevent E/viewGroup: dispatch: ACTION_DOWN
com.example.motionevent E/viewGroup: onIntercept: ACTION_DOWN
com.example.motionevent E/viewGroup: onIntercept: true
com.example.motionevent E/viewGroup: onTouchEvent: ACTION_DOWN
com.example.motionevent E/viewGroup: onTouchEvent: false
com.example.motionevent E/viewGroup: dispatch: false
com.example.motionevent E/activity: onTouchEvent: ACTION_DOWN      //activity消费
com.example.motionevent E/activity: onTouchEvent: false           
com.example.motionevent E/activity: dispatch: false               
com.example.motionevent E/activity: dispatch: ACTION_MOVE          
com.example.motionevent E/activity: onTouchEvent: ACTION_MOVE      //activity消费
com.example.motionevent E/activity: onTouchEvent: false           
com.example.motionevent E/activity: dispatch: false               
com.example.motionevent E/activity: dispatch: ACTION_UP           
com.example.motionevent E/activity: onTouchEvent: ACTION_UP        //activity消费
com.example.motionevent E/activity: onTouchEvent: false           
com.example.motionevent E/activity: dispatch: false                                       
```

(6) `ViewGroup#onInterceptTouchEvent(ev)` 返回**true**；

​      `ViewGroup#onTouchEvent(ev)`返回**true**；

ViewGroup拦截，并消费；

```
com.example.motionevent E/activity: dispatch: ACTION_DOWN
com.example.motionevent E/viewGroup: dispatch: ACTION_DOWN
com.example.motionevent E/viewGroup: onIntercept: ACTION_DOWN
com.example.motionevent E/viewGroup: onIntercept: true
com.example.motionevent E/viewGroup: onTouchEvent: ACTION_DOWN    //viewgroup消费
com.example.motionevent E/viewGroup: onTouchEvent: true
com.example.motionevent E/viewGroup: dispatch: true
com.example.motionevent E/activity: dispatch: true
com.example.motionevent E/activity: dispatch: ACTION_MOVE
com.example.motionevent E/viewGroup: dispatch: ACTION_MOVE
com.example.motionevent E/viewGroup: onTouchEvent: ACTION_MOVE     //viewgroup消费
com.example.motionevent E/viewGroup: onTouchEvent: true
com.example.motionevent E/viewGroup: dispatch: true
com.example.motionevent E/activity: dispatch: true
com.example.motionevent E/activity: dispatch: ACTION_MOVE
com.example.motionevent E/viewGroup: dispatch: ACTION_MOVE
com.example.motionevent E/viewGroup: onTouchEvent: ACTION_MOVE    //viewgroup消费
com.example.motionevent E/viewGroup: onTouchEvent: true
com.example.motionevent E/viewGroup: dispatch: true
com.example.motionevent E/activity: dispatch: true
com.example.motionevent E/activity: dispatch: ACTION_UP
com.example.motionevent E/viewGroup: dispatch: ACTION_UP
com.example.motionevent E/viewGroup: onTouchEvent: ACTION_UP     //viewgroup消费
com.example.motionevent E/viewGroup: onTouchEvent: true
com.example.motionevent E/viewGroup: dispatch: true
com.example.motionevent E/activity: dispatch: true
```







### 参考资料

1. [Android事件分发机制前篇——事件如何传递到Activity中  作者：Dpal ](https://www.jianshu.com/p/faedef1910fe)

2. [Android事件分发机制详解：史上最全面、最易懂   作者: Carson_Ho](https://www.jianshu.com/p/38015afcdb58/)