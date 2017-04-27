---
title: Android Data Binding Library
date: 2017-04-22 19:53:18
tags:
---

### 前言

本文介绍了如何使用数据绑定库写声明布局和减少绑定应用程序逻辑和布局所需的代码，在官方介绍基础上加上理解整理而成。

### 准备

新建一个 Project，确保 Android 的 Gradle 插件版本不低于 1.5.0-alpha1：

classpath 'com.android.tools.build:gradle:1.5.0' 然后修改对应模块（Module）的 build.gradle：

```
dataBinding {

    enabled true
}
```

布局文件

Databinding 布局文件格式

```
layout xmlns:android="http://schemas.android.com/apk/res/android">
<data>
   <variable
            name="key"
            type="String" />

    </data>
    <!--原先的根节点（Root Element）-->
    <LinearLayout>
    ....
    </LinearLayout>
</layout>
```

<!--more-->


**注意： 一个的Layout中至多能有两个子节点。多于两个子节点会出异常。Key为引用，下面调用方式需要使用key.xxx 进行调用**

数据对象
添加一个 POJO 类 - User，非常简单，两个属性以及他们的 getter 和 setter。

```
package dragger2.nuoyuan.com.myapplication.bean;

public class Xuser implements Cloneable {
    private final String firstName;
    private final String lastName;

    public Xuser(String firstName, String lastName) {
        this.firstName = firstName;
        this.lastName = lastName;
    }

    public String getFirstName() {
        return this.firstName;
    }

    public String getLastName() {
        return this.lastName;
    }

    @Override
    public Xuser clone() throws CloneNotSupportedException {
        Xuser mail = (Xuser) super.clone();
        return mail;
    }


}
```

稍后，我们会新建一个 Xuser类型的变量，然后把它跟布局文件中声明的变量进行绑定。

定义 Variable

回到布局文件，在 data 节点中声明一个 Xuser 类型的变量 user。

```
<data>
	<variable name="user" type="dragger2.nuoyuan.com.myapplication.bean.Xuser" />
</data>
```

其中 type 属性就是我们在 Java 文件中定义的 Xuser类。

当然，data 节点也支持 import，所以上面的代码可以换一种形式来写。

```
<data>
    <import type="dragger2.nuoyuan.com.myapplication.bean.Xuser" />
    <variable name="user" type="User" />
</data>
```

然后我们刚才在 build.gradle 中添加的那个插件 - com.android.databinding 会根据 xml 文件的名称 Generate 一个继承自 ViewDataBinding 的类。 当然，IDE 中看不到这个文件，需要手动去 build 目录下找。

例如，这里 xml 的文件名叫 activity_basic.xml，那么生成的类就是 ActivityBasicBinding。

注意

java.lang.* 包中的类会被自动导入，可以直接使用，例如要定义一个 String 类型的变量：

```
<variable name="firstName" type="String" />
```

绑定 Variable (变量)

```
 TestdataactivityBinding testdataactivityBinding = DataBindingUtil.setContentView(this, R.layout.testdataactivity);
 Xuser user = new Xuser("Test", "User");
 testdataactivityBinding.setUser(user);
```

除了使用框架自动生成的 TestdataactivityBinding ，我们也可以通过如下方式自定义类名。

注意
TestdataactivityBinding 类是自动生成的，所有的 set 方法也是根据 variable 名称生成的。例如，我们定义了两个变量。

```
<data>
    <variable name="firstName" type="String" />
    <variable name="lastName" type="String" />
</data>
```

那么就会生成对应的两个 set 方法。

```
setFirstName(String firstName);
setLastName(String lastName);
```

###使用 Variable

数据与 Variable 绑定之后，xml 的 UI 元素就可以直接使用了。

```
<TextView
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
android:text="@{user.lastName}" />
```


至此，一个简单的数据绑定就完成了，可参考完整代码
Databinding：https://github.com/yatou252303/googledatabinding


### 一些高级用法

Onclick 设置

### 方式1
Onclick（View view）

```
/**
 * 弹出Toash
 */
public class MyHandlers {
    public void onClickFriend(View view) {
        Toast.makeText(MyApp.context, "dataBinding", 500).show();
    }
}
```

```
<?xml version="1.0" encoding="utf-8"?>
<layout xmlns:android="http://schemas.android.com/apk/res/android">

    <data>
        

        <variable
            name="handler"
            type="dragger2.nuoyuan.com.myapplication.HandleClickListener.MyHandlers" />

       
    </data>

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:orientation="vertical">

        <TextView
            android:layout_width="wrap_content"
            android:layout_height="50dp"
            android:onClick="@{handler::onClickFriend}"
            android:text="@{user.lastName}" />
    </LinearLayout>
</layout>
```


### 方式2

```
Onclick（Parameter var1，Parameter Var2）
```

```
public class Presenter {
    public void onSaveClick(Xuser task) {

        Toast.makeText(MyApp.context, task.getFirstName(), 500).show();

    }

    public void onSaveClick(Context context) {

        Toast.makeText(MyApp.context, "dddddd", 500).show();

    }
}
```

