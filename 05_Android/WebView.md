### WebViewClient与WebChromeClient区别

WebViewClient主要帮助WebView处理各种通知、请求事件的，有以下常用方法：
- onPageFinished 页面请求完成
- onPageStarted 页面开始加载
- shouldOverrideUrlLoading 拦截url
- onReceivedError 访问错误时回调，例如访问网页时报错404，在这个方法回调的时候可以加载错误页面。



WebChromeClient主要辅助WebView处理Javascript的对话框、网站图标、网站title、加载进度等，有以下常用方法。

- onJsAlert webview不支持js的alert弹窗，需要自己监听然后通过dialog弹窗
- onReceivedTitle 获取网页标题
- onReceivedIcon 获取网页icon
- onProgressChanged 加载进度回调


### 常用API

#### 1. WebView常用API

* getSettings()  -  获取设置WebView的WebSettings对象。

* setWebViewClient(WebViewClient client)  -  设置将接收各种通知和请求的WebViewClient。

* setWebChromeClient(WebChromeClient client)  -  设置chrome处理。

* requestFocus(): 触摸焦点起作用（如果不设置，则在点击网页文本输入框时，不能弹出软键盘及不响应其他的一些事件)

* **JavaScript 相关**

  ```java
  // 注入Javascript对象
  public void addJavascriptInterface(Object object, String name);
  
  // 移除已注入的Javascript对象，下次加载或刷新页面时生效
  public void removeJavascriptInterface(String name);
  
  // 对传入的JS表达式求值，通过resultCallback返回结果
  // 此函数添加于API19，必须在UI线程中调用，回调也将在UI线程
  public void evaluateJavascript(String script, ValueCallback<String> resultCallback)
  ```

* 导航相关

  ```java
  // 复制一份BackForwardList
  public WebBackForwardList copyBackForwardList();
  
  // 是否可后退
  public boolean canGoBack();
  
  // 是否可前进
  public boolean canGoForward();
  
  // 是否可前进/后退steps页，大于0表示前进小于0表示后退
  public boolean canGoBackOrForward(int steps);
  
  // 后退一页
  public void goBack();
  
  // 前进一页
  public void goForward();
  
  // 前进/后退steps页，大于0表示前进小于0表示后退
  public void goBackOrForward(int steps);
  
  // 清除当前webview访问的历史记录
  public void clearHistory();
  ```

  



#### 2.WebSettings常用API

* setAllowFileAccess  -  启用或禁用WebView访问文件数据
* setBlockNetworkImage  -  是否显示网络图像
* setBuiltInZoomControls  -  设置是否支持缩放
* setFixedFontFamily  -  设置固定使用的字体
* setJavaScriptEnabled  -  设置是否支持Javascript
* setLayoutAlgorithm  -  设置布局方式
* setLightTouchEnabled  -  设置用鼠标激活被选项
* setSupportZoom  -  设置是否支持变焦
* setCacheMode  -  设置缓冲的模式
* setDefaultFontSize  -  设置默认的字体大小
* setDefaultTextEncodingName  -  设置在解码时时候用的默认编码



#### 3. WebViewClient 常用API

* doUpdateVisitedHistory  -  更新历史记录
* onFormResubmission  -  应用程序重新请求网页数据
* onPageStarted  -  网页开始加载
* onReceivedError  -  报告错误信息
* onScaleChanged  -  WebView发生改变
* shouldOverrideUrlLoading  -  控制新的连接在当前WebView中打开
* onLoadResource  -  加载指定地址提供的资源
* onPageFinished  -  网页加载完毕



#### 4. WebChromeClient常用API

* onCloseWindow  -  关闭WebView
* onCreateWindow  -  创建WebView
* onProgressChanged  -  加载进度条改变
* onReceivedlcon  -  网页图标更改
* onReceivedTitle  -  网页Title更改
* onRequestFocus  -  WebView显示焦点
* onJsAlert  -  处理Javascript中的Alert对话框
* onJsConfirm  -  处理Javascript中的Confirm对话框
* onJsPrompt  -  处理Javascript中的Prompt对话框



