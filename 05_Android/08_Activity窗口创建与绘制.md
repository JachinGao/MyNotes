# 一、Acitivity窗口创建与绘制



## 1. ActivityRecord

ActivityRecord是AMS调度Activity的基本单位，它需要记录AndroidManifest.xml中所定义Activity的静态特征，同时， 记录Activity在被调度时的状态变化。

系统进程维护的是ActivityRecord，应用进程维护的是Activity，两者之间的映射关系就是利用Token来维系的。 应用进程的Activity在创建的时候，就被赋予了一个Token。

ActivityRecord的appToken在其构造函数中被创建，所以每个ActivityRecord拥有其各自的appToken。而WMS接受AMS对Token的声明，并为appToken创建了唯一的一个AppWindowToken。因此，这个类型为IApplicationToken的Binder对象appToken粘结了AMS的ActivityRecord与WMS的AppWindowToken，只要给定一个ActivityRecord，都可以通过appToken在WMS中找到一个对应的AppWindowToken，从而使得AMS拥有了操纵Activity的窗口绘制的能力。

例如，当AMS认为一个Activity需要被隐藏时，以Activity对应的ActivityRecord所拥有的appToken作为参数调用WMS的setAppVisibility()函数。此函数通过appToken找到其对应的AppWindowToken，然后将属于这个Token的所有窗口隐藏。



## 2. Token的传递

### (1) Token的创建

* Token持有了一个ActivityRecord实例的弱引用。在创建一个ActivityRecord的时候，就会创建了一个Token类型的对象;
* 构造一个ActivityRecord时，会将自己(this)传给Token，变量**ActivityRecord.appToken**存的就是最终创建出来的Token;

```java
final class ActivityRecord {
    
    final IApplicationToken.Stub appToken; // window manager token
    	...
 		ActivityRecord(ActivityManagerService _service, 
 			ProcessRecord _caller, ...) {
        service = _service;
        appToken = new Token(this, service);
        }
    
    //ActivityRecord中的一个内部类Token;
    static class Token extends IApplicationToken.Stub {
        private final WeakReference<ActivityRecord> weakActivity;
        private final ActivityManagerService mService;

        Token(ActivityRecord activity, ActivityManagerService service) {
            weakActivity = new WeakReference<>(activity);
            mService = service;
        }
    }
}
```



### (2) 将Token传递给Activity

1. 在AMS进程调用realStartActivityLocked()，进而调用ApplicationThread中的scheduleLaunchActivity()方法，参数包含token对象，最后创建一个Activity实例；
2. 进入到ActivityThread中，通过调用performLaunchActivity()方法，进而调用Activity.attach()方法，建立应用进程的Activity与系统进程中ActivityRecord的关联。
3. 在ActivityThread应用进程中，会以token为Key保存ActivityClientRecord对象到mActivitys这个Arraymap中；



realStartActivityLocked()#**ActivityStackSupervisor**

```java
final boolean realStartActivityLocked(ActivityRecord r,
        ProcessRecord app, boolean andResume, boolean checkConfig)
        throws RemoteException {
   			 ...
    app.thread.scheduleLaunchActivity(new Intent(r.intent), r.appToken,
   					System.identityHashCode(r), r.info, 
   					new  Configuration(mService.mConfiguration),
                    new Configuration(stack.mOverrideConfig), r.compat, 									r.launchedFromPackage,task.voiceInteractor, 											app.repProcState, r.icicle, r.persistentState, 											results,newIntents, !andResume, 			                 			 				mService.isNextTransitionForward(), profilerInfo);
			...                    
                    
  }
```



performLaunchActivity()**ActivityThread**

```java
final ArrayMap<IBinder, ActivityClientRecord> mActivities = new ArrayMap<>();

private Activity performLaunchActivity(ActivityClientRecord r, Intent customIntent) {
  	...
     activity.attach(appContext, this, getInstrumentation(), r.token,
                        r.ident, app, r.intent, r.activityInfo, title, r.parent,
                        r.embeddedID, r.lastNonConfigurationInstances, config,
                        r.referrer, r.voiceInteractor);
     ...
     //以ActivityClientRecord.token为Key保存了ActivityClientRecord
	mActivities.put(r.token, r);
 }
```

attach()#**Activity**

```java
    private IBinder mToken; 
final void attach(Context context, ActivityThread aThread,
            Instrumentation instr, IBinder token, int ident,
            Application application, Intent intent, ActivityInfo info,
            CharSequence title, Activity parent, String id,
            NonConfigurationInstances lastNonConfigurationInstances,
            Configuration config, String referrer, IVoiceInteractor voiceInteractor) {
        	attachBaseContext(context);

        mWindow = new PhoneWindow(this);
        mWindow.setCallback(this);
        mWindow.setOnWindowDismissedCallback(this);
        mWindow.getLayoutInflater().setPrivateFactory(this);      

        mInstrumentation = instr;
        mToken = token;
        mIdent = ident;
        mApplication = application;
        mIntent = intent;
        mReferrer = referrer;
        mComponent = intent.getComponent();
        mActivityInfo = info;
        mTitle = title;
        mParent = parent;
        mEmbeddedID = id;
        ...
 }    
```