```
<?xml version="1.0" encoding="utf-8"?>
<layout xmlns:android="http://schemas.android.com/apk/res/android">

    <data>
        <variable
            name="click"
            type="dragger2.nuoyuan.com.myapplication.HandleClickListener.Presenter" />


        <variable
            name="user"
            type="dragger2.nuoyuan.com.myapplication.bean.Xuser" />
    </data>

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:orientation="vertical">

        <Button
            android:layout_width="match_parent"
            android:layout_height="100dp"
            android:gravity="center"
            android:onClick="@{(view)->click.onSaveClick(user) }"
            android:text="@{user.firstName}" />

        <TextView
            android:layout_width="wrap_content"
            android:layout_height="50dp"
            android:text="@{user.firstName}" />

    </LinearLayout>
</layout>
```

### Include 嵌套Databinging 用法
 

```
<?xml version="1.0" encoding="utf-8"?>
<layout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:bind="http://schemas.android.com/apk/res-auto">

    <data>

        <import type="java.util.List" />

        <variable
            name="user"
            type="dragger2.nuoyuan.com.myapplication.bean.Xuser" />
    </data>

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:orientation="vertical">

        <include
            layout="@layout/name"
            bind:user="@{user}" />

        <include
            android:id="@+id/views"
            layout="@layout/contact"
            bind:user="@{user}" />

        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:orientation="horizontal">

            <Button
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:onClick="include"
                android:text="@{user.firstName}" />
        </LinearLayout>


    </LinearLayout>



</layout>
```

在Include的布局文件中必须要有 user

```
<?xml version="1.0" encoding="utf-8"?>
<layout xmlns:android="http://schemas.android.com/apk/res/android">

    <data>

        <variable
            name="user"
            type="dragger2.nuoyuan.com.myapplication.bean.Xuser" />

    </data>

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="50dp"
        android:background="@color/colorAccent"
        android:orientation="vertical">

        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="@{user.lastName}" />

        <TextView
            android:id="@+id/innertext"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content" />

    </LinearLayout>

</layout>
```

构建过程中编译器会对应Databinding布局文件生成Binding文件
而且会对每一个设置Id的组件的生成final Static 成员变量

![这里写图片描述](http://img.blog.csdn.net/20170418173007616?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbzI3OTY0MjcwNw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

其中view 是

```
<include
            android:id="@+id/views"
            layout="@layout/contact"
            bind:user="@{user}" />
```

的id.这样就可以方便的进行调用的，而不是使用findViewByid（）

### DataBinding 支持的操作符号

```
Mathematical + - / * %
String concatenation +
Logical && ||
Binary & | ^
Unary + - ! ~
Shift >> >>> <<
Comparison == > < >= <=
instanceof
Grouping ()
Literals - character, String, numeric, null
Cast
Method calls
Field access
Array access []
Ternary operator ?:
android:text="@{String.valueOf(index + 1)}"
android:visibility="@{age < 13 ? View.GONE : View.VISIBLE}"
android:transitionName='@{"image_" + id}'
```

###Missing Operations

```
this
super
new
```

增加的操作

```
android:text="@{user.displayName ?? user.lastName}"
```

DataBinding 在ListAdapter 和 RecycleView中的用法DataBinding Import用法以及一些基本数据类型的使用

### 查看源码

DataBinding 布局中使用的Import导入具体的包名就可以使用相应的包功能这点同java类似
基本类型可以不显示进行导包
 

```
  <data>

        <import type="android.util.SparseArray" />

        <import type="java.util.Map" />

        <import type="java.util.List" />

        <variable
            name="list"
            type="List&lt;String&gt;" />

        <variable
            name="sparse"
            type="SparseArray&lt;String&gt;" />

        <variable
            name="map"
            type="Map&lt;String, String&gt;" />

        <variable
            name="index"
            type="int" />

        <variable
            name="key"
            type="String" />
</data>
```

用法

```
<TextView
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="list[index]:"
    android:textStyle="bold" />
```

### DataBinding  类型转换使用
请查看demo
###DataBinding 自定义的属性用法的

 自定义使用起来相当好用，而且不需要在attrs.xml中进行声明，只要提供相应set方法即可。Databinding会自动搜索相应的Set方法
  

```
<dragger2.nuoyuan.com.myapplication.utils.NYImageView
            android:id="@+id/img"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            app:error="@{@drawable/error}"
            app:imageUrl="@{imageUrl}" />
```

提供调用函数的，需要为静态

```
@BindingAdapter({"imageUrl", "error"})
public static void loadImage(ImageView view, String url, Drawable error) {
    Glide.with(activity).load(url).into(view);
}
```

Bindview之后设置显示Url地址

```
attrsetActivityBinding.setImageUrl("http://tse4.mm.bing.net/th?id=OIP.MKt6WVLkDWmz5ZKgzujUcwEsEs&w=204&h=201&c=7&qlt=90&o=4&dpr=1.1&pid=1.7");
```

### DataBinding 中ViewStub 用法
请查看demo






demo地址：https://github.com/yatou252303/googledatabinding




------
>引用：

  >参考 db demo https://github.com/LyndonChin/MasteringAndroidDataBinding
 >googledb 翻译 http://www.jcodecraeer.com/a/anzhuokaifa/androidkaifa/2015/0603/2992.html
 > 官方地址 https://developer.android.com/topic/libraries/data-binding/index.html#generated_binding











