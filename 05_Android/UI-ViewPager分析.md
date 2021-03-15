OnPageChangeListener 接口，三个回调方法
void onPageScrolled(int position, float positionOffset, @Px int positionOffsetPixels);
void onPageSelected(int position);
void onPageScrollStateChanged(int state);
如果不需要全部实现，可以使用SimpleOnPageChangeListener

PageTransformer



自定义 PageAdapter 需要实现下面四个方法：

* **instantiateItem(ViewGroup container, int position)：**该方法的功能是创建指定位置的页面视图。适配器有责任增加即将创建的 View 视图到这里给定的 container 中，这是为了确保在 finishUpdate(viewGroup) 返回时 this is be done!
* **destroyItem(ViewGroup container, int position, Object object)：**该方法的功能是移除一个给定位置的页面。适配器有责任从容器中删除这个视图，这是为了确保在 finishUpdate(viewGroup) 返回时视图能够被移除。
* **getCount()：**返回当前有效视图的数量；
* **isViewFromObject(View view, Object object)：**该函数用来判断 instantiateItem() 函数所返回来的 Key 与一个页面视图是否是代表的同一个视图(即它俩是否是对应的，对应的表示同一个 View)。返回值：如果对应的是同一个View，返回 true，否则返回 false；





**核心API**
**setCurrentItem() 设置选中页**
**setOffscreenPageLimit 保持活动的页面数**

**startUpdate ：**Called when a change in the shown pages is going to start being made.
**finishUpdate：**Called when the a change in the shown pages has been completed.


When you implement a PagerAdapter, you must override the following methods at minimum:[instantiateItem(ViewGroup, int)](https://developer.android.com/reference/androidx/viewpager/widget/PagerAdapter#instantiateItem(android.view.ViewGroup, int))[destroyItem(ViewGroup, int, Object)](https://developer.android.com/reference/androidx/viewpager/widget/PagerAdapter#destroyItem(android.view.ViewGroup, int, java.lang.Object))[getCount()](https://developer.android.com/reference/androidx/viewpager/widget/PagerAdapter#getCount())[isViewFromObject(View, Object)](https://developer.android.com/reference/androidx/viewpager/widget/PagerAdapter#isViewFromObject(android.view.View, java.lang.Object))