### (3) 将Token传递给WMS

将Token传递给WMS时，会创建AppWindowToken对象，并将Token加入到mTokenMap中。

首先，在启动一个Activity时，会调用startActivityLocked()来在WMS中添加一个AppWindowToken对象；

startActivityLocked()#**ActivityStack**

```java
private final void startActivityLocked(ActivityRecord r, boolean newTask,
            boolean doResume){
                 addConfigOverride(r, task);
       }
            
void addConfigOverride(ActivityRecord r, TaskRecord task) {
  final Rect bounds = task.updateOverrideConfigurationFromLaunchBounds();
  
  mWindowManager.addAppToken(task.mActivities.indexOf(r), r.appToken,
     r.task.taskId, mStackId, r.info.screenOrientation, r.fullscreen,
     (r.info.flags & FLAG_SHOW_FOR_ALL_USERS) != 0, r.userId,r.info.configChanges,
     task.voiceSession != null, r.mLaunchTaskBehind, bounds, task.mOverrideConfig,
                task.mResizeMode, r.isAlwaysFocusable(), task.isHomeTask(),
                r.appInfo.targetSdkVersion);
        r.taskConfigOverride = task.mOverrideConfig;
    }
```

addAppToken()#**WMS**

```java
final HashMap<IBinder, WindowToken> mTokenMap = new HashMap<>();

@Override
    public void addAppToken(int addPos, IApplicationToken token, int taskId, int stackId,int requestedOrientation, boolean fullscreen, boolean showForAllUsers, int userId,int configChanges, boolean voiceInteraction, boolean launchTaskBehind,
 Rect taskBounds, Configuration config, int taskResizeMode, boolean alwaysFocusable,
            boolean homeTask, int targetSdkVersion) {
        
        synchronized(mWindowMap) {
            AppWindowToken atoken = findAppWindowToken(token.asBinder());
            if (atoken != null) {
                Slog.w(TAG_WM, "Attempted to add existing app token: " + token);
                return;
            }
            atoken = new AppWindowToken(this, token, voiceInteraction);
            atoken.inputDispatchingTimeoutNanos = inputDispatchingTimeoutNanos;
            atoken.appFullscreen = fullscreen;
            atoken.showForAllUsers = showForAllUsers;
            atoken.targetSdk = targetSdkVersion;
            atoken.requestedOrientation = requestedOrientation;
            atoken.layoutConfigChanges = (configChanges &
                    (ActivityInfo.CONFIG_SCREEN_SIZE | ActivityInfo.CONFIG_ORIENTATION)) != 0;
            atoken.mLaunchTaskBehind = launchTaskBehind;
            atoken.mAlwaysFocusable = alwaysFocusable;        

            Task task = mTaskIdToTask.get(taskId);
            if (task == null) {
                task = createTaskLocked(taskId, stackId, userId, atoken, taskBounds, config);
            }
            task.addAppToken(addPos, atoken, taskResizeMode, homeTask);

            mTokenMap.put(token.asBinder(), atoken);

            // Application tokens start out hidden.
            atoken.hidden = true;
            atoken.hiddenRequested = true;
        }
    }
```

成员变量mTokenMap指向的是一个HashMap，它里面保存的是一系列的WindowToken对象，每一个WindowToken对象都是用来描述一个窗口的，并且是以描述这些窗口的一个Binder对象的IBinder接口为键值的。例如，对于Activity组件类型的窗口来说，它们分别是以用来描述它们的一个ActivityRecord对象的IBinder接口保存在成员变量mTokenMap所指向的一个HashMap中的。

通过这个过程我们就可以知道，每一个Activity组件，在ActivityManagerService服务内部都有一个对应的ActivityRecord对象，并且在WindowManagerService服务内部关联有一个AppWindowToken对象。





# 二、Activity与WMS的交互过程

我们从两个角度分析：

一方面是从Activity组件到WindowManagerService服务的连接；

另一方面是从WindowManagerService服务到Activity组件的连接。



## 1. 概述

### (1) 从Activity组件到WindowManagerService服务的连接

以Activity组件所在的应用程序进程为单位来进行。当一个应用程序进程在启动第一个Activity组件的时候，它便会打开一个到WindowManagerService服务的连接，这个连接以应用程序进程从WindowManagerService服务处获得一个实现了**IWindowSession**接口的**Session**代理对象来标志。

Session类实现了**IWindowSession**接口，因此，应用程序进程就可以通过保存在ViewRoot类的静态成员变量sWindowSession所描述的一个Session代理对象所实现的IWindowSession接口来与WindowManagerService服务通信。

