---
title: Android深入理解自定义属性
date: 2016-04-17 19:42:00
tags: android_resource_attr
---
&emsp;&emsp; 对于自定义属性，大家肯定都不陌生，遵循以下几步，就可以实现：

1. 自定义一个CustomView(extends View )类
2. 编写values/attrs.xml，在其中编写styleable和item等标签元素
3. 在布局文件中CustomView使用自定义的属性（注意namespace）
4. 在CustomView的构造方法中通过TypedArray获取
<!-- more --> 
### 1.基本的举例

- 自定义属性的声明文件

```bash
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <declare-styleable name="test">
        <attr name="text" format="string"/>
        <attr name="testAttr" format="integer"/>
    </declare-styleable>
</resources>
```

- 自定义View类

```bash
public class MyTextView extends View {

    private static final String TAG = MyTextView.class.getSimpleName();

    public MyTextView(Context context, AttributeSet attrs) {
        super(context, attrs);

        TypedArray ta = context.obtainStyledAttributes(attrs, R.styleable.test);

        String text = ta.getString(R.styleable.test_testAttr);
        int textAttr = ta.getInteger(R.styleable.test_text, -1);

        Log.e(TAG, "text = " + text + " , textAttr = " + textAttr);

        ta.recycle();
    }
```

- 布局文件中使用

```bash
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    xmlns:a="http://schemas.android.com/apk/res/com.example.test"
    android:layout_width="match_parent"
    android:layout_height="match_parent" >

    <com.example.test.MyTextView
        android:layout_width="100dp"
        android:layout_height="200dp"
        a:testAttr="520"
        a:text="helloworld" />

</RelativeLayout>
```

### 2.AttributeSet与TypedArray

构造方法中的有个参数叫做AttributeSet（eg: MyTextView(Context context, AttributeSet attrs) ）这个参数看名字就知道包含的是参数的集合，那么我能不能通过它去获取我的自定义属性呢？
  
恩，没错，通过AttributeSet可以获得布局文件中定义的所有属性的key和value（还有一些方法，自己去尝试），那么是不是说TypedArray这个鬼可以抛弃了呢？答案是：NO!

**那么TypedArray是什么鬼？**

通过AttributeSet获取的值，如果是引用都变成了“**@+数字**”的字符串。TypedArray其实是用来简化我们的工作的，比如如果布局中的属性的值是引用类型（比如：@dimen/dp100），如果使用AttributeSet去获得最终的像素值，那么需要第一步拿到id，第二步再去解析id。而TypedArray正是帮我们简化了这个过程。

### 3.declare-styleable
首先删除declare-styleable的标签，那么现在的attrs.xml为：

```bash
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <attr name="testAttr" format="integer" />
</resources>
```

MyTextView实现：

```bash
public class MyTextView extends View {

    private static final String TAG = MyTextView.class.getSimpleName();

    private static final int[] mAttr = { android.R.attr.text, R.attr.testAttr };
    private static final int ATTR_ANDROID_TEXT = 0;
    private static final int ATTR_TESTATTR = 1;

    public MyTextView(Context context, AttributeSet attrs) {
        super(context, attrs);

        // ==>use typedarray
        TypedArray ta = context.obtainStyledAttributes(attrs, mAttr);

        String text = ta.getString(ATTR_ANDROID_TEXT);
        int textAttr = ta.getInteger(ATTR_TESTATTR, -1);
        //输出 text = Hello world! , textAttr = 520
        Log.e(TAG, "text = " + text + " , textAttr = " + textAttr);

        ta.recycle();
    }

}
```

貌似多了些代码，可以看到我们声明了一个int数组，数组中的元素就是我们想要获取的attr的id。并且我们根据元素的在数组中的位置，定义了一些整形的常量代表其下标，然后通过TypedArray进行获取。 

可以看到，我们原本的：

```bash
R.styleable.test => mAttr
R.styleable.test_text => ATTR_ANDROID_TEXT(0)
R.styleable.test_testAttr => ATTR_TESTATTR(1)
```

styleale的出现系统可以为我们完成很多常量（int[]数组，下标常量）等的编写，简化我们的开发工作（想想如果一堆属性，自己编写常量，你得写成什么样的代码）。那么大家肯定还知道declare-styleable的name属性，一般情况下写的都是我们自定义View的类名。主要为了直观的表达，该declare-styleable的属性，都是改View所用的。


**总结：**
1. attrs.xml里面的declare-styleable以及item，android会根据其在R.java中生成一些常量方便我们使用(aapt干的)，本质上，我们可以不声明declare-styleable仅仅声明所需的属性即可。
2. 我们在View的构造方法中，可以通过AttributeSet去获得自定义属性的值，但是比较麻烦，而TypedArray可以很方便的便于我们去获取。
3. 我们在自定义View的时候，可以使用系统已经定义的属性。例如：
```bash
<declare-styleable name="test">
        <attr name="android:text" />
        <attr name="testAttr" format="integer" />
</declare-styleable>
```

