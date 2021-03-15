Recyclerview几个核心模块；

* **LayoutManager**：控制 item 的布局

* **RecyclerView.Adapter**：为 RecyclerView 提供数据

* **ItemDecoration**：为 RecyclerView 添加分割线

* **ItemAnimator**：控制 item 的动画

* **Recycler**：负责回收和提供 View，和 RecyclerView 的复用机制相关



布局preLayout





**LayoutManager**

> 控制item布局；

removeAndRecycleAllViews



detachAndScrapAttachedViews











**ItemDecoration**

> 用于给列表的item添加各种装饰效果，,给`RecyclerView`的子`item`设置四边边距，以及上下左右绘制分割线。开发中最常见的就是为item添加分割线。

核心方法有三个：

```java
public static abstract class ItemDecoration {
    public void onDraw(Canvas c, RecyclerView parent, State state) {
        onDraw(c, parent);
    }
    public void onDrawOver(Canvas c, RecyclerView parent, State state) {
        onDrawOver(c, parent);
    }
    public void getItemOffsets(Rect outRect, View view, RecyclerView parent, State state) {
        getItemOffsets(outRect, ((LayoutParams) 		
        view.getLayoutParams()).getViewLayoutPosition(),parent);
    }
}
```

这三个方法的用途：

- `onDraw`：在item绘制之前时被调用，将指定的内容绘制到item view内容之下；
- `onDrawOver`：在item被绘制之后调用，将指定的内容绘制到item view内容之上
- `getItemOffsets`：在每次测量item尺寸时被调用，将decoration的尺寸计算到item的尺寸中