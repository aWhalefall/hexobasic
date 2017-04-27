---
title: 学习 greenDao 3
date: 2017-04-24 20:57:38
tags: android

---



前言
 前面已经分析orm数据库的使用，这篇开始时下最火greendao使用分析。分几个问题来分析


 [android Sqlite Orm 实现方式](http://blog.csdn.net/o279642707/article/details/70157865)  
```
1.文件生成  √
2.增删改查 √
3.代码结构 √
4.数据库升级
5.项目依赖  √
6.优缺点，为什么选择greenDao 而不是其他
7.greenDao  主键设置  为基本Long 类型  传值 = Null √
8.greenDao 需要重新刷新生成的类，否则会出现bug √
```

<!--more-->


###  项目依赖配置

```
 buildscript {
    repositories {
        jcenter()
    }

    dependencies {
        classpath 'com.android.tools.build:gradle:2.3.1'
        classpath 'org.greenrobot:greendao-gradle-plugin:3.2.2' // add plugin
        // NOTE: Do not place your application dependencies here; they belong
        // in the individual module build.gradle files
    }
}

allprojects {
    repositories {
        jcenter()
    }
}

task clean(type: Delete) {
    delete rootProject.buildDir
}
```
### Build.gradle 配置

```
apply plugin: 'com.android.application'
apply plugin: 'org.greenrobot.greendao' // apply plugin


dependencies {
    compile 'org.greenrobot:greendao:3.2.2'
    compile 'net.zetetic:android-database-sqlcipher:3.5.6' //加密
    compile 'com.android.support:recyclerview-v7:25.0.0'
}
```

 
### 代码结构

 本文从一个简单的pojo开始（javabean）

```
 @Entity
public class Student {

    @Id(autoincrement = true)
    public Long id;

    @Property(nameInDb = "stu_name") 
    public String name;
    @Transient
    public int sex;
}
    
```

最基础java bean 格式 ， makeProject （ctrl+f9） 进行项目构建，会自动生成如下代码,代码默认路径在 app/build/generated/source 下面，生产的路径可以自定义，下面会介绍配置方式。 

generationCode

```
@Entity
public class Student {

    @Id(autoincrement = true)
    public Long id;

    @Property(nameInDb = "stu_name")  //默认大写，可以进行指定
    public String name;
    @Transient
    public int sex;

    @Generated(hash = 1097502469)
    public Student(Long id, String name) {
        this.id = id;
        this.name = name;
    }
    @Generated(hash = 1556870573)
    public Student() {
    }
    public Long getId() {
        return this.id;
    }
    public void setId(Long id) {
        this.id = id;
    }
    public String getName() {
        return this.name;
    }
    public void setName(String name) {
        this.name = name;
    }

}
```

###　通过什么方式进行生成的？

有兴趣可以查看 github开源文件

![这里写图片描述](http://img.blog.csdn.net/20170424164129275?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbzI3OTY0MjcwNw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

如上图两个文件夹，简单介绍一下。就是使用freemark 模板生成工具进行实例代码生成，而freemark 不止可以生成java temp ,同样html 模板也可以生成， freemark 是一种可以简单模板代码的工具。有兴趣可以查看
链接：xxxxxxxxxxxxxxxx .

Greendao为用户提供了Gradle plugs 进行管理生成代码路径，版本迭代配置

在App-->build.gradle 文件中设置

```
greendao {
    schemaVersion 2  //版本迭代修改
    daoPackage 'com.greendao.wc.db' 生成dao路径
    targetGenDir 'src/main/java'
    generateTests true  //是否生成测试
    targetGenDirTests 'src/androidTest/java' 测试文件存放路径
}
```

 

### 增删改查

构建Student 实体不止生成上面的代码同时生成
![这里写图片描述](http://img.blog.csdn.net/20170424164241650?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbzI3OTY0MjcwNw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
studentDao ，上面图中不止有Dao文件还有 xxxxxSession 和xxxxmaster 文件 他们的关系可以看一个类图



类图：
![这里写图片描述](http://img.blog.csdn.net/20170424164139572?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbzI3OTY0MjcwNw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

这张类图主要描述的层次结构 xxmaster 对数据库Db进行封装，已经负责数据库创建，升级，链接等任务，XXSession主要负责各种实体Dao管理。Dao负责具体表操作，Entity数据库表的映射等 Schme 在Green Dao 3中没有体现出来。如想了解查看GreenDao2.


上面已经的说了，Dao类负责针对实体表操作的一个封装，对表的简单操作包括（增删改查），这里不讨论 表间关系的维护以及操作。

 增删改查一并被封装到了 AbstractDao 中看类结构
 
![这里写图片描述](http://img.blog.csdn.net/20170424164329494?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbzI3OTY0MjcwNw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
上面展示部分类中方法。

### greenDao  主键设置 
 grennDao必须要有一个主键，而主键类型必须是long，Long 

```
 @Entity
public class Student {

    @Id(autoincrement = true)
    public Long id;

    @Property(nameInDb = "stu_name")  //默认大写，可以进行指定
    public String name;
    @Transient
    public int sex;
}
```

为包装Long 类型  传值 = Null. 
如果设置成long则需要传值，造成字段不进行自增。建议设置成Long 并传Null，交给GreenDao去处理主键自增

greenDao 更新实体注意
greenDao 更新实体需要重新构建生成的类，否则会出现bug，方式一移除之前自动生成的方法，构造重新生成。这里有一个注解@keep 可以告知greenDao那些方法不进行覆盖。

##数据库升级

虽然的GreenDao已经有Master进行升级处理，但是我们需要更多的自定义处理，则需要自己对下面的方法重写

```
    public static class DevOpenHelper extends OpenHelper {
        public DevOpenHelper(Context context, String name) {
            super(context, name);
        }

        public DevOpenHelper(Context context, String name, CursorFactory factory) {
            super(context, name, factory);
        }

        @Override
        public void onUpgrade(Database db, int oldVersion, int newVersion) {
            Log.i("greenDAO", "Upgrading schema from version " + oldVersion + " to " + newVersion + " by dropping all tables");
            dropAllTables(db, true);
            onCreate(db);
        }
}
```

这里使用一篇blog提供的升级方式进行修改的

Blog地址：http://blog.csdn.net/fancylovejava/article/details/46713445

大致方式就是：

![这里写图片描述](http://img.blog.csdn.net/20170424164357366?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbzI3OTY0MjcwNw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)


### 类结构

![这里写图片描述](http://img.blog.csdn.net/20170424164431620?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbzI3OTY0MjcwNw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
首先在onupgrade（）中比较版本，根据版本调用对应版本的数据库操作的函数完成该版本更新，知道oldVersion == newVersion

### 优缺点，为什么选择greenDao 而不是其它Orm数据库

  好用，性能高，文档多，社区活跃，等等。


### demo地址：http://pan.baidu.com/s/1o8Av7IU



－－－

>引用：

>Migrator：http://blog.csdn.net/fancylovejava/article/details/46713445
>Greendao3 官方: 
http://greenrobot.org/greendao/documentation/updating-to-greendao-3-and-annotations/






 