1. 在Activity组件的启动过程中，调用这个IWindowSession接口的成员函数addToDisplay可以将一个关联的W对象传递到WindowManagerService服务，以便WindowManagerService服务可以为该Activity组件创建一个WindowState对象。

2. 在Activity组件的销毁过程中，调用这个这个IWindowSession接口的成员函数remove来请求WMS服务之前为该Activity组件所创建的一个WindowState对象

3. 在Activity组件的运行过程中，调用这个这个IWindowSession接口的成员函数relayout来**请求WMS服务来对该Activity组件的UI进行布局**，以便该Activity组件的UI可以正确地显示在屏幕中。



### (2) 从WindowManagerService服务到Activity组件的连接

以Activity组件为单位来进行的。在应用程序进程这一侧，每一个Activity组件都关联一个实现了**IWindow**接口的**W**对象，这个W对象在Activity组件的视图对象创建完成之后，就会通过前面所获得一个Session代理对象来传递给WindowManagerService服务，而WindowManagerService服务接收到这个W对象之后，就会在内部创建一个WindowState对象来描述与该W对象所关联的Activity组件的窗口状态，并且以后就通过这个W对象来控制对应的Activity组件的窗口状态。

W类实现了**IWindow**接口，因此，WindowManagerService服务就可以通过它在内部所创建的WindowState对象的成员变量mClient来要求运行在应用程序进程这一侧的Activity组件来配合管理窗口的状态。

1. 当一个Activity组件的**窗口的大小发生改变**后，WindowManagerService服务就会调用这个IWindow接口的成员函数resized来通知该Activity组件，它的大小发生改变了。
2. 当一个Activity组件的**窗口的可见性**之后，WindowManagerService服务就会调用这个IWindow接口的成员函数dispatchAppVisibility来通知该Activity组件，它的可见性发生改变了。
3. 当一个Activity组件的**窗口获得或者失去焦点**之后，WindowManagerService服务就会调用这个IWindow接口的成员函数windowFoucusChanged来通知该Activity组件，它的焦点发生改变了。



## 2. 应用程序进程请求WMS服务创建Session对象

(1) session创建和W的创建都从ViewRootImpl的构造开始；

```java
public ViewRootImpl(Context context, Display display) {
    mContext = context;
    mWindowSession = WindowManagerGlobal.getWindowSession();
    mWindow = new W(this);  //W在ViewRootImpl的构造开始创建，返回值为mWindow；
}
```

(2) getWindowSession()#**WindowManagerGlobal**

```java
public static IWindowSession getWindowSession() {
    synchronized (WindowManagerGlobal.class) {
        if (sWindowSession == null) {
            try {
                InputMethodManager imm = InputMethodManager.getInstance();
                IWindowManager windowManager = getWindowManagerService();
                sWindowSession = windowManager.openSession(
                        new IWindowSessionCallback.Stub() {
                            @Override
                            public void onAnimatorScaleChanged(float scale) {
                                ValueAnimator.setDurationScale(scale);
                            }
                        },
                        imm.getClient(), imm.getInputContext());
            } catch (RemoteException e) {
                throw e.rethrowFromSystemServer();
            }
        }
        return sWind\owSession;
    }
}
```

(3) 获取WMS代理对象，最终调用的还是WMS中的openSession()成员函数，返回值为Session对象；所以sWindowSession就是Session的远程代理。

```
@Override
public IWindowSession openSession(IWindowSessionCallback callback, IInputMethodClient client,IInputContext inputContext) {
    if (client == null) throw new IllegalArgumentException("null client");
    if (inputContext == null) throw new IllegalArgumentException("null inputContext");
    Session session = new Session(this, callback, client, inputContext);
    return session;
}
```



## 3. 应用程序进程请求WMS为Activity创建WindowState对象

在Android系统中，WindowManagerService服务负责统一管理系统中的所有窗口，因此，当运行在应用程序进程这一侧的Activity组件在启动完成之后，需要与WindowManagerService服务建立一个连接，以便WindowManagerService服务可以管理它的窗口。

具体来说，就是应用程序进程将一个Activitty组件的视图对象设置到与它所关联的一个ViewRoot对象的内部的时候，就会将一个实现了IWindow接口的Binder本地对象mWindw传递WindowManagerService服务。这个实现了IWindow接口的Binder本地对象唯一地标识了一个Activity组件，WindowManagerService服务接收到了这个Binder本地对象之后，就会将它保存在一个新创建的WindowState对象的内部，这样WindowManagerService服务以后就可以通过它来和Activity组件通信，以便可以要求Activity组件配合来管理系系统中的所有窗口。



(1) 调用setView#**ViewRootImpl**

