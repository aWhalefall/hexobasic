---
title: Android Debug Database
date: 2017-04-26 22:23:21
categories: others
tags: android
---

### 前言
Android客户端查看sqlite数据库是很繁琐的事情，需要DDMS中找到sqlite数据库，导出来到桌面，使用的其它数据库软件查看。当然可以root之后在手机上看。Android Debug Database是一个studio 插件，方便在浏览器中查看。解决了痛点

![这里写图片描述](http://img.blog.csdn.net/20170401170016577?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbzI3OTY0MjcwNw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

### 定义：

Android Debug Database is a powerful library for debugging databases and shared preferences in Android applications.
一个可以在浏览器中查看的客户端数据库以及Sp文件的插件。

<!--more-->
### 使用方式一

 1.直接在app-->build.gradle 中引入依赖jar

debugCompile 'com.amitshekhar.android:debug-db:1.0.0'

  2.build程序

  我这边build时候出现问题

UNEXPECTED TOP-LEVEL EXCEPTION: com.android.dex.DexException: Multiple dex files define Lcom/google/gson/JsonSerializer;

Gson mutidex冲突，原因就是项目中使用的jar和 debug-db 用的Gson 重复导致
 compile 'com.google.code.gson:gson:2.8.0'

解决方式，移除app->libs中的jar 保留Modul中的 Gson：2.8.0 重新Build 项目，就可以了，当然有其他解决方式，这里只给出一个方式
运行

04-01 16:11:23.894 31783-31783/D/DebugDB: Open http://172.27.35.14:8080 in your browser

使用浏览器， 输入 http://172.27.35.14:8080 就可以查看了

### 使用方式二

1.使用debug-db库，目的就是可以看源代码学习一下，app-->import-->inportModul-找到具体的debug-db位置导入就可以了
2.构建通过运行



查看具体运行界面

![这里写图片描述](https://raw.githubusercontent.com/amitshekhariitbhu/Android-Debug-Database/master/assets/debugdb.png)

左边可以看到数据库列表，右边展示数据库中数据表

### Editing values


![这里写图片描述](https://raw.githubusercontent.com/amitshekhariitbhu/Android-Debug-Database/master/assets/debugdb_edit.png)


Android调试数据库允许您以非常简单的方式直接在浏览器中查看数据库和共享首选项。

Android调试数据库可以做什么？

查看所有数据库。

查看应用程序中使用的共享首选项中的所有数据。

在给定的数据库上运行任何sql查询来更新和删除您的数据。

直接编辑数据库值。

直接编辑共享首选项。

删除数据库行和共享首选项。

搜索您的数据

数据排序

下载数据库。

所有这些功能都可以在不影响设备的情况下工作 - >不需要root设备


看了这些还不心动？？？？ 赶快试试吧




>引用： 

>debug-db   https://github.com/amitshekhariitbhu/Android-Debug-Database
>sqlite 数据库语法   http://www.runoob.com/sqlite/sqlite-syntax.html
>