---
title: Android Design Support Library 
tags: android,Support Library
grammar_cjkRuby: true
---

[toc]

## 2015-05-29

迄今为止（`2015-05-29`），`Android 5.0 Lollipop` 是 `Android` 最重要的版本发布，引入了 `meterial design`，还引入了新的设计语言，刷新了 `Android` 整个的体验。[**detailed spec**](https://material.google.com/material-design/introduction.html?utm_campaign=io15&utm_source=dac&utm_medium=blog) 是开始使用 `meterial design` 的绝佳场所，但是我们知道，这对开发者——尤其是考虑向后兼容性的开发者——来说，是一个大的挑战。为了帮助开发者，我们创建了 `Android Design Support Library`，给开发者带来了许多重要的 `meterial design` 类型的组件，它们可以给所有的开发者提供便利，并能完美支持 `Android 2.1` 及以上版本的设备。在它的里面，包含了：`navigation drawer view`， `floating labels for editing text,`， `floating action button`， `snackbar`， `tabs`， `motion and scroll framework`。

## Navigation View
---
对于应用内的认证和导航来说，[**navigation drawer**](https://material.google.com/patterns/navigation-drawer.html?utm_campaign=io15&utm_source=dac&utm_medium=blog) 可以是一个重要的焦点，它可以在设计上保持一致性，尤其对初次使用者来说，非常容易就可以在应用内导航。[**NavigationView**](https://developer.android.com/reference/android/support/design/widget/NavigationView.html?utm_campaign=io15&utm_source=dac&utm_medium=blog) 通过提供抽屉式导航所需要的框架使得这一切都变得灰常容易，它还使得导航的内容可以来源于 `menu resource`。

![抽屉式导航][1]

  
使用方法：在 `DrawerLayout` 布局中，把 `NavigationView` 当作抽屉的内容视图。

``` xml?linenums=true
<android.support.v4.widget.DrawerLayout
        xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:app="http://schemas.android.com/apk/res-auto"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:fitsSystemWindows="true">

    <!-- your content layout -->

    <android.support.design.widget.NavigationView
            android:layout_width="wrap_content"
            android:layout_height="match_parent"
            android:layout_gravity="start"
            app:headerLayout="@layout/drawer_header"
            app:menu="@menu/drawer"/>
</android.support.v4.widget.DrawerLayout>
```

你需要注意 `NavigationView` 的两个属性：1. `app:headerLayout`：控制用于头部视图（可选）的布局；2. `app:menu`：导航项的菜单资源（可以在运行时更新）。`NavigationView` 会为你照顾状态栏，来确保在 `API 21+` 的设备上，`NavigationView` 和状态栏正常交互。

下面是菜单的导航菜单项的申明示例：

``` xml?linenums=true
<group android:checkableBehavior="single">
    <item
        android:id="@+id/navigation_item_1"
        android:checked="true"
        android:icon="@drawable/ic_android"
        android:title="@string/navigation_item_1"/>
    <item
        android:id="@+id/navigation_item_2"
        android:icon="@drawable/ic_android"
        android:title="@string/navigation_item_2"/>
</group>
```

被设置 `checked` 属性的项会在导航中高亮显示，以便于用户知道当前选择了哪个导航项。

在导航菜单中，你还可以为每个组设置一个二级头部：

``` xml?linenums=true
<item
    android:id="@+id/navigation_subheader"
    android:title="@string/navigation_subheader">
    <menu>
        <item
            android:id="@+id/navigation_sub_item_1"
            android:icon="@drawable/ic_android"
            android:title="@string/navigation_sub_item_1"/>
        <item
            android:id="@+id/navigation_sub_item_2"
            android:icon="@drawable/ic_android"
            android:title="@string/navigation_sub_item_2"/>
    </menu>
</item>
```

通过使用 `setNavigationItemSelectedListener()`，你可以为每个选择的项设置一个监听事件 `OnNavigationItemSelectedListener`。回调方法中，有 `MenuItem` 类型的参数，用于确定用户点击了哪个导航项，可以让你处理选择事件，改变选中的高亮状态，加载新的内容，使用编程关闭抽屉，或其它的一些操作。

## EditText 的浮动文本
---
在 `meterial design` 方面，简陋的 `EditText` 甚至都有提升的空间。众所周知，当在 `EditText` 中输入文字后，它的 `hint` 就消失了。但是现在，你把它放入 `TextInputLayout` 中，这种情况就会发生变化，它会使得 `EditText` 在被输入文本后， `hint` 变成一个浮动在 `EditText` 上方的文本，这种方式可以确保无论在什么时候，用户都可以完整的看到自己的输入。

![浮动文本][2]

除了显示 `hint`，还可以通过调用 `setError()` 方法来把错误信息显示到 `EditText` 的下方。

## 浮动的动作按钮
---
浮动的动作按钮是一个圆形按钮，用于表示界面上的一个主要操作。`FloatingActionButton` 将会给你一套统一的实现，使用 `colorAccent` 可以默认从样式中抽取颜色。

![浮动的动作按钮][3]

浮动的动作按钮除了正常尺寸外，它也支持最小尺寸（`fabSize="mini"`）。因为 `FloatingActionButton` 继承自 `ImageView`，因此，可以使用 `android:src` 或 `setImageDrawable` 来控制 `FloatingActionButton` 上显示的图标。

## Snackbar
---
如果你想在某个操作上提供轻量级的、快速的反馈，那么，使用 `snackbar` 是个不错的选择。`snackbar` 在屏幕底部显示，包含一个单一动作（可选）的文本。在指定的时间后，就会以动画的方式从屏幕上消失。此外，在时间到达之前，用户还可以使用挥动的方式让其消失。

![snackbar][4]

``` java?linenums=true
Snackbar
  .make(parentLayout, R.string.snackbar_text, Snackbar.LENGTH_LONG)
  .setAction(R.string.snackbar_action, myOnClickListener)
  .show(); // Don’t forget to show!
```

## Tab
---
应用中，通过 `tab` 在不同的内容之间进行切换，这不是一个新的概念。

![tab][5]

`design library` 中的 `TabLayout` 实现了固定的 `tab`：即每个 `tab` 内容的宽度都一样，和滚动的 `tab`：每个 `tab` 内容的宽度不固定，可以水平滚动。`tab` 还可以动态添加：

``` java?linenums=true
TabLayout tabLayout = ...;
tabLayout.addTab(tabLayout.newTab().setText("Tab 1"));
```

但是，如果你在 `tab` 中使用了水平分页的 `ViewPager`，那你可以直接从 `PagerAdapter` 的 `getPageTitle()` 中创建 `tab`，然后使用 `setupWithViewPager()` 方法将两个结合到一起。这样就确保了 `tab` 的选择事件会更新 `ViewPager`，并且，页面改变也会更新选择的 `tab`。

## CoordinatorLayout，运动，滚动
---
`meterial design` 中，不仅仅只有与众不同的视觉效果：运动也是遵循良好 `meterial design` 应用的重要组成部分。`meterial design` 中，有众多的运动单元，包括：`touch ripples`，`meaningful transitions`，`CoordinatorLayout`。`CoordinatorLayout` 是一个在子控件之间提供了额外控件级别触摸事件的布局，`meterial design` 中的很多组件都可以利用到该布局的特性。

> 未完待续：http://android-developers.blogspot.jp/2015/05/android-design-support-library.html



  [1]: ./images/QQ20161111-0.png "抽屉式导航"
  [2]: ./images/QQ20161111-1.png "浮动文本"
  [3]: ./images/QQ20161111-2.png "浮动的动作按钮"
  [4]: ./images/QQ20161111-3.png "snackbar"
  [5]: ./images/QQ20161111-4.png "tab"