```java
public void setView(View view, WindowManager.LayoutParams attrs, View panelParentView){
     synchronized (this) {
        if (mView == null) {
             mView = view;
             ...
             requestLayout();
             ...
	         res = mWindowSession.addToDisplay(mWindow, mSeq, mWindowAttributes,
                           getHostVisibility(), mDisplay.getDisplayId(),
                           mAttachInfo.mContentInsets, mAttachInfo.mStableInsets,
                           mAttachInfo.mOutsets, mInputChannel);
        }
     }
}           		            		
```

(2) 在addToDisplay#Session中，实际上又调用了WMS中的方法；

addToDisplay#**Session**

```
 mService.addWindow(this, window, seq, attrs, viewVisibility, displayId,
             		outContentInsets, outStableInsets, outOutsets, outInputChannel);
```

(3) 最终实现是在WMS中；

1. addWindow()内部做一些合法性检查后，就会创建WindowState对象；

2. 接下来再调用前面所创建的一个WindowState对象win的成员函数attach来为当前正在增加的窗口创建一个用来连接到SurfaceFlinger服务的SurfaceSession对象。有了这个SurfaceSession对象之后，当前正在增加的窗口就可以和SurfaceFlinger服务通信。

3. 最后还做了两件事情。

   第一件事情是将前面所创建的一个WindowState对象win添加到WindowManagerService类的成员变量mWindowMap所描述的一个HashMap中，这是以参数所描述的一个类型为W的Binder代理对象的IBinder接口来键值来保存的。

   第二件事情是检查当前正在增加的是否是一个起始窗口，如果是的话，那么就会将前面所创建的一个WindowState对象win设置到用来描述它所属的Activity组件的一个AppWindowToken对象的成员变量startingWindow中去，这样系统就可以在显示这个Activity组件的窗口之前，先显示该起始窗口。

addWindow**#WMS**

```java
 public int addWindow(Session session, IWindow client, int seq,
            WindowManager.LayoutParams attrs, int viewVisibility, int displayId,
            Rect outContentInsets, Rect outStableInsets, Rect outOutsets,
            InputChannel outInputChannel) {
     ...
     WindowState win = new WindowState(this, session, client, token,
           attachedWindow, appOp[0], seq, attrs, viewVisibility, displayContent);
     
      win.attach(); //SurfaceSession对象的创建
      mWindowMap.put(client.asBinder(), win);
           
      if (type == TYPE_APPLICATION_STARTING && token.appWindowToken != null) {
                token.appWindowToken.startingWindow = win;
            }
 }                  
```



## 4. SurfaceSession对象的创建

继续调用前面所创建的一个WindowState对象的成员函数attach来创建一个关联的SurfaceSession对象，以便可以用来和SurfaceFlinger服务通信。

(1) attach()#**WindowState**

```java
 void attach() {
     mSession.windowAddedLocked();
     }
```



 (2) windowAddedLocked()#**Session**

```java
SurfaceSession mSurfaceSession;   

void windowAddedLocked() {
        if (mSurfaceSession == null) {  
            mSurfaceSession = new SurfaceSession();
            mService.mSessions.add(this);
            if (mLastReportedAnimatorScale != mService.getCurrentAnimatorScale()) {
                mService.dispatchNewAnimatorScaleLocked(this);
            }
        }
        mNumWindow++;
    }
```



1. 这个SurfaceSession对象是WMS服务用来与SurfaceSession服务建立连接的。Session类的成员函数windowAddedLocked创建一个SurfaceSession对象，并且将正在处理的Session对象添加WMS类的成员变量mSession所描述的一个HashSet中去，表示WindowManagerService服务又多了一个激活的应用程序进程连接。

2. Session类的另外一个成员变量mNumWindow是一个整型变量，用来描述当前正在处理的Session对象内部包含有多少个窗口，即运行在引用了当前正在处理的Session对象的应用程序进程的内部的窗口的数量。每当运行在应用程序进程中的窗口销毁时，该应用程序进程就会通知WindowManagerService服务移除用来描述该窗口状态的一个WindowState对象，并且通知它所引用的Session对象减少其成员变量mNumWindow的值。当一个Session对象的成员变量mNumWindow的值减少为0时，就说明该Session对象所描述的应用程序进程连接已经不需要了，因此，该Session对象就可以杀掉其成员变量mSurfaceSession所描述的一个SurfaceSession对象，以便可以断开和SurfaceSession服务的连接。

(3) SurfaceSession对象的初始化

在SurfaceSession构造函数中，通多JNI调用，会创建一个SurfaceComposerClient对象client；使得每一个Java层的SurfaceSession对象在C++层就都有一个对应的SurfaceComposerClient对象，因此，Java层的应用程序就可以通过SurfaceSession类来和SurfaceFlinger服务建立连接。





# 三. Surface创建的过程

### 1.概述

在应用程序进程这一侧，每一个Java层的Surface对都对应有一个C++层的Surface对象，并且后者的地址值保存在前者的成员变量mNativeSurface中。

