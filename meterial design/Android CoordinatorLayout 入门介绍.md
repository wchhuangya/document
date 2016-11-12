---
title: Android CoordinatorLayout 入门介绍
tags: android,component,CoordinatorLayout
grammar_cjkRuby: true
---

[toc]

在 `2015` 年的 `I/O` 开发者大会上，`Google` 介绍了一个新的 `Android Design Support Library`，该库可以帮助开发者在应用上使用 `meterial design`。它包含了许多重要的 `meterial design` 的构建块，并且它支持 `API 7` 及以上的版本。如果你错过了这次大会，那就请打开谷歌开发者站点来查阅它的相关信息吧：[传送门](http://android-developers.blogspot.com/2015/05/android-design-support-library.html)。

## CoordinatorLayout
---
在库中所有好的东东之间，看起来真正有趣的是 `CoordinatorLayout`，它是一个 `FrameLayout`。从它的名字中，你或许已经猜到：该布局的强大在于，能够协调子元素之间的依赖关系。

你需要做的就是把 `View` 包含在 `CoordinatorLayout` 中。让我们直接进入代码吧。下面的例子非常简单，它包含了 `Floating Action Button`，点击时会触发一个 `Snackbar`。

首先，在 `gradle` 文件中引入 `meterial design` 库：

`compile 'com.android.support:design:22.2.0'`

接下来，为 `Activity` 创建一个简单的布局文件：

``` xml?linenums=true
<?xml version="1.0" encoding="utf-8"?>
<android.support.design.widget.CoordinatorLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <android.support.design.widget.FloatingActionButton
        android:id="@+id/fab"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="end|bottom"
        android:layout_margin="16dp"
        android:src="@drawable/ic_done" />

</android.support.design.widget.CoordinatorLayout>
```

还需要添加 `MainActivity`：

``` java?linenums=true
public class MainActivity extends AppCompatActivity {

  @Override  
  protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);
    findViewById(R.id.fab).setOnClickListener(new View.OnClickListener() {
      @Override
      public void onClick(View view) {
        Snackbar.make(view, "Hello Snackbar", Snackbar.LENGTH_LONG).show();
      }
    });
  }
}
```

![效果演示][1]

  
非常酷，是吗？
  
但是，如果你想使用其它 `FAB` 的实现又会怎么样呢？因为 `Support Library` 中的 `FAB` 实现没有菜单选项，于是，我们尝试一下由开发者 `Base` 实现的 `FAB` 开源库。

`compile 'com.getbase:floatingactionbutton:1.9.1'`

``` xml?linenums=true
<?xml version="1.0" encoding="utf-8"?>
<android.support.design.widget.CoordinatorLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <com.getbase.floatingactionbutton.FloatingActionButton
        android:id="@+id/fab"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="end|bottom"
        android:layout_margin="16dp"
        app:fab_icon="@drawable/ic_done" />

</android.support.design.widget.CoordinatorLayout>
```

![开源库效果显示][2]

如图所示，在这种情况下，`CoordinatorLayout` 不能正常工作。这是因为 `View` 没有默认的 `CoordinatorLayout.Behavior` 的实现。现有的解决方案就是等待有人来完善它。

或者呢，我们可以为这个组件写一个自定义的行为实现。:D

## View 知道如何表现
---
这个框架真的非常强大，要给 `view` 自定义行为时，你根本不需要获取这个 `view`。你还可以改变任意 `view` 的默认行为。

首先，需要继承 `Behavior` 类：

`public class FloatingActionButtonBehavior extends CoordinatorLayout.Behavior<FloatingActionButton>`

为了使这个类可以填充 `xml` 中的内容，我们需要给它设置一个构造方法，方法有两个参数：`Context` 和 `AttributeSet`。

`public FloatingActionButtonBehavior(Context context, AttributeSet attrs) {}`

接下来的步骤是重写 `layoutDependsOn()` 方法，并且，如果我们想监听改变，就让方法返回 `true`。在例子中，我们只想监听 `Snackbar` 对象的改变。

``` java?linenums=true
@Override
public boolean layoutDependsOn(CoordinatorLayout parent, FloatingActionButton child, View dependency) {
  return dependency instanceof SnackbarLayout;
}
```

现在，让我们继承真正行为的实现。当 `CoordinatorLayout` 中的 `view` 每次发生变化时，`onDependentViewChanged` 方法都会被调用。在这个方法中，我们要读取当前 `Snackbar` 的状态。当 `Snackbar` 显示的时候，我们想把 `FAB` 也移上来。要实现这样的目的，我们需要把 `FAB` 的 `Y` 坐标设置为 `Snackbar` 的高度。要得到正确的转换值，我们需要从转化的 `Y` 值中减去 `Snackbar` 的高度。

``` java?linenums=true
@Override
public boolean onDependentViewChanged(CoordinatorLayout parent, FloatingActionButton child, View dependency) {
  float translationY = Math.min(0, dependency.getTranslationY() - dependency.getHeight());
  child.setTranslationY(translationY);
  return true;
}
```

最后一步就是告诉 `CoordinatorLayout` 使用 `FloatingActionButtonBeahvior`：

``` xml?linenums=true
<?xml version="1.0" encoding="utf-8"?>
<android.support.design.widget.CoordinatorLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <com.getbase.floatingactionbutton.FloatingActionButton
        android:id="@+id/fab"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="end|bottom"
        android:layout_margin="16dp"
        app:layout_behavior="com.getbase.coordinatorlayoutdemo.FloatingActionButtonBehavior"
        app:fab_icon="@drawable/ic_done" />

</android.support.design.widget.CoordinatorLayout>
```

最终，问题被修正了：

![问题修正后的效果显示][3]

如果你想为 `view` 定义默认行为，只需要在你的 `Behavior` 类上添加 `DefaultBehavior` 注解就可以了。

以上代码的 `GitHub` 地址为：**https://github.com/ggajews/coordinatorlayoutwithfabdemo** 。

祝大家编码愉快！


  [1]: ./images/snackbar1.gif "效果显示"
  [2]: ./images/snackbar2.gif "开源库效果显示"
  [3]: ./images/snackbar3.gif "问题修正后的效果显示"