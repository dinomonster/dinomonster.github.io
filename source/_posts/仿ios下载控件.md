---
title: 仿ios下载控件
date: 2016-08-24 17:29:03
tags: Android
---

仿着做了个类似ios appstore下载按钮
效果图如下：
<img src="/2.gif" width = "300" height = "200" alt="2" align=center />
### 1.创建自定义view
代码中注释写的很全

```java
import android.content.Context;
import android.content.res.TypedArray;
import android.graphics.Bitmap;
import android.graphics.BitmapFactory;
import android.graphics.Canvas;
import android.graphics.Color;
import android.graphics.Paint;
import android.graphics.Rect;
import android.graphics.RectF;
import android.util.AttributeSet;
import android.view.View;

import com.tyky.edu.teacher.R;


/**
 * Created by Dino on 8/18 0018.
 */
public class DownloadProgress extends View{
    private int statue;
    public static final int downloadStatue = 0;
    public static final int progressStatue = 1;
    public static final int finishStatue = 2;

    private int width;// 控件的宽度
    private int height;// 控件的高度
    private int radius;// 圆形的半径
    private int socktwidth = dp2px(2);// 圆环进度条的宽度
    private Paint paint = new Paint();
    private int progress = 70;// 百分比0~100;
    private int textSize = dp2px(10);// 文字大小
    private Bitmap bitmap;
    @Deprecated
    float scale = 0.35f;// 中间背景图片相对圆环的大小的比例
    private int preColor = Color.parseColor("#ffffff");// 进度条未完成的颜色
    private int progressColor = Color.parseColor("#2DB6E6");// 进度条颜色
    private float paddingscale = 0.8f;// 控件内偏距占空间本身的比例
    private int CircleColor = Color.parseColor("#ffffff");// 圆中间的背景颜色
    private int textColor = progressColor;// 文字颜色
    private onProgressListener monProgress;// 进度时间监听
    private int startAngle = 270;
    RectF rectf = new RectF();

    private Bitmap bitmpre;
    private int whiteColor = Color.parseColor("#ffffff");// 矩形中间的背景颜色

    public DownloadProgress(Context context, AttributeSet attrs) {
        super(context, attrs);
        TypedArray a = context.obtainStyledAttributes(attrs, R.styleable.DownloadProgress);
        statue = a.getInteger(R.styleable.DownloadProgress_statue, 0);// 默认0

        bitmap = BitmapFactory.decodeResource(getResources(),R.drawable.square);
        bitmpre = BitmapFactory.decodeResource(getResources(),R.drawable.download_pre);
        width = bitmpre.getWidth();
        height = bitmpre.getHeight();
    }

    public DownloadProgress(Context context, AttributeSet attrs, int defStyle) {
        super(context, attrs, defStyle);

    }

    @Override
    protected void onMeasure(int widthMeasureSpec, int heightMeasureSpec)
    {
        int widthMode = MeasureSpec.getMode(widthMeasureSpec);
        int widthSize = MeasureSpec.getSize(widthMeasureSpec);
        int heightMode = MeasureSpec.getMode(heightMeasureSpec);
        int heightSize = MeasureSpec.getSize(heightMeasureSpec);
        int reWidth;
        int reHeight;
        if (widthMode == MeasureSpec.EXACTLY)
        {
            reWidth = widthSize;
        } else
        {
            int desired = (int) (getPaddingLeft() + width + getPaddingRight());
            reWidth = desired;
        }

        if (heightMode == MeasureSpec.EXACTLY)
        {
            reHeight = heightSize;
        } else
        {
            int desired = (int) (getPaddingTop() + height + getPaddingBottom());
            reHeight = desired;
        }

        setMeasuredDimension(reWidth, reHeight);
    }

    @Override
    protected void onDraw(Canvas canvas) {
        switch (statue){
            case downloadStatue:
                drawDownload(canvas);
                break;
            case progressStatue:
                drawProgress(canvas);
                break;
            case finishStatue:
                drawOpen(canvas);
                break;
        }

        super.onDraw(canvas);
    }

    private void drawDownload(Canvas canvas){
        //图片缩小显示
        int scalse = 5;
        Rect srcRect = new Rect(0,0,width,height);
        Rect dstRect = new Rect(width/scalse,height/scalse,width/scalse,height/scalse);
        canvas.drawBitmap(bitmpre,0,0,paint);
        canvas.drawBitmap(bitmpre,srcRect,dstRect,paint);
    }

    private void drawOpen(Canvas canvas){
        int border = 1;
        int h = bitmpre.getHeight()/2;
        RectF outRect = new RectF(0, h/2,width, h/2+h);
        RectF inRect = new RectF(border,h/2+border,width-border,h/2+h-border);
        paint.setAntiAlias(true);
        paint.setColor(progressColor);
        // 绘制外矩形
        canvas.drawRoundRect(outRect,5,5, paint);
        paint.setColor(whiteColor);
        // 绘制内矩形
        canvas.drawRoundRect(inRect,5,5, paint);
        String v ="打开";
        paint.setColor(textColor);
        paint.setTextSize(textSize);
        // 绘制中间文字
        Paint.FontMetricsInt fontMetrics = paint.getFontMetricsInt();
        int baseline = ((int)(inRect.bottom + inRect.top) - fontMetrics.bottom - fontMetrics.top) / 2;
        // 下面这行是实现水平居中，drawText对应改为传入targetRect.centerX()
        paint.setTextAlign(Paint.Align.CENTER);
        canvas.drawText(v, inRect.centerX(), baseline, paint);
    }

    private void drawProgress(Canvas canvas){
        int size = height;
        if (height > width)
            size = width;
        radius = (int) (size * paddingscale / 2f);
        paint.setAntiAlias(true);
        paint.setColor(progressColor);
        canvas.drawCircle(width / 2, height / 2, radius+2, paint);//画最外面的边线
        paint.setColor(preColor);
        // 绘制最大的圆 进度条圆环的背景颜色（未走到的进度）就是这个哦
        canvas.drawCircle(width / 2, height / 2, radius, paint);
        rectf.set((width - radius * 2) / 2f, (height - radius * 2) / 2f,
                ((width - radius * 2) / 2f) + (2 * radius),
                ((height - radius * 2) / 2f) + (2 * radius));
        paint.setColor(progressColor);
        canvas.drawArc(rectf, startAngle, progress * 3.6f, true, paint);
        paint.setColor(CircleColor);
        // 绘制用于遮住伞形两个边的小圆
        canvas.drawCircle(width / 2, height / 2, radius - socktwidth, paint);
        if (bitmap != null) {// 绘制中间的图片
            int width2 = (int) (rectf.width() * scale);
            int height2 = (int) (rectf.height() * scale);
            rectf.set(rectf.left + width2, rectf.top + height2, rectf.right
                    - width2, rectf.bottom - height2);
            canvas.drawBitmap(bitmap, null, rectf, null);
        }
    }

    public int dp2px(int dp) {
        return (int) ((getResources().getDisplayMetrics().density * dp) + 0.5);
    }

    /**
     * 设置进度
     *
     * @param progress
     *            <p>
     *            ps: 百分比 0~100;
     */
    public void setProgress(int progress) {
        if (progress > 100)
            return;
        this.progress = progress;
        invalidate();
        if (monProgress != null)
            monProgress.onProgress(progress);
    }
    /**
     * 设置状态
     *
     */
    public void setStatue(int statue) {
        this.statue = statue;
        invalidate();
    }

    /**
     * 设置圆环进度条的宽度 px
     */
    public DownloadProgress setProdressWidth(int width) {
        this.socktwidth = width;
        return this;
    }

    /**
     * 设置文字大小
     *
     * @param value
     */
    public DownloadProgress setTextSize(int value) {
        textSize = value;
        return this;
    }

    /**
     * 设置文字大小
     *
     */
    public DownloadProgress setTextColor(int color) {
        this.textColor = color;
        return this;
    }

    /**
     * 设置进度条之前的颜色
     *
     */
    public DownloadProgress setPreProgress(int precolor) {
        this.preColor = precolor;
        return this;
    }

    /**
     * 设置进度颜色
     *
     * @param color
     */
    public DownloadProgress setProgressColor(int color) {
        this.progressColor = color;
        return this;
    }

    /**
     * 设置圆心中间的背景颜色
     *
     * @param color
     * @return
     */
    public DownloadProgress setCircleBackgroud(int color) {
        this.CircleColor = color;
        return this;
    }

    /**
     * 设置圆相对整个控件的宽度或者高度的占用比例
     *
     * @param scale
     */
    public DownloadProgress setPaddingscale(float scale) {
        this.paddingscale = scale;
        return this;
    }

    /**
     * 设置开始的位置
     *
     * @param startAngle
     *            0~360
     *            <p>
     *            ps 0代表在最右边 90 最下方 按照然后顺时针旋转
     */
    public DownloadProgress setStartAngle(int startAngle) {
        this.startAngle = startAngle;
        return this;
    }

    public interface onProgressListener {
        void onProgress(int value);
    }
}

```


### 2.在attrs文件添加如下代码

```java
<declare-styleable name="DownloadProgress">
        <!-- 状态0未下载 1下载中 2下载完成 -->
        <attr name="statue" format="integer" />
    </declare-styleable>
```


### 3.使用方法

在xml布局中：
```java
<com.tyky.edu.teacher.ui.DownloadProgress
            android:id="@+id/download_btn"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_centerVertical="true"
            android:gravity="center"
            app:statue="1"
            android:layout_marginRight="10dp" />
```

记得在父布局加上`xmlns:android="http://schemas.android.com/apk/res/android"`

下载回调的时候setStatue()来更改状态，setProgress更改进度值


还有可以优化的地方，加上状态切换的动画，后面有时间再做。