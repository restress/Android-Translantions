### Using the Material Theme

新的material theme提供：

- 可以自定义调色盘的系统组件
- 系统组件的触碰反馈
- activity的转场动画

你可以根据你的图标来自定义整体风格调色盘。你也可以给actionbar和status bar着色。

系统组件有全新的设计和触摸动画，你可以在app中自定义调色盘，触摸动画和转场动画。

material theme被定义为：

- `@android:style/Theme.Material` (dark version)
- `@android:style/Theme.Material.Light` (light version)
- `@android:style/Theme.Material.Light.DarkActionBar`

要查看material风格的theme，可以参考API [R.style](https://developer.android.com/reference/android/R.style.html)

<img src="https://i.loli.net/2017/12/18/5a378448c917d.png" style="zoom:50%"/>



<img src="https://i.loli.net/2017/12/18/5a378448cb295.png" style="zoom:50%"/>



注意：material theme只在android5.0以上可以运行，v7 support Libraries也提供material design相关组件，更多信息，查看[Maintaining Compatibility](https://developer.android.com/training/material/compatibility.html)



### 定制调色盘 

---

为了定制化风格的基础颜色，需要在继承了material theme之后定义属性。

```xml
<resources>
  <!-- inherit from the material theme -->
  <style name="AppTheme" parent="android:Theme.Material">
    <!-- Main theme colors -->
    <!--   your app branding color for the app bar -->
    <item name="android:colorPrimary">@color/primary</item>
    <!--   darker variant for the status bar and contextual app bars -->
    <item name="android:colorPrimaryDark">@color/primary_dark</item>
    <!--   theme UI controls like checkboxes and text fields -->
    <item name="android:colorAccent">@color/accent</item>
  </style>
</resources>
```



#### 定制状态栏

---

<img src="https://i.loli.net/2017/12/18/5a379f5815418.png" style="zoom:50%"/>



material theme 可以轻松自定义状态栏，所以你可以自定义适合自己的颜色，material design还提供了足够的对比来展示白色的状态栏图标。使用android:statusBarColor属性设置状态栏的自定义颜色。默认的android:statusBarColor是继承android:colorPrimaryDark的。

你也可以在状态栏后面自己显示。比如说，你想要在照片上面显示透明的状态栏，会有微妙的黑色渐变来显示状态栏上的白色图标。如果想要那样的话，把android:statusBarColor设置成@android:color/transparent并且调整window flags为required。当然你也可以使用Window.setStatusBarColor()方法来实现动画或退出。

注意：状态栏（status bar）和工具栏（toolbar）要有一个清晰的界限，除非是你想要在这些栏下面显示边缘清晰的图片或者媒体，或者你使用了梯度来表明这些图标仍然可用。

如果你个性化定制了导航栏和状态栏，那么要让他们都透明，或者只修改状态栏。导航栏要在其他所有的情况下都保持黑色。

#### 独立主题的view

在XML中的元素可以单独定义android:theme属性，需要指定一个theme的资源。这个属性可以修改当前元素及子元素的风格，这对于在接口内修改主题颜色调色盘很有用。



