### android ViewGroup构造方法的作用

```
public CustomTouchView2(Context context) {
    super(context);
    init(null);
}

public CustomTouchView2(Context context, @Nullable AttributeSet attrs) {
    super(context, attrs);
    init(attrs);
}


public CustomTouchView2(Context context, @Nullable AttributeSet attrs, int defStyleAttr) {
    super(context, attrs, defStyleAttr);
}

public CustomTouchView2(Context context, @Nullable AttributeSet attrs, int defStyleAttr, int defStyleRes) {
    super(context, attrs, defStyleAttr, defStyleRes);
}
```



1.(Context context)   一般用于java代码中使用new 的方式

2.(Context context, @Nullable AttributeSet attrs)  用于布局文件中使用 style方式，在attrs文件中声明styleable属性的方式，如

```
<declare-styleable name="CustomToolBar">
    <attr name="back_src" format="reference" />
</declare-styleable>
然后通过初始化中
TypedArray typedArray = context.obtainStyledAttributes(attrs, R.styleable.CustomToolBar);
int title_text_color = typedArray.getColor(R.styleable.CustomToolBar_title_text_color, 0xff000000);
去获取属性值
```

3.(Context context, @Nullable AttributeSet attrs, int defStyleAttr)

适用于主题配置的属性如：

 <style name="AppTheme" parent="@android:style/Theme.Holo">
        <item name="android:textViewStyle">@style/BlueTextStyle</item>
        <item name="MyCustomViewDefStyleAttr">@style/CustomStyle</item>
 </style>

```java
 <style name="CustomStyle">
       <item name="custom_attr1">attr1_defStyleAttr</item>
        <item name="custom_attr2">attr2_defStyleAttr</item>
        <item name="custom_attr3">attr3_defStyleAttr</item>
    </style>
```

this(context, attrs, com.android.internal.R.attr.textViewStyle);

通过主题配置的属性去获取对应的

```java
TypedArray typedArray= getContext().getTheme().obtainStyledAttributes(attrs,R.styleable.CustomTouchView,R.attr.MyCustomViewDefStyleAttr,0);
```

4.(Context context, @Nullable AttributeSet attrs, int defStyleAttr, int defStyleRes)

<style name="MyCustomViewDefStyleRes">
    <item name="custom_attr1">attr1_defStyleRes</item>
    <item name="custom_attr2">attr2_defStyleRes</item>
    <item name="custom_attr3">attr3_defStyleRes</item>
    <item name="custom_attr4">attr4_defStyleRes</item>
</style>
TypedArray typedArray= getContext().getTheme().obtainStyledAttributes(attrs,R.styleable.CustomTouchView,0,R.style.MyCustomViewDefStyleRes);

注意：只有当defStyleAttr为0或者当前Theme中没有给defStyleAttr属性赋值时才起作用

**在布局xml中直接定义 > 在布局xml中通过style定义 > 自定义View所在的Activity的Theme中指定style引用 > 构造函数中defStyleRes指定的默认值**



