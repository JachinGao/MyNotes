# Activity、Window与WindowManager



## 一、Activity与window的关系

1. 在Activity中调用attach()方法，创建了一个Window，最后其实创建的是子类PhoneWindow；
2. 在Activity中调用setContentView(R.layout.xxx)时，其实调用的时getWindow().setContentView()方法，将开发者设置的布局加载进来；
3. 创建ParentView，实际创建的是DecorView，通过布局填充器将上述R.layout.xxx所描述的布局加载进来；



## 二、Window、WindowManager和WMS的关系

1. Window是一个抽象类，具体的实现类为PhoneWindow，它对View进行管理。

2. WindowManager是一个接口类，继承自接口ViewManager，是用来管理Window的，它的实现类为WindowManagerImpl，但是具体的功能都会委托给WindowManagerGlobal来实现。

3. 如果我们想要对Window进行添加和删除就可以使用WindowManager，具体的工作最终都是由WMS来处理的，WindowManager和WMS的交互是Binder跨进程通信。

```java
   package android.view;
   public interface ViewManager
   {
       public void addView(View view, ViewGroup.LayoutParams params);
       public void updateViewLayout(View view, ViewGroup.LayoutParams params);
       public void removeView(View view);
   }
```

  

### Window的类型

Window的类型有很多种，比如**应用程序窗口**、**系统错误窗口**、**输入法窗口**、**PopupWindow**、**Toast**、**Dialog**等等。

总来来说分为三大类分别是：Application Window（**应用程序窗口**）、Sub Windwow（**子窗口**）、System Window（**系统窗口**），每个大类又包含了很多种类型，它们都定义在WindowManager的静态内部类LayoutParams中。

### Window的标志

Window的标志也就是Flag，用于控制Window的显示，被定义在WindowManager的内部类LayoutParams中；用于描述窗口可见，锁屏之上显示，触摸事件焦点等等。





## 三、PhoneWindow的添加的过程

使用WindowManager关联一个应用程序窗口视图对象（View对象）和一个ViewRoot对象；



### 1. PhoneWindow初始化与DecorView创建

**(1)** 在attach()#**Activity**中，除了创建context上下文，还创建了PhoneWindow对象；

```java
 attachBaseContext(context);
 mWindow = new PhoneWindow(this, window);
 mWindow.setWindowControllerCallback(this);
 mWindow.setCallback(this);
```

在PhoneWindow构造函数中，将context上下文对象传入，以后Window类就可以通过它来访问与Activity组件相关的资源；

```
public PhoneWindow(Context context) {
        super(context);
        mLayoutInflater = LayoutInflater.from(context);
    }
```

PhoneWindow成员函数setCallback来设置窗口回调接口，这样当PhoneWindow对象接收到系统给它分发的IO输入事件（键盘和触摸屏事件），会转发给与它所关联的Activity组件处理；



**(2)** 在Android系统中，每一个应用程序窗口都需要由一个窗口管理者来管理，在接下来，mWindow会设置一个窗口管理者；

```
mWindow.setWindowManager(
                (WindowManager)context.getSystemService(Context.WINDOW_SERVICE),
                mToken, mComponent.flattenToString(),
                (info.flags & ActivityInfo.FLAG_HARDWARE_ACCELERATED) != 0);
```

**(3)** 创建View对象

函数调用到onCreate()，然后执行setContentView(int layoutResID)；

setContentView()#**PhoneWindow**

```java
 ViewGroup mContentParent;
 
 @Override
    public void setContentView(int layoutResID) {
        if (mContentParent == null) {
            installDecor();
        } else {
            mContentParent.removeAllViews();
        }
        mLayoutInflater.inflate(layoutResID, mContentParent);
        final Callback cb = getCallback();
        if (cb != null) {
            cb.onContentChanged();
        }
    }
```

