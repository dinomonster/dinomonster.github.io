---
title: 使用AnimatorSet执行一组动画
date: 2016-10-06 16:58:25
tags: Android
---

#### 需求要实现一组动画按顺序执行，发现用AnimationSet实现不了，只能一组动画同时播放。发现还有个AnimatorSet可以实现。

AnimationSet 与 AnimatorSet 最大的不同在于，AnimationSet 使用的是 Animation 子类、AnimatorSet 使用的是 Animator 的子类。

Animation 是针对视图外观的动画实现，动画被应用时外观改变但视图的触发点不会发生变化，还是在原来定义的位置。

Animator 是针对视图属性的动画实现，动画被应用时对象属性产生变化，最终导致视图外观变化。

```java
public static void startScale(View view, long duration,float fromeScale,float toScale) {
        AnimatorSet animationSet = new AnimatorSet();
        ObjectAnimator scaleX = ObjectAnimator.ofFloat(view, "scaleX", fromeScale, toScale);
        ObjectAnimator scaleY = ObjectAnimator.ofFloat(view, "scaleY", fromeScale, toScale);
        ObjectAnimator scaleXshrink = ObjectAnimator.ofFloat(view, "scaleX", toScale, fromeScale);
        ObjectAnimator scaleYshrink = ObjectAnimator.ofFloat(view, "scaleY", toScale, fromeScale);
        ObjectAnimator scaleXRe = ObjectAnimator.ofFloat(view, "scaleX", fromeScale, toScale);
        ObjectAnimator scaleYRe = ObjectAnimator.ofFloat(view, "scaleY", fromeScale, toScale);
        animationSet.setDuration(duration);
        animationSet.playSequentially(scaleX, scaleXshrink, scaleXRe);
        animationSet.playSequentially(scaleY, scaleYshrink, scaleYRe);
        animationSet.start();
    }
```

以上是实现的对一个view放大>缩小>放大 按顺序执行的一组动画
还可以调用其play、before、with、after 等方法设置动画的执行顺序。
