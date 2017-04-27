---
title: Evenbus 3.0 分析
date: 2017-04-27 15:08:49
categories: 编程
tags: android
---

#### 前言：

 EvenBus 很火，不知道什么时候开始，自定义生成模板代替注解。EvenBus 3.0 同样支持了这种预编译处理注解机制。Annotation processor indexes annotation information for efficient subscriber registration on Android( 摘自，evenbus 3.0)


#### EvenBus 是什么，以及有那些同类的产品？

**EventBus是为Android优化的发布/订阅事件总线。**
![这里写图片描述](https://github.com/greenrobot/EventBus/raw/master/EventBus-Publish-Subscribe.png)

可以和EvenBus做同样事情的系统元件.广播，Handler，观察者模式
可以看demo


#### EvenBus 能做什么？

<!-- more -->

```
1.简化组件之间的通信
2.解耦事件发送者和接收者
3.对Activity，Fragment和后台线程表现良好
4.避免复杂和容易出错的依赖关系和生命周期问题
5.很快专门针对高性能进行优化
6.代码量<50k
7.具有传送线程，用户优先级等高级功能
```



#### 2.EvenBus有哪几种角色参与，各个角色起什么作用？

![这里写图片描述](http://img.blog.csdn.net/20170427145714863?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbzI3OTY0MjcwNw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)




#### 3.EvenBus核心类图
![这里写图片描述](http://img.blog.csdn.net/20170427145728020?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbzI3OTY0MjcwNw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

#### 4.EvenBus 核心点。如何快速掌握核心点

 简单三步开始EvenBus

>自从EventBus 3.x发布之后其通过注解预编译的方式解决了之前通过反射机制所引起的性能效率问题，其中注解预编译所采用的的就是android-apt的方式，不过最近Apt工具的作者宣布了不再维护该工具了，因为Android Studio推出了官方插件，并且可以通过gradle来简单的配置，它就是annotationProcessor. 


之前处理方式需要导入 Apt 插件，这里不过多介绍

```
dependencies {
...
...
    compile files('libs/eventbus-3.0.0.jar')
    annotationProcessor 'org.greenrobot:eventbus-annotation-processor:3.0.1'
}
```


1.Step 1: Define events

```
public class MessageEvent {
 
    public final String message;
 
    public MessageEvent(String message) {
        this.message = message;
    }
}
```

Step 2:准备 subscribers




```
@Subscribe(threadMode = ThreadMode.MAIN)
public void onMessageEvent(MessageEvent event) {
Toast.makeText(getActivity(), event.message, Toast.LENGTH_SHORT).show();
}


@Subscribe
public void handleSomethingElse(SomeOtherEvent event) {
doSomethingWith(event);
}
```

Step3：注册自己和从EveBus注销

```
@Override
public void onStart() {
    super.onStart();
    EventBus.getDefault().register(this);
}
 
@Override
public void onStop() {
    EventBus.getDefault().unregister(this);
    super.onStop();
}
```

这样就可以发布Bus事件了

```
EventBus.getDefault().post(new MessageEvent("Hello everyone!"));
```




####　一，Register 流程解析

![这里写图片描述](http://img.blog.csdn.net/20170427145825426?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbzI3OTY0MjcwNw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)


###事件发布流程

![这里写图片描述](http://img.blog.csdn.net/20170427145854850?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbzI3OTY0MjcwNw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)


####　5.EvenBus 如何实现线程调度的


```
 private void postToSubscription(Subscription subscription, Object event, boolean isMainThread) {
        switch (subscription.subscriberMethod.threadMode) {
            case POSTING:
                invokeSubscriber(subscription, event);
                break;
            case MAIN:
                if (isMainThread) {
                    invokeSubscriber(subscription, event);
                } else {
                    mainThreadPoster.enqueue(subscription, event);
                }
                break;
            case BACKGROUND:
                if (isMainThread) {
                    backgroundPoster.enqueue(subscription, event);
                } else {
                    invokeSubscriber(subscription, event);
                }
                break;
            case ASYNC:
                asyncPoster.enqueue(subscription, event);
                break;
            default:
                throw new IllegalStateException("Unknown thread mode: " + subscription.subscriberMethod.threadMode);
        }
}
```


主要参与调度的类：

```
private final HandlerPoster mainThreadPoster;
private final BackgroundPoster backgroundPoster;
private final AsyncPoster asyncPoster;
private final SubscriberMethodFinder subscriberMethodFinder;
private final ExecutorService executorService;
```

####　6. 优化之EventBusIndex

到这里，会发现有个很大的疑问：SubscriberMethod信息是怎么生成的？答案很肯定的嘛，就是加了注解@subscribe的方法嘛。我们可以通过在运行时通过采用反射的方法，获取相应添加了注解的方法，再封装成为SubscriberMethod。而对我们开发者来说，使用反射带来的性能消耗，不由得我们不慎重一二的。 在3.0的版本，EventBus加入了annotation-processor处理的逻辑，有个Subscriber Index的介绍，主要是通过annotation-processor在编译期根据注解直接生成相应的信息，来避免在运行时通过反射来获取。使用方法如下：

```
defaultConfig {
...
...
 jackOptions {
            enabled true  //支持java 8
        }
        javaCompileOptions {
            annotationProcessorOptions {
                arguments = [eventBusIndex: 'org.greenrobot.eventbusperf.MyEventBusIndex']
            }
        }
...
...
}
```

配置Index的调用如下：

EventBus eventBus = EventBus.builder().addIndex(new MyEventBusIndex()).build();
之后，我们在执行了代码的编译之后，会生成一个包名为com.example.myapp的类MyEventBusIndex。这个类会实现一个如下的接口：
public interface SubscriberInfoIndex {
    SubscriberInfo getSubscriberInfo(Class<?> subscriberClass);
}
从接口中可以看出，这个Index类只提供了一个方法getSubsriberInfo，这个方法需要我们传入订阅者所在的class类，然后获得一个SubscriberInfo的类，其类结构如下：

```
public interface SubscriberInfo {
    Class<?> getSubscriberClass();

    SubscriberMethod[] getSubscriberMethods();

    SubscriberInfo getSuperSubscriberInfo();

    boolean shouldCheckSuperclass();
}
```

从接口中暴露的方法，即可获取所需的所有SubscriberMethod信息。而这只是一个获取单个类的方法，而apt生成的EventBusIndex中，会将所有的这些class及SubscriberInfo保存在静态变量hashMap结构，这样就达到了避免运行期反射获取生成订阅方法的性能问题。而变成另外一个流程：订阅类在注册的时候，直接通过HashMap中的订阅类，获取到SubscriberInfo，进而获取到所有的SubscerberMethod，并封装为Subscription，被添加到事件总线中。这样，在引入了apt之后，EventBus的性能问题就不需要我们担心了。（PS：在EventBus作者的博客也提及到了这一点，使用apt了的EventBus在性能表现这方便，犹如打鸡血一般。）



－－－－－

Demo 地址： http://pan.baidu.com/s/1hrWCsD2

>引用：
>Java注解处理器使用详解：http://www.codeceo.com/article/java-annotation-processor.html

>EvenBus 官网 ：http://greenrobot.org/eventbus/changelog/#V300_2016-02-04_Annotations

####　Evenbus 扩展


  >注解处理器使用 ：http://www.codeceo.com/article/java-annotation-processor.html

>  oracle Annotations basics:http://docs.oracle.com/javase/tutorial/java/annotations/index.html
  
  >码农网：http://www.codeceo.com/article/tag/android

  >   Android注解使用之注解编译android-apt如何切换到annotationProcessor: 
  >   http://www.cnblogs.com/whoislcj/p/6148410.html

   

  
   >原作者blog：https://www.race604.com/