#### 5. WebSetting常用API

在android中专门通过WebSettings来设置WebView的一些属性、状态等。在创建WebView时， // 系统有一个默认的设置，我们能够通过WebView.getSettings来得到这个设置： // WebSettings和WebView都在同一个生命周期中存在。当WebView被销毁后。假设再使用WebSettings，则会抛出异常。

```java
WebSettings settings = web.getSettings();

// 存储(storage)
// 启用HTML5 DOM storage API，默认值 false
settings.setDomStorageEnabled(true); 
// 启用Web SQL Database API，这个设置会影响同一进程内的所有WebView，默认值 false
// 此API已不推荐使用，参考：https://www.w3.org/TR/webdatabase/
settings.setDatabaseEnabled(true);  
// 启用Application Caches API，必需设置有效的缓存路径才能生效，默认值 false
// 此API已废弃，参考：https://developer.mozilla.org/zh-CN/docs/Web/HTML/Using_the_application_cache
settings.setAppCacheEnabled(true); 
settings.setAppCachePath(context.getCacheDir().getAbsolutePath());

// 定位(location)
settings.setGeolocationEnabled(true);

// 是否保存表单数据
settings.setSaveFormData(true);
// 是否当webview调用requestFocus时为页面的某个元素设置焦点，默认值 true
settings.setNeedInitialFocus(true);  

// 是否支持viewport属性，默认值 false
// 页面通过`<meta name="viewport" ... />`自适应手机屏幕
settings.setUseWideViewPort(true);
// 是否使用overview mode加载页面，默认值 false
// 当页面宽度大于WebView宽度时，缩小使页面宽度等于WebView宽度
settings.setLoadWithOverviewMode(true);
// 布局算法
settings.setLayoutAlgorithm(WebSettings.LayoutAlgorithm.NORMAL);

// 是否支持Javascript，默认值false
settings.setJavaScriptEnabled(true); 
// 是否支持多窗口，默认值false
settings.setSupportMultipleWindows(false);
// 是否可用Javascript(window.open)打开窗口，默认值 false
settings.setJavaScriptCanOpenWindowsAutomatically(false);

// 资源访问
settings.setAllowContentAccess(true); // 是否可访问Content Provider的资源，默认值 true
settings.setAllowFileAccess(true);    // 是否可访问本地文件，默认值 true
// 是否允许通过file url加载的Javascript读取本地文件，默认值 false
settings.setAllowFileAccessFromFileURLs(false);  
// 是否允许通过file url加载的Javascript读取全部资源(包括文件,http,https)，默认值 false
settings.setAllowUniversalAccessFromFileURLs(false);

// 资源加载
settings.setLoadsImagesAutomatically(true); // 是否自动加载图片
settings.setBlockNetworkImage(false);       // 禁止加载网络图片
settings.setBlockNetworkLoads(false);       // 禁止加载所有网络资源

// 缩放(zoom)
settings.setSupportZoom(true);          // 是否支持缩放
settings.setBuiltInZoomControls(false); // 是否使用内置缩放机制
settings.setDisplayZoomControls(true);  // 是否显示内置缩放控件

// 默认文本编码，默认值 "UTF-8"
settings.setDefaultTextEncodingName("UTF-8");
settings.setDefaultFontSize(16);        // 默认文字尺寸，默认值16，取值范围1-72
settings.setDefaultFixedFontSize(16);   // 默认等宽字体尺寸，默认值16
settings.setMinimumFontSize(8);         // 最小文字尺寸，默认值 8
settings.setMinimumLogicalFontSize(8);  // 最小文字逻辑尺寸，默认值 8
settings.setTextZoom(100);              // 文字缩放百分比，默认值 100

// 字体
settings.setStandardFontFamily("sans-serif");   // 标准字体，默认值 "sans-serif"
settings.setSerifFontFamily("serif");           // 衬线字体，默认值 "serif"
settings.setSansSerifFontFamily("sans-serif");  // 无衬线字体，默认值 "sans-serif"
settings.setFixedFontFamily("monospace");       // 等宽字体，默认值 "monospace"
settings.setCursiveFontFamily("cursive");       // 手写体(草书)，默认值 "cursive"
settings.setFantasyFontFamily("fantasy");       // 幻想体，默认值 "fantasy"


if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.KITKAT) {
    // 用户是否需要通过手势播放媒体(不会自动播放)，默认值 true
    settings.setMediaPlaybackRequiresUserGesture(true);
}
if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.LOLLIPOP) {
    // 5.0以上允许加载http和https混合的页面(5.0以下默认允许，5.0+默认禁止)
    settings.setMixedContentMode(WebSettings.MIXED_CONTENT_ALWAYS_ALLOW);
}
if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.M) {
    // 是否在离开屏幕时光栅化(会增加内存消耗)，默认值 false
    settings.setOffscreenPreRaster(false);
}

if (isNetworkConnected(context)) {
    // 根据cache-control决定是否从网络上取数据
    settings.setCacheMode(WebSettings.LOAD_DEFAULT);
} else {
    // 没网，离线加载，优先加载缓存(即使已经过期)
    settings.setCacheMode(WebSettings.LOAD_CACHE_ELSE_NETWORK);
}
```