在WMS服务这一侧，每一个应用程序窗口，即每一个Activity组件，都有一个对应的WindowState对象，这个WindowState对象的成员变量mSurface同样是指向了一个Surface对象。每一个Java层的Surface对都对应有一个C++层的SurfaceControl对象，并且后者的地址值保存在前者的成员变量mSurfaceControl中。

一个应用程序窗口分别位于应用程序进程和WindowManagerService服务中的两个Surface对象有什么区别呢？

具体来说，就是位于应用程序进程这一侧的Surface对象负责绘制应用程序窗口的UI，即往应用程序窗口的图形缓冲区填充UI数据，而位于WindowManagerService服务这一侧的Surface对象负责设置应用程序窗口的属性，例如位置、大小等属性。这两种不同的操作方式分别是通过C++层的Surface对象和SurfaceControl对象来完成的。



### 2. Surface对象，那么它们是如何创建出来的呢？

创建过程可以分为如下四步：

(1) 应用程序进程请求WMS服务为一个应用程序窗口创建一个Surface对象；

(2) WMS服务请求SurfaceFlinger服务创建一个Layer对象，并且获得一个ISurface接口;

(3) WMS服务将获得的ISurface接口保存在其内部的Surface对象中，并且将该ISurface接口返回给应用程序进程；

(4) 应用程序进程得到WMS服务返回的ISurface接口之后，再将其封装成其内部的另外一个Surface对象。



### 3. 创建流程

1. 从第一次requestLayout开始进行分析

   

   requestLayout()#**ViewRootImpl**

   ```java
      @Override
       public void requestLayout() {
           if (!mHandlingLayoutInLayoutRequest) {
               checkThread();
               mLayoutRequested = true;
               scheduleTraversals();
           }
       }
   ```

   ```
   void scheduleTraversals()#ViewRootImpl
   void doTraversal()#ViewRootImpl
   performTraversals();#ViewRootImpl
   relayoutWindow()#ViewRootImpl
   ```

2. 远程调用relayout()

   ```java
    final Surface mSurface = new Surface();
    final W mWindow;
    
    private int relayoutWindow(WindowManager.LayoutParams params, int viewVisibility,
               boolean insetsPending) throws RemoteException {
        ...
        int relayoutResult = mWindowSession.relayout(mWindow, mSeq, params,
                   (int) (mView.getMeasuredWidth() * appScale + 0.5f),
                   (int) (mView.getMeasuredHeight() * appScale + 0.5f),
          viewVisibility,insetsPending?WindowManagerGlobal.RELAYOUT_INSETS_PENDING ：0,
                   mWinFrame, mPendingOverscanInsets, mPendingContentInsets, 	                           mPendingVisibleInsets,mPendingStableInsets,                                           mPendingOutsets, mPendingConfiguration, mSurface);
        ...
         return relayoutResult;
     }
   ```

   ```java
   
   interface IWindowSession {
       ......
    
       int relayout(IWindow window, in WindowManager.LayoutParams attrs,
               int requestedWidth, int requestedHeight, int viewVisibility,
               boolean insetsPending, out Rect outFrame, out Rect outContentInsets,
               out Rect outVisibleInsets, out Configuration outConfig,
               out Surface outSurface);
    
       ......
   }
   ```

   当运行在WindowManagerService服务内部的Session对象处理完成当前应用程序进程发送过来的类型为TRANSACTION_relayout的进程间通信请求之后，就会将处理结果写入到Parcel对象_reply中，并且将这个Parcel对象_reply返回给当前应用程序进程处理。返回结果包含了一系列与参数window所描述的应用程序窗口相关的参数，如下所示：

   ```java
      1. 窗口的大小：最终保存在输出参数outFrame中。
   
      2. 窗口的内容区域边衬大小：最终保存在输出参数outContentInsets中。
   
      3. 窗口的可见区域边衬大小：最终保存在输出参数outVisibleInsets中。
   
      4. 窗口的配置信息：最终保存在输出参数outConfig中。
   
      5. 窗口的绘图表面：最终保存在输出参数outSurface中。
   ```

3. 继续分析Session内的relayout()实现,其内部又调用了WMS内的relayoutWindow() 方法；

   ```java
       public int relayout(IWindow window, int seq, WindowManager.LayoutParams attrs,
               int requestedWidth, int requestedHeight, int viewFlags,
               int flags, Rect outFrame, Rect outOverscanInsets, Rect outContentInsets,
               Rect outVisibleInsets, Rect outStableInsets, Rect outsets, Configuration
                       outConfig,
               Surface outSurface) {
   
           int res = mService.relayoutWindow(this, window, seq, attrs,
                   requestedWidth, requestedHeight, viewFlags, flags,
                   outFrame, outOverscanInsets, outContentInsets, outVisibleInsets,
                   outStableInsets, outsets, outConfig, outSurface);
    
           return res;
       }
   ```

