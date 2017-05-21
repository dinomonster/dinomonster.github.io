---
title: surfaceView和View的区别
date: 2017-5-16 10:52:55
tags: Android
---

### surfaceView和View的区别在于：

surfaceView是在一个新起的单独线程中可以重新绘制画面，而View必须在UI的主线程中更新画面。那么在UI的主线程中更新画面 可能会引发问题，比如你更新画面的时间过长，那么你的主UI线程会被你正在画的函数阻塞。
那么将无法响应按键，触屏等消息。当使用surfaceView 由于是在新的线程中更新画面所以不会阻塞你的UI主线程。
但这也带来了另外一个问题，就是事件同步。比如你触屏了一下，你需要surfaceView中 thread处理，一般就需要有一个event queue的设计来保存touch event，这会稍稍复杂一点，因为涉及到线程同步。

### 所以基于以上，根据游戏特点，一般分成两类。

- 被动更新画面的。比如棋类，这种用view就好了。因为画面的更新是依赖于 onTouch 来更新，可以直接使用 invalidate。 因为这种情况下，这一次Touch和下一次的Touch需要的时间比较长些，不会产生影响。
- 主动更新。比如一个人在一直跑动。这就需要一个单独的thread不停的重绘人的状态，避免阻塞main UI thread。所以显然view不合适，需要surfaceView来控制。

### SurfaceView简介

在一般的情况下，应用程序的View都是在相同的GUI线程中绘制的。这个主应用程序线程同时也用来处理所有的用户交互(例如，按钮单击或者文本输入)。
可以把容易阻塞的处理移动到后台线程中。但是，对于一个View的onDraw方法，不能这样做，因为从后台线程修改一个GUI元素会被显式地禁止的。
当需要快速地更新View的UI，或者当渲染代码阻塞GUI线程的时间过长的时候，SurfaceView就是解决上述问题的最佳选择。 
SurfaceView封装了一个Surface对象，而不是Canvas。这一点很重要，因为Surface可以使用后台线程绘制。对于那些资源敏感的 操作，或者那些要求快速更新或者高速帧率的地方，例如，使用3D图形，创建游戏，或者实时预览摄像头，这一点特别有用。
独立于GUI线程进行绘图的代价是额外的内存消耗，所以，虽然它是创建定制的View的有效方式--有时甚至是必须的，但是使用Surface View的时候仍然要保持谨慎。

### 何时应该使用SurfaceView？
- SurfaceView使用的方式与任何View所派生的类都是完全相同的。可以像其他View那样应用动画，并把它们放到布局中。
- SurfaceView封装的Surface支持使用本章前面所描述的所有标准Canvas方法进行绘图，同时也支持完全的OpenGL ES库。
- 使用OpenGL，你可以再Surface上绘制任何支持的2D或者3D对象，与在2D画布上模拟相同的效果相比，这种方法可以依靠硬件加速(可用的时候)来极大地提高性能。
- 对于显示动态的3D图像来说，例如，那些使用Google Earth功能的应用程序，或者那些提供沉浸体验的交互式游戏，SurfaceView特别有用。它还是实时显示摄像头预览的最佳选择。
### 创建一个新的SurfaceView控件
- 要创建一个新的SurfaceView，需要创建一个新的扩展了SurfaceView的类，并实现SurfaceHolder.Callback。
- SurfaceHolder回调可以在底层的Surface被创建和销毁的时候通知View，并传递给它对SurfaceHolder对象的引用，其中包含了当前有效的Surface。
- 一个典型的Surface View设计模型包括一个由Thread所派生的类，它可以接收对当前的SurfaceHolder的引用，并独立地更新它。