### Native调用JS代码

#### 方式1：通过`WebView`的`loadUrl（）`

```javascript
//JS代码
function callJs() {
       alert("Android 调用Js方法");
}
```

```java
//调用
private void callJs() {
        if (mWebView != null) {
            mWebView.post(() -> mWebView.loadUrl("javascript:callJs()"));
        }
}
```



#### 方式2：通过`WebView`的`evaluateJavascript（）`

- 优点：该方法比第一种方法效率更高、使用更简洁。
- 因为该方法的执行不会使页面刷新，而第一种方法（loadUrl ）的执行则会。
- Android 4.4 后才可使用

```java
 mWebView.post(() ->
            mWebView.evaluateJavascript("javascript:callJs()", new ValueCallback<String>() {
                        @Override
                        public void onReceiveValue(String value) {
                            Logger.show(value);
                        }
            }));
```





### JS调用Android代码

#### 方式1：通过 `WebView`的`addJavascriptInterface（）`进行对象映射

缺点：存在严重的漏洞问题，具体请看文章：[你不知道的 Android WebView 使用漏洞](http://www.jianshu.com/p/3a345d27cd42)



#### 方式2：通过 `WebViewClient` 的方法`shouldOverrideUrlLoading ()`回调拦截 url

具体原理：

1. Android通过 `WebViewClient` 的回调方法`shouldOverrideUrlLoading ()`拦截 url；
2. 解析该 url 的协议；
3. 如果检测到是预先约定好的协议，就调用相应方法；

```java
@Override
    public boolean shouldOverrideUrlLoading(WebView view, String url) {
        Logger.show("shouldOverrideUrlLoading:" + url);
        view.loadUrl(url);
        Uri uri = Uri.parse(url);
        if (uri.getScheme().equals("js")) {

            // 如果 authority  = 预先约定协议里的 webview，即代表都符合约定的协议
            if (uri.getAuthority().equals("webview")) {
                Set<String> collection = uri.getQueryParameterNames();
                for (String s : collection) {
                    String parameter = uri.getQueryParameter(s);
                    Logger.show("shouldOverrideUrlLoading: str = " + s+", para:"+parameter);
                }
            }
        }
        return true;
    }
```



#### 方式3：通过 `WebChromeClient` 的`onJsAlert()`、`onJsConfirm()`、`onJsPrompt（）`方法回调拦截JS对话框`alert()`、`confirm()`、`prompt（）` 消息