4. relayoutWindow()#WindowManagerService

   ```java
public int relayoutWindow(Session session, IWindow client,
               WindowManager.LayoutParams attrs, int requestedWidth,
               int requestedHeight, int viewVisibility, boolean insetsPending,
               Rect outFrame, Rect outContentInsets, Rect outVisibleInsets,
               Configuration outConfig, Surface outSurface) {
           ...
    		Surface surface = win.createSurfaceLocked();
      		
       	if (surface != null) {
                           outSurface.copyFrom(surface);
                           ......
                       } else {
                           // For some reason there isn't a surface.  Clear the
                           // caller's object so they see the same state.
                           outSurface.release();
                       }
       	...
   }
    
   ```
   
   
   
5. createSurfaceLocked()#WindowState

   ```java
    Surface createSurfaceLocked() {
    		...
     		mSurface = new Surface(mSession.mSurfaceSession, mSession.mPid,
              		              mAttrs.getTitle().toString(),
               	             0, w, h, mAttrs.format, flags);
           ...
           return mSurface;
    }
   ```

6. new Surface()

   ```java
   public class Surface implements Parcelable {
       ......
    
       private int mSurfaceControl;
       ......
       private Canvas mCanvas;
       ......
       private String mName;
       ......
    
       public Surface(SurfaceSession s,
               int pid, String name, int display, int w, int h, int format, int flags)
           throws OutOfResourcesException {
           ......
    
           mCanvas = new CompatibleCanvas();
           init(s,pid,name,display,w,h,format,flags);
           mName = name;
       }
    
       private native void init(SurfaceSession s,
               int pid, String name, int display, int w, int h, int format, int flags)
               throws OutOfResourcesException;
    
       ......
   }
   ```

   

   Surface类有三个成员变量mSurfaceControl、mCanvas和mName，它们的类型分别是int、Canvas和mName;这里我们主要关注与**成员变量mSurfaceControl所关联的C++层的SurfaceControl对象的创建**。

   **mSurfaceControl**保存的是在C++层的一个SurfaceControl对象的地址值;

   **mCanvas**用来描述一块类型为CompatibleCanvas的画布;

   **mName**用来描述当前正在创建的一个绘图表面的名称;

   

7. init()方法

   (1) Java层的SurfaceSession对象有一个成员变量mClient，它指向了在C++层SurfaceComposerClient对象。
   
   (2) 调用SurfaceComposerClient对象的成员函数createSurface来请求SurfaceFlinger服务来为参数clazz所描述的一个Java层的Surface对象所关联的应用程序窗口创建一个Layer对象。
   
   (3) SurfaceFlinger服务创建完成这个Layer对象之后，就会将该Layer对象内部的一个实现了ISurface接口的**SurfaceLayer**对象返回给函数，于是，函数就可以获得一个实现了ISurface接口的Binder代理对象。这个实现了ISurface接口的Binder代理对象被封装在C++层的一个SurfaceControl对象surface中。
   
   (4) 最后，SurfaceComposerClient类的成员函数createSurface就将SurfaceFlinger服务返回来的Binder代理对象surface和surface_data_t对象data封装成一个SurfaceControl对象result，并且返回给调用者。
   
   (5) 得到了SurfaceControl对象surface之后，会将它的地址值保存在参数clazz所指向的一个Java层的Surface对象的成员变量mSurfaceControl中，



8. 经过层层调用，最后可以得到：

   在WindowManagerService服务这一侧创建的Java层的Surface对象在C++层关联有一个SurfaceControl对象，用来设置应用窗口的属性，例如，大小和位置等。

   在应用程序进程这一侧创建的ava层的Surface对象在C++层关联有一个Surface对象，用来绘制应用程序窗品的UI。



# 四、应用程序窗口的测量、布局与绘制



## 1. 应用程序窗口的测量

(1) DecorView类的成员函数measure是从父类View继承下来，View类的成员函数measure()内部又会调用onMeasure()方法；

(2) 在viewGroup会循环遍历到每一个view的measure()方法进行测量；

```java
protected void measureChildWithMargins(View child,
            int parentWidthMeasureSpec, int widthUsed,
            int parentHeightMeasureSpec, int heightUsed) {
   		... 
        child.measure(childWidthMeasureSpec, childHeightMeasureSpec);
    }
```

(3) View类的成员函数onMeasure执行完成之后，需要再调用另外一个成员函数setMeasuredDimension来将测量好的宽度和高度设置到View类的成员变量mMeasuredWidth和mMeasuredHeight中，并且将成员变量mPrivateFlags的EASURED_DIMENSION_SET位设置为1。

