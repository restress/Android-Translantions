### Creating Lists and Cards

要创建复杂的material design的lists和cards，可以使用RecyclerView和CardView组件。

#### 创建列表

---

RecyclerView是ListView的进阶版，更加灵活。这个组件可以容纳大量的数据集合，在有限的视图里面可以有效率地滚动。可以在运行时数据集中的数据有变化的时候使用RecyclerView。

RecyclerView简化了展示和处理大量数据，通过提供一下方法：

- 用于定位项item的视图管理器（Layout Managers）
- 对于常见项操作的动画，比如说增加和删除项

当然，你也可以自定义视图管理器和动画。

![RecyclerView](https://i.loli.net/2017/12/18/5a37ac9ae05f1.png)

使用RecyclerView组件，需要定义一个适配器adapter和视图管理器layout manager。创建适配器adapter需要继承RecyclerView.Adapter类。执行细节需要根据数据集和视图类型决定。

视图管理器Layout Manager定位RecyclerView中项的视图，并且决定什么时候重新使用用户看不见的项视图。如果要重新使用一个项的视图，视图管理器Layout Manager会要求适配器adapter使用不同的数据集来覆盖当前视图显示的内容。通过这种方法复用视图能提高效率，通过防止产生不必要的视图以及操作耗时的findViewById()方法。

RecyclerView提供了以下内嵌的视图管理器Layout Manager:

- LinearLayoutManager 显示横向或者纵向滚动列表
- GridLayoutManager 在表格中展示列表
- StaggerGridLayoutManager 在错开的表格中展示列表

如果需要自定义视图管理器Layout Manager，需要继承RecyclerView.LayoutManager

#### 动画

---

RecyclerView的增加和删除动画是自带的。如果要自定义动画，需要继承RecyclerView.ItemAnimator,并且调用RecyclerView.setItemAnimator()方法。

#### 例子

---

Layout：

```xml
<!-- A RecyclerView with some commonly used attributes -->
<android.support.v7.widget.RecyclerView
    android:id="@+id/my_recycler_view"
    android:scrollbars="vertical"
    android:layout_width="match_parent"
    android:layout_height="match_parent"/>
```

一旦增加了RecyclerView,就需要增加一个对象处理部分，连接到一个视图管理器Layout Manager,并且使用一个用于展示数据的适配器adapter

```java
public class MyActivity extends Activity {
    private RecyclerView mRecyclerView;
    private RecyclerView.Adapter mAdapter;
    private RecyclerView.LayoutManager mLayoutManager;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.my_activity);
        mRecyclerView = (RecyclerView) findViewById(R.id.my_recycler_view);

        // use this setting to improve performance if you know that changes
        // in content do not change the layout size of the RecyclerView
        mRecyclerView.setHasFixedSize(true);

        // use a linear layout manager
        mLayoutManager = new LinearLayoutManager(this);
        mRecyclerView.setLayoutManager(mLayoutManager);

        // specify an adapter (see also next example)
        mAdapter = new MyAdapter(myDataset);
        mRecyclerView.setAdapter(mAdapter);
    }
    ...
}
```

适配器提供数据集的接口，创建数据项的视图，覆盖不再显示的数据项内容。以下代码展示了一个简单的例子，使用TextView组件展示数组字符串。

```java
public class MyAdapter extends RecyclerView.Adapter<MyAdapter.ViewHolder> {
    private String[] mDataset;

    // Provide a reference to the views for each data item
    // Complex data items may need more than one view per item, and
    // you provide access to all the views for a data item in a view holder
    public static class ViewHolder extends RecyclerView.ViewHolder {
        // each data item is just a string in this case
        public TextView mTextView;
        public ViewHolder(TextView v) {
            super(v);
            mTextView = v;
        }
    }

    // Provide a suitable constructor (depends on the kind of dataset)
    public MyAdapter(String[] myDataset) {
        mDataset = myDataset;
    }

    // Create new views (invoked by the layout manager)
    @Override
    public MyAdapter.ViewHolder onCreateViewHolder(ViewGroup parent,
                                                   int viewType) {
        // create a new view
        TextView v = (TextView) LayoutInflater.from(parent.getContext())
                .inflate(R.layout.my_text_view, parent, false);
        // set the view's size, margins, paddings and layout parameters
        ...
        ViewHolder vh = new ViewHolder(v);
        return vh;
    }

    // Replace the contents of a view (invoked by the layout manager)
    @Override
    public void onBindViewHolder(ViewHolder holder, int position) {
        // - get element from your dataset at this position
        // - replace the contents of the view with that element
        holder.mTextView.setText(mDataset[position]);

    }

    // Return the size of your dataset (invoked by the layout manager)
    @Override
    public int getItemCount() {
        return mDataset.length;
    }
}
```

#### 创建Cards

---

CardView是继承了FrameLayout，并且可以将信息展示在卡片上的。CardView可以有阴影和圆角。

创建一个有阴影的card，需要使用card_view:cardelevation属性。在android5.0（API 21）以上，CardView使用真实的高度和动态阴影，在更早的版本中则会回归传统的阴影。更多信息查看[Maintaining Compatibility](https://developer.android.com/training/material/compatibility.html)

在CardView组件中使用以下属性自定义外观：

- 在layout设置圆角，使用card_view:cardCornerRadius
- 在代码中设置圆角，使用CardView.setRadius
- 设置card的背景，使用card_view:cardBackgroundColor

以下的代码例子展示了如何在layout中使用CardView

```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    xmlns:card_view="http://schemas.android.com/apk/res-auto"
    ... >
    <!-- A CardView that contains a TextView -->
    <android.support.v7.widget.CardView
        xmlns:card_view="http://schemas.android.com/apk/res-auto"
        android:id="@+id/card_view"
        android:layout_gravity="center"
        android:layout_width="200dp"
        android:layout_height="200dp"
        card_view:cardCornerRadius="4dp">

        <TextView
            android:id="@+id/info_text"
            android:layout_width="match_parent"
            android:layout_height="match_parent" />
    </android.support.v7.widget.CardView>
</LinearLayout>
```

更多信息，查看API [CardView](https://developer.android.com/reference/android/support/v7/widget/CardView.html)



#### 增加依赖

---

RecyclerView和CardView组件是v7 Support Libraries，需要在app module中增加Gradle依赖。

```java
dependencies {
    ...
    compile 'com.android.support:cardview-v7:21.0.+'
    compile 'com.android.support:recyclerview-v7:21.0.+'
}
```



#### 例子代码

---

下载CardView例子，在[Android CardView Sample](https://github.com/googlesamples/android-CardView/)

