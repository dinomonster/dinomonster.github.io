---
title: Android如何使用Vector(矢量图)以及兼容旧版本
date: 2016-09-29 12:06:07
tags: [Android,Vector]
---

这段时间忙成狗，天天加班，昨天凌晨一点多下班，都没时间写东西了，今天中午抽个空记录下Vector兼容旧版本的问题
Android 5.0发布的时候，Google提供了Vector的支持。Vector Drawable相对于普通的Drawable来说，有以下几个好处：

### 1.Vector图像可以自动进行适配，不需要通过分辨率来设置不同的图片，
### 2.Vector图像可以大幅减少图像的体积，同样一张图，用Vector来实现，可能只有PNG的几十分之一 使用简单，
### 3.很多设计工具，都可以直接导出SVG图像，从而转换成Vector图像 功能强大，不用写很多代码就可以实现非常复杂的动画 成熟、稳定，前端已经非常广泛的进行使用了

Vector图像刚发布的时候，是只支持Android 5.0+的，对于Android pre-L的系统来说，并不能使用，所以，可以说那时候的Vector并没有什么卵用。
不过自从AppCompat 23.2之后，Google对p-View的Android系统也进行了兼容，也就是说，Vector可以使用于Android 2.1以上的所有系统，
只需要引用com.android.support:appcompat-v7:23.2.0以上的版本就可以了。

### 兼容版本需要做以下三步：
#### 1.引用com.android.support:appcompat-v7:23.2.0以上的包
#### 2.在build中defaultConfig{}里面写`vectorDrawables.useSupportLibrary = true`
#### 3.使用`app:srcCompat`替换`android:src`

不过 `app:srcCompat` 支持ImageView\ImageButton
### Button并不能直接使用app:srcCompat来使用Vector图像，需要通过Selector来进行使用，首先，创建两个图像，用于Selector的两个状态
select1.xml
```xml
<code class="hljs xml"><vector android:height="24dp" android:viewportheight="24.0" android:viewportwidth="24.0" android:width="24dp" xmlns:android="http://schemas.android.com/apk/res/android">
    <path android:fillcolor="#FF000000" android:pathdata="M14.59,8L12,10.59 9.41,8 8,9.41 10.59,12 8,14.59 9.41,16 12,13.41 14.59,16 16,14.59 13.41,12 16,9.41 14.59,8zM12,2C6.47,2 2,6.47 2,12s4.47,10 10,10 10,-4.47 10,-10S17.53,2 12,2zM12,20c-4.41,0 -8,-3.59 -8,-8s3.59,-8 8,-8 8,3.59 8,8 -3.59,8 -8,8z">
</path></vector>
</code>
```
select2.xml
```xml
<code class="hljs xml"><vector android:height="24dp" android:viewportheight="24.0" android:viewportwidth="24.0" android:width="24dp" xmlns:android="http://schemas.android.com/apk/res/android">
    <path android:fillcolor="#FF000000" android:pathdata="M11,15h2v2h-2zM11,7h2v6h-2zM11.99,2C6.47,2 2,6.48 2,12s4.47,10 9.99,10C17.52,22 22,17.52 22,12S17.52,2 11.99,2zM12,20c-4.42,0 -8,-3.58 -8,-8s3.58,-8 8,-8 8,3.58 8,8 -3.58,8 -8,8z">
</path></vector>
</code>
```
selector.xml
```
<code class="hljs xml"><!--?xml version="1.0" encoding="utf-8"?-->
<selector xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:drawable="@drawable/selector1" android:state_pressed="true">
    <item android:drawable="@drawable/selector2">
</item></item></selector></code>
```

button
```
<button android:background="@drawable/selector" android:id="@+id/btn" android:layout_height="70dp" android:layout_width="70dp"></button>
```

然后运行，你会发现。。依然不行
需要在activity里面加上
```
static {
    AppCompatDelegate.setCompatVectorFromResourcesEnabled(true);
}
```
开启这个flag后，你就可以正常使用Selector这样的DrawableContainers了。
同时，你还开启了类似android:drawableLeft这样的compound drawable的使用权限，以及RadioButton的使用权限，以及ImageView’s src属性。

Radiobutton同样可以
```
<radiobutton android:button="@drawable/selector" android:layout_height="50dp" android:layout_width="50dp"></radiobutton>
```

还有动态Vector等会再另起一篇好了