这个操作是强制的，因为当前视图最终就是通过View类的成员变量mMeasuredWidth和mMeasuredHeight来获得它的宽度和高度的。为了保证这个操作是强制的，View类的成员函数measure再接下来就会检查成员变量mPrivateFlags的EASURED_DIMENSION_SET位是否被设置为1了。如果不是的话，那么就会抛出一个类型为IllegalStateException的异常来。



## 2. 应用程序窗口的布局

DecorView类的成员函数layout是从父类View继承下来，我们从View类的成员函数layout开始分析;

 View类的成员函数layout首先调用另外一个成员函数setFrame来设置当前视图的位置以及大小。设置完成之后，如果当前视图的大小或者位置与上次相比发生了变化，那么View类的成员函数setFrame的返回值changed就会等于true。在这种情况下， View类的成员函数layout就会继续调用另外一个成员函数onLayout重新布局当前视图的子视图。

layout()#**View**

```java
public final void layout(int l, int t, int r, int b) {
        boolean changed = setFrame(l, t, r, b);
        if (changed || (mPrivateFlags & LAYOUT_REQUIRED) == LAYOUT_REQUIRED) {
            ......
 
            onLayout(changed, l, t, r, b);
            mPrivateFlags &= ~LAYOUT_REQUIRED;
        }
        mPrivateFlags &= ~FORCE_LAYOUT;
    }
```

(1) 在setFrameI()方法中会有 invalidate()方法的调用；

(2) invalidate()

请求绘制当前视图的UI是通过调用View类的成员变量mParent所描述的一个ViewParent接口的成员函数invalidateChild来实现的。前面我们假设当前视图是应用程序窗口的顶层视图，即它是一个类型为DecoreView的视图，它的成员变量mParent指向的是与其所关联的一个ViewRoot对象。因此，绘制当前视图的UI的操作实际上是通过调用ViewRoot类的成员函数invalidateChild来实现.

ViewRoot类的成员函数invalidateChild首先调用另外一个成员函数checkThread来检查当前正在执行的是否是一个UI线程。如果不是的话，ViewRoot类的成员函数checkThread就会抛出一个异常出来。这是因为所有的UI操作都必须要在UI线程中执行。

调整好参数dirty所描述的一个需要重新绘制的区域之后， ViewRoot类的成员函数invalidateChild就将它所描述的一个区域与成员变量mDirty所描述的一区域执行一个合并操作，并且将得到的新区域保存在成员变量mDirty中。从这个操作就可以看出，ViewRoot类的成员变量mDirty描述的就是当前正在处理的应用程序窗口下一次所要重新绘制的总区域。

(3) onLayout()

View类的成员函数layout中，接下来它就会调用另外一个成员函数onLayout来重新布局当前视图的子视图的布局了。View类的成员函数onLayout是由子类来重写的，并且只有当该子类描述的是一个容器视图时，它才会重写父类View的成员函数onLayout。

DecorView类继承FrameLayout类，我们以FrameLayout类的成员函数onLayout()方法为例，分析其实现。

FrameLayout类的成员函数onLayout通过一个for循环来布局当前视图的每一个子视图。如果一个子视图child是可见的，那么FrameLayout类的成员函数onLayout就会根据当前视图可以用来显示子视图的区域以及它所设置的gravity属性来得到它在应用程序窗口中的左上角位置（childeLeft，childTop）。

当一个子视图child在应用程序窗口中的左上角位置确定了之后，再结合它在前面的测量过程中所确定的宽度width和高度height，我们就可以完全地确定它在应用程序窗口中的布局了，即可以调用它的成员函数layout来设置它的位置和大小。

## 3. 应用程序窗口的绘制

#### (1) draw()

**将成员变量mSurface**所描述的应用程序窗口的绘图表面保存在变量surface中，以便接下来可以通过变量surface来操作应用程序窗口的绘图表面。

**成员变量mDirty**描述的是一个矩形区域，表示应用程序窗口的脏区域，即需要重新绘制的区域。函数将这个脏区域保存变量dirty中，以便接下来可以使用。

**成员变量mUseGL**用来描述应用程序窗口是否直接使用OpenGL接口来绘制UI。

**参数fullRedrawNeeded**用来描述是否需要绘制应用程序窗口的所有区域。如果需要的话，那么就会将应用程序窗口的脏区域的大小设置为整个应用程序窗口的大小（0，0，mWidth，mHeight）



* 如果应用程序窗口的脏区域dirty不等于空，或者应用程序窗口在正处于动画状态，即成员变量mIsAnimating的值等于true，那么函数接下来就需要重新绘制应用程序窗口的UI了。

* 在绘制之前，首先会调用Surface对象的**成员函数lockCanvas**来创建一块画布canvas。有了这块画布之后，接下来就可以调用成员变量mView所描述的一个类型为DecorView的顶层视图的成员函数draw来在上面绘制应用程序窗口的UI。

