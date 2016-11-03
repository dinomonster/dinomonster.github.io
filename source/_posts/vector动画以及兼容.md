---
title: vector动画以及兼容
date: 2016-10-06 16:32:01
tags: [Android,Vector]
---

### 之前讲过了用静态的Vector，这里讲一下使用动画，这里就直接使用支持包了
#### 1.引用com.android.support:appcompat-v7:23.2.0以上的包
#### 2.在build中defaultConfig{}里面写`vectorDrawables.useSupportLibrary = true`
上面两步和使用静态vector要求一样
下面开始就是使用动画的步骤
#### 1.新建vector静态文件

```java
<?xml version="1.0" encoding="utf-8"?>
<vector xmlns:android="http://schemas.android.com/apk/res/android"
    android:width="256dp"
    android:height="256dp"
    android:viewportHeight="70"
    android:viewportWidth="70">
    <path
        android:name="heart1"
        android:pathData="..."
        android:strokeColor="#E91E63"
        android:strokeWidth="1"
        />
    <path
        android:name="heart2"
        android:pathData="..."
        android:strokeColor="#E91E63"
        android:strokeWidth="1"
        />

    
</vector>
```

#### 2.新建动画文件
```java
<?xml version="1.0" encoding="utf-8"?>
<objectAnimator
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:duration="6000"
    android:propertyName="trimPathEnd"
    android:valueFrom="0"
    android:valueTo="1"
    android:valueType="floatType"/>
```

#### 3.新建Animated-Vector图像
```java
<?xml version="1.0" encoding="utf-8"?>
<animated-vector
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:drawable="@drawable/ic_android_black_24dp">
    <target
        android:name="heart1"
        android:animation="@animator/path_animator"/>
    <target
        android:name="heart2"
        android:animation="@animator/path_animator"/>
   

</animated-vector>
```

#### 4.在布局文件引用Animated-Vector图像
```java
<android.support.v7.widget.AppCompatImageView
        android:id="@+id/aaaa"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        app:srcCompat="@drawable/v_animation"/>
```

#### 5.在代码中启动动画
```java
AppCompatImageView imageView = (AppCompatImageView)findViewById(R.id.aaaa);
Drawable animation = imageView.getDrawable();
        if (animation instanceof Animatable) {
            ((Animatable) animation).start();
        }
```

完整代码可以可以查看[github](https://github.com/dinomonster/VectorsTest "github")

附上两个图标库
[iconfont](http://www.iconfont.cn/ "iconfont")
[easyicon](http://www.easyicon.net/ "easyicon")

还有[svg在线转vector](http://inloop.github.io/svg2android/ "svg在线转vector")