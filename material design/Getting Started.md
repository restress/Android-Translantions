###Getting Started

要创建materail design的app：

1. 复习[material design specification](https://material.io/guidelines/)
2. 在app上使用material theme
3. 按照materail design规范来创建layouts
4. 指定view的高度来投射阴影
5. 使用系统组件创建lists和cards
6. 自定义动画

#### 保持之前的兼容性

------

你可以通过一些方法保持在android5.0之前的兼容性，参考[Maintaing Compatibility](https://developer.android.com/training/material/compatibility.html)

####使用material design更新app

------

要使用material design更新目前已经存在的app，首先要根据material design指导来更新视图。同时

要确认合并视图，触碰反馈和动画。

#### 根据material design创建新app

------

如果想要创建一个具有material design特征的app，[material design guideliness](https://material.io/guidelines/) 提供给你一个兼容框架。根据这些规则在android框架下来使用新的功能开发您的app。

#### 使用Material Theme

------

在app中使用你的material design，需要指定一个继承了android:Theme.Material的style

```xml
<!-- res/values/styles.xml -->
<resources>
  <!-- your theme inherits from the material theme -->
  <style name="AppTheme" parent="android:Theme.Material">
    <!-- theme customizations -->
  </style>
</resources>
```

material theme 提供了一个可以自定义调色盘，默认动画，转场动画的进阶系统。想要更多信息，请查看[Using Material Theme](https://developer.android.com/training/material/theme.html)

#### 设计视图

------

除了使用和自定义material theme之外，你的视图还要参考[material design guideliness](https://material.io/guidelines/) 。

当你设计视图时，尤其要注意：

- Baseline grids基线格点
- Keylines
- Spacing
- Touch target size
- Layout structure

#### 定义视图高度

------

视图可以投射阴影，高度决定了它的投射阴影和绘画顺序。使用android:elevation可以设置视图高度

```xml
<TextView
    android:id="@+id/my_textview"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="@string/next"
    android:background="@color/white"
    android:elevation="5dp" />
```

新的translationZ属性创建反射高度临时变化的动画。高度变化在反应触碰手势[responding to touch gestures](https://developer.android.com/training/material/animations.html#ViewState)的时候很有用。

更多信息，请查看[Defining Shadows and Clipping Views](https://developer.android.com/training/material/shadows-clipping.html)

#### 创建Lists和Cards

------

RecyclerView可以自定义视图类型，提供进阶动作，是一个更加可插拔式的ListView。CardView可以让你使用一致性视图显示片状信息。以下是使用CardView的代码示例

```xml
<android.support.v7.widget.CardView
    android:id="@+id/card_view"
    android:layout_width="200dp"
    android:layout_height="200dp"
    card_view:cardCornerRadius="3dp">
    ...
</android.support.v7.widget.CardView>
```

更多信息，请查看[Creating Lists and Cards](https://developer.android.com/training/material/lists-cards.html)

#### 自定义动画

------

Android 5.0（API 21）包括了一些新的自定义动画的API。比如，你可以在activity中定义转场动画。

```java
public class MyActivity extends Activity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        // enable transitions
        getWindow().requestFeature(Window.FEATURE_CONTENT_TRANSITIONS);
        setContentView(R.layout.activity_my);
    }

    public void onSomeButtonClicked(View view) {
        getWindow().setExitTransition(new Explode());
        Intent intent = new Intent(this, MyOtherActivity.class);
        startActivity(intent,
                      ActivityOptions
                          .makeSceneTransitionAnimation(this).toBundle());
    }
}
```

当你启动另一个activity的时候，退出动画就会被激活。

了解更多信息，查看[Defining Custom Animation](https://developer.android.com/training/material/animations.html)