* 绘制完成之后，应用程序窗口的UI就都体现在前面所创建的画布canvas上了，因此，这时候就需要将它交给SurfaceFlinger服务来渲染，这是通过调用Surface对象surface的**成员函数unlockCanvasAndPost**来实现的。

  

 draw()#**ViewRootImpl**

```java
private void draw(boolean fullRedrawNeeded) {
        Surface surface = mSurface;
        ...
        Rect dirty = mDirty;
        ...

        if (mUseGL) {
            if (!dirty.isEmpty()) {
                Canvas canvas = mGlCanvas;
                if (mGL != null && canvas != null) {
                    ......
 
                    int saveCount = canvas.save(Canvas.MATRIX_SAVE_FLAG);
                    try {
                        canvas.translate(0, -yoff);
                        if (mTranslator != null) {
                            mTranslator.translateCanvas(canvas);
                        }
                        canvas.setScreenDensity(scalingRequired
                                ? DisplayMetrics.DENSITY_DEVICE : 0);
                        mView.draw(canvas);
                        ......
                    } finally {
                        canvas.restoreToCount(saveCount);
                    }
                    ......
                }
            }
            if (scrolling) {
                mFullRedrawNeeded = true;
                scheduleTraversals();
            }
            return;
        }
 
        ......
 
        if (!dirty.isEmpty() || mIsAnimating) {
            Canvas canvas;
            try {
                ......
                canvas = surface.lockCanvas(dirty);
 
                ......
            } catch (Surface.OutOfResourcesException e) {
                ......
                return;
            } catch (IllegalArgumentException e) {
                ......
                return;
            }
 
            try {
                if (!dirty.isEmpty() || mIsAnimating) {
                    .....
 
                    mView.mPrivateFlags |= View.DRAWN;
 
                    ......
                    int saveCount = canvas.save(Canvas.MATRIX_SAVE_FLAG);
                    try {
                        canvas.translate(0, -yoff);
                        if (mTranslator != null) {
                            mTranslator.translateCanvas(canvas);
                        }
                        canvas.setScreenDensity(scalingRequired
                                ? DisplayMetrics.DENSITY_DEVICE : 0);
                        mView.draw(canvas);
                    } finally {
                        ......
                        canvas.restoreToCount(saveCount);
                    }
 
                    ......
                }
 
            } finally {
                surface.unlockCanvasAndPost(canvas);
            }
        }
 
        ......
 
        if (scrolling) {
            mFullRedrawNeeded = true;
            scheduleTraversals();
        }
    }
```



#### (2) lockCanvas

Surface类的成员函数lockCanvas调用另外一个成员函数lockCanvasNative来创建一块画布。Surface类的成员函数lockCanvasNative是一个JNI方法，它是由C++层的函数Surface_lockCanvas来实现的。

调用前面所获得的C++层的Surface对象surface的成员函数lock来获得一个图形缓冲区，这个图形缓冲区使用一个SurfaceInfo对象info来描述，其中，图形缓冲区的地址就保存在它的成员变量bits中。

获得图形缓冲区之后，我们就可以在上面绘制应用程序窗口的UI了。由于Java层的应用程序窗口是通Skia图形库来绘制应用程序窗口的UI的，而Skia图形库在绘制UI时，是需要一块画布的，因此，函数接下来就会将前面所获得的图形缓冲区封装在一块画布中。

建立好对应的联系后，会继续调用其成员变量mView所描述的一个DecorView对象的成员函数draw()，在画布上面绘制应用程序窗口的UI。

#### (3)  draw()#View

这个函数主要完成以下六个操作：

1. 绘制当前视图的背景。

2. 保存当前画布的堆栈状态，并且在在当前画布上创建额外的图层，以便接下来可以用来绘制当前视图在滑动时的边框渐变效果。
   
3. 绘制当前视图的内容。
   
4. 绘制当前视图的子视图的内容。
   
5. 绘制当前视图在滑动时的边框渐变效果。
   
6. 绘制当前视图的滚动条。

#### (4) unlockCanvasAndPost()

图层绘制完成后，接下来就会调用Java层的Surface类的成员函数unlockCanvasAndPost来请求SurfaceFlinger服务渲染这块画布里面所包含的一个图形缓冲区。Surface类的成员函数unlockCanvasAndPost是一个JNI方法，它是由C++层的函数Surface_unlockCanvasAndPost来实现的。



## 4. 总结

(1) 渲染Android应用程序窗口UI需要经过三步曲：测量、布局、绘制。

(2) Android应用程序窗口UI首先是使用Skia图形库API来绘制在一块画布上，实际地是绘制在这块画布里面的一个图形缓冲区中，这个图形缓冲区最终会被交给SurfaceFlinger服务，而SurfaceFlinger服务再使用OpenGL图形库API来将这个图形缓冲区渲染到硬件帧缓冲区中。



# 四、参考资料

https://blog.csdn.net/qijingwang/article/details/105219569