```java
private DecorView mDecor;

private void installDecor() {
        mForceDecorInstall = false;
        if (mDecor == null) {
            mDecor = generateDecor(-1);  //创建DecorView对象
        } else {
            mDecor.setWindow(this);
        }
        if (mContentParent == null) {
            mContentParent = generateLayout(mDecor);
        }
}
```

```java
public static final int ID_ANDROID_CONTENT = com.android.internal.R.id.content;
        
    protected ViewGroup generateLayout(DecorView decor) {
     	mDecor.startChanging();
        mDecor.onResourcesLoaded(mLayoutInflater, layoutResource);
        ViewGroup contentParent = (ViewGroup)findViewById(ID_ANDROID_CONTENT);
        return contentParent;
      }
```



### 2. 系统窗口的添加

(1) 接下来就要进入handleResumeActivity()#ActivityThread，使用WindowManager关联一个应用程序窗口视图对象（View对象）和一个ViewRoot对象；

handleResumeActivity#**ActivityThread**

```java
final void handleResumeActivity(IBinder token, boolean clearHide, boolean 							isForward, boolean reallyResume, int seq, String reason) {
          
    ActivityClientRecord r = mActivities.get(token);
       
    		if (r.window == null && !a.mFinished && willBeVisible) {
                   r.window = r.activity.getWindow();
                   View decor = r.window.getDecorView();
                   decor.setVisibility(View.INVISIBLE);
                   ViewManager wm = a.getWindowManager();
                   WindowManager.LayoutParams l = r.window.getAttributes();
				   //描述应用程序窗口视图的布局属性
               
                   if (a.mVisibleFromClient && !a.mWindowAdded) {
                       a.mWindowAdded = true;
                       wm.addView(decor, l);
                   }
            }
   }
```

 

(2) wm实际为[WindowManagerImpl对象](http://liuwangshu.cn/framework/wm/1-windowmanager.html)，使用addView()方法添加；

```java
mWindowManager.addView(mStatusBarView, mLp); //两个参数：view和params
```

内部调用**WindowManagerGlobal**的方法；

```java
mGlobal.addView(view, params, mContext.getDisplay(), mParentWindow);
```



(3) addView#**WindowManagerGlobal**，创建**ViewRootImpl**；

```java
public void addView(View view, ViewGroup.LayoutParams params,
          Display display, Window parentWindow) {
 
      final WindowManager.LayoutParams wparams = (WindowManager.LayoutParams) params;
      
      ViewRootImpl root;
      View panelParentView = null;
    
          root = new ViewRootImpl(view.getContext(), display);
          view.setLayoutParams(wparams);
          mViews.add(view);
          mRoots.add(root);
          mParams.add(wparams);
      }
      try {
          root.setView(view, wparams, panelParentView);
      } catch (RuntimeException e) {
         ...
      }
  }
```



(4) 调用setView#**ViewRootImpl**

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

ViewRoot类的成员函数**setView()**需要执行两个操作:

1. 调用ViewRoot类的另外一个成员函数requestLayout来请求对应用程序窗口视图的UI作第一次布局。

2. 调用ViewRoot类的成员变量mWindowSession所描述的一个类型为Session的Binder代理对象的成员函数addToDisplay()来请求WindowManagerService增加一个WindowState对象，以便可以用来描述当前正在处理的一个ViewRoot所关联的一个应用程序窗口。



(5) 在addToDisplay()方法中，实际上又调用了WMS中的方法；

addToDisplay#**Session**

```
 mService.addWindow(this, window, seq, attrs, viewVisibility, displayId,
             		outContentInsets, outStableInsets, outOutsets, outInputChannel);
```



ViewRootImpl身负了很多职责：

- 负责为应用程序窗口视图创建、管理Surface；
- 负责与WMS进行进程间通信、配合WMS来管理系统的应用程序窗口；
- View树的根并管理View树；
- 触发View的测量、布局和绘制；
- 输入事件的中转站；

