---
title: Scroller
date: 2016-05-03 15:29:13
tags: android_ui_scroller
---
&emsp;&emsp;Scroller是一个帮助View滚动的辅助类，在使用它之前，用户需要通过startScroll来设置滚动的参数，即起始点坐标和（x,y）轴上要滚动的距离。
<!-- more -->
## 1.Scroller
&emsp;&emsp;Scroller它封装了滚动时间、要滚动的目标x轴和y轴，以及在每个时间内View应该滚动到的（x,y）轴的坐标点，这样用户就可以在有效的滚动周期内通过Scroller的getCurX()和getCurY()来获取当前时刻View应该滚动的位置，然后通过调用View的scrollTo或者ScrollBy方法进行滚动。那么如何判断滚动是否结束呢？ 我们只需要覆写View类的computeScroll方法，该方法会在View绘制时被调用，在里面调用Scroller的computeScrollOffset来判断滚动是否完成，如果返回true表明滚动未完成，否则滚动完成。上述说的scrollTo或者ScrollBy的调用就是在computeScrollOffset为true的情况下调用，并且最后还要调用目标View的postInvalidate()或者invalidate()以实现View的重绘。View的重绘又会导致computeScroll方法被调用，从而继续整个滚动过程，直至computeScrollOffset返回false， 即滚动结束。整个过程有点绕。

我们看一个实例：

```bash
public class ScrollLayout extends FrameLayout {
    private String TAG = ScrollLayout.class.getSimpleName();
    Scroller mScroller ;

    public ScrollLayout(Context context) {
        super(context);
        mScroller = new Scroller(context) ;
    }

    // 该函数会在View重绘之时被调用
    @Override
    public void computeScroll() {
        if ( mScroller.computeScrollOffset() ) {
            // 滚动到此,View应该滚动到的x,y坐标上
            this.scrollTo(mScroller.getCurrX(), mScroller.getCurrY());
            // 请求重绘该View，从而又会导致computeScroll被调用，然后继续滚动，
            // 直到computeScrollOffset返回false
          this.postInvalidate();
        }
    }
    // 调用这个方法进行滚动，这里我们只滚动竖直方向
    public void scrollTo(int y) {
          // 参数1和参数2分别为滚动的起始点水平、竖直方向的滚动偏移量
          // 参数3和参数4为在水平和竖直方向上滚动的距离
          mScroller.startScroll(getScrollX(), getScrollY(), 0, y);
          this.invalidate();
    }
}
```

滚动该视图的代码：

```bash
 ScrollLayout scrollView = new ScrollLayout(getContext()) ;
        scrollView.scrollTo(100);
```

通过上面这段代码会让scrollView在y轴上向下滚动100个像素点。我们结合代码来分析一下。首先调用scrollTo(inty)方法**，然后在该方法中通过mScroller.startScroll()方法来设置滚动的参数，再调用invalidate()方法使得该View重绘。重绘时会调用computeScroll方法，**在该方法中通过mScroller.computeScrollOffset()判断滚动是否完成，如果返回true，代表没有滚动完成，此时把该View滚动到此刻View应该滚动到的x、 y位置，这个位置通过mScroller的getCurrX和 getCurrY获得。然后继续调用重绘方法，继续执行滚动过程，直至滚动完成。

