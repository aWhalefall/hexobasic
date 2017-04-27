---
title: androd SVG 矢量动画
date: 2017-04-25 22:03:41
categories: 编程
tags: android
---

#### 前言
学习android动画中发现svg矢量动画效果还不错。W3c中有完整的介绍。跟着api进行学习
#### 什么是SVG？
[进入w3c](http://www.w3school.com.cn/svg/svg_reference.asp) 

```
SVG 指可伸缩矢量图形 (Scalable Vector Graphics)
SVG 用来定义用于网络的基于矢量的图形
SVG 使用 XML 格式定义图形
SVG 图像在放大或改变尺寸的情况下其图形质量不会有所损失
SVG 是万维网联盟的标准
SVG 与诸如 DOM 和 XSL 之类的 W3C 标准是一个整体
```
<!--more-->

#### SVG 的历史和优势
在 2003 年一月，SVG 1.1 被确立为 W3C 标准。
参与定义 SVG 的组织有：太阳微系统、Adobe、苹果公司、IBM 以及柯达。
与其他图像格式相比，使用 SVG 的优势在于：

```
SVG 可被非常多的工具读取和修改（比如记事本）
SVG 与 JPEG 和 GIF 图像比起来，尺寸更小，且可压缩性更强。
SVG 是可伸缩的
SVG 图像可在任何的分辨率下被高质量地打印
SVG 可在图像质量不下降的情况下被放大
SVG 图像中的文本是可选的，同时也是可搜索的（很适合制作地图）
SVG 可以与 Java 技术一起运行
SVG 是开放的标准
SVG 文件是纯粹的 XML
SVG 的主要竞争者是 Flash。
```


与 Flash 相比，SVG 最大的优势是与其他标准（比如 XSL 和 DOM）相兼容。而 Flash 则是未开源的私有技术。
#### Svg 格式

```
<?xml version="1.0" standalone="no"?>
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" 
"http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">

<svg width="100%" height="100%" version="1.1"
xmlns="http://www.w3.org/2000/svg">

<circle cx="100" cy="50" r="40" stroke="black"
stroke-width="2" fill="red"/>

</svg>

Svg 标示符号 <svg>,</svg>
<circle cx="100" cy="50" r="40" stroke="black"
stroke-width="2" fill="red"/>
```

#### 简单的动画控制


这里我们只讨论客户端的实现方案，svg最主要的是**Svg Path** 路径点。也就是动画点。


效果：
![这里写图片描述](http://img.blog.csdn.net/20170105162703940?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfMjcyNTg3OTk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)


### 1.path 是怎么来？


1.使用具体的工具去画矢量图, PS也可以，生成路径--导出路径留待使用

2.使用图片最背景选取具体的绘制路径的点，并生成路径，推荐GIMP （ps也同样可以）



这里使用GIMP 进行举例子，photoshop 需要下载svg插件。


![这里写图片描述](http://img.blog.csdn.net/20170411173825608?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbzI3OTY0MjcwNw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

![这里写图片描述](http://img.blog.csdn.net/20170411173905485?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbzI3OTY0MjcwNw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

![这里写图片描述](http://img.blog.csdn.net/20170411173914812?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbzI3OTY0MjcwNw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)


#### 引出第三个问题 SVG 路径转换为Android，w3c给出了定义

SVG路径

```
<?xml version="1.0" encoding="UTF-8" standalone="no"?>
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 20010904//EN"
              "http://www.w3.org/TR/2001/REC-SVG-20010904/DTD/svg10.dtd">

<svg xmlns="http://www.w3.org/2000/svg"
     width="3.02778in" height="3.16667in"
     viewBox="0 0 218 228">
  <path id="选区"
        fill="none" stroke="black" stroke-width="1"
        d="M 218.00,0.00
           C 218.00,0.00 218.00,228.00 218.00,228.00
             218.00,228.00 0.00,228.00 0.00,228.00
             0.00,228.00 0.00,0.00 0.00,0.00
             0.00,0.00 218.00,0.00 218.00,0.00 Z
           M 85.00,49.00
           C 74.10,43.40 58.80,29.70 48.00,27.00
             47.37,32.11 49.50,33.50 53.00,36.99
             56.74,40.70 69.43,51.00 70.91,55.00
             72.37,58.95 66.69,71.89 61.00,73.75
             58.27,74.64 54.63,72.09 52.00,71.00
             ...
             ...
             ...
            C 171.48,178.95 171.13,180.88 171.76,183.00
             172.25,184.63 173.11,185.64 174.00,187.00
             171.36,190.02 171.47,190.34 173.00,194.00
             162.60,203.60 182.46,205.63 188.77,200.99
             193.07,197.84 192.64,192.04 186.98,190.14
             185.36,189.97 182.72,190.01 181.00,190.14
             181.00,190.14 177.00,190.14 177.00,190.14
             177.00,190.14 177.00,188.00 177.00,188.00
             184.57,187.98 190.42,187.53 189.00,178.00
             189.00,178.00 192.00,175.00 192.00,175.00
             185.59,174.69 177.39,170.44 172.74,177.13 Z" />
</svg>
```


#### 2.SVG 路径转换为Android中能被Paint draw的点
格式：

```
M = moveto， 相当于android 里的 moveTo()

L = lineto， 相当于lineTo()进行画直线

H = horizontal lineto， 画水平直线

V = vertical lineto， 画竖直直线

C = curveto， 相当于android 里的 cubicTo()

S = smooth curveto

Q = quadratic Belzier curve

T = smooth quadratic Belzier curveto

A = elliptical Arc

Z = closepath
```

在这里需要将svg文件转换为能别Paint画笔处理具体值，这里使用网上给出的一个封装好的工具类进行解析path处理

```
package com.gaok.ui.svgtrace.utils;

import android.graphics.Path;
import android.graphics.PointF;

public class SvgPathParser {

    private static final int TOKEN_ABSOLUTE_COMMAND = 1;
    private static final int TOKEN_RELATIVE_COMMAND = 2;
    // 具体的数值或".""-"
    private static final int TOKEN_VALUE = 3;
    private static final int TOKEN_EOF = 4;

    private int mCurrentToken;
    private PointF mCurrentPoint = new PointF();
    private int mLength;
    private int mIndex;
    // 要解析的path 字符串
    private String mPathString;
    private float scale = 4f;
    private float xMin;
    private float xMax;
    private float yMin;
    private float yMax;

    protected float transformX(float x) {
        return x * scale;
    }

    protected float transformY(float y) {
        return y * scale;
    }

    public Path parsePath(String s) {
        try {
            mCurrentPoint.set(Float.NaN, Float.NaN);
            mPathString = s;
            mIndex = 0;
            mLength = mPathString.length();

            PointF tempPoint1 = new PointF();
            PointF tempPoint2 = new PointF();
            PointF tempPoint3 = new PointF();

            Path p = new Path();
            p.setFillType(Path.FillType.WINDING);

            boolean firstMove = true;
            while (mIndex < mLength) {
                char command = consumeCommand();
                boolean relative = (mCurrentToken == TOKEN_RELATIVE_COMMAND);
                switch (command) {
                    case 'M':
                    case 'm': {
                        // m指令，相当于android 里的 moveTo()
                        boolean firstPoint = true;
                        while (advanceToNextToken() == TOKEN_VALUE) {
                            consumeAndTransformPoint(tempPoint1, relative && mCurrentPoint.x != Float.NaN);
                            if (firstPoint) {
                                p.moveTo(tempPoint1.x, tempPoint1.y);
                                firstPoint = false;
                                if (firstMove) {
                                    mCurrentPoint.set(tempPoint1);
                                    firstMove = false;
                                }
                            } else {
                                p.lineTo(tempPoint1.x, tempPoint1.y);
                            }
                        }
                        mCurrentPoint.set(tempPoint1);
                        break;
                    }

                    case 'C':
                    case 'c': {
                        // c指令，相当于android 里的 cubicTo()
                        if (mCurrentPoint.x == Float.NaN) {
                            throw new Exception("Relative commands require current point");
                        }

                        while (advanceToNextToken() == TOKEN_VALUE) {
                            consumeAndTransformPoint(tempPoint1, relative);
                            consumeAndTransformPoint(tempPoint2, relative);
                            consumeAndTransformPoint(tempPoint3, relative);
                            p.cubicTo(tempPoint1.x, tempPoint1.y, tempPoint2.x, tempPoint2.y, tempPoint3.x, tempPoint3.y);
                        }
                        mCurrentPoint.set(tempPoint3);
                        break;
                    }

                    case 'L':
                    case 'l': {
                        // 相当于lineTo()进行画直线
                        if (mCurrentPoint.x == Float.NaN) {
                            throw new Exception("Relative commands require current point");
                        }

                        while (advanceToNextToken() == TOKEN_VALUE) {
                            consumeAndTransformPoint(tempPoint1, relative);
                            p.lineTo(tempPoint1.x, tempPoint1.y);
                        }
                        mCurrentPoint.set(tempPoint1);
                        break;
                    }

                    case 'H':
                    case 'h': {
                        // 画水平直线
                        if (mCurrentPoint.x == Float.NaN) {
                            throw new Exception("Relative commands require current point");
                        }

                        while (advanceToNextToken() == TOKEN_VALUE) {
                            float x = transformX(consumeValue());
                            if (relative) {
                                x += mCurrentPoint.x;
                            }
                            p.lineTo(x, mCurrentPoint.y);
                        }
                        mCurrentPoint.set(tempPoint1);
                        break;
                    }

                    case 'V':
                    case 'v': {
                        // 画竖直直线
                        if (mCurrentPoint.x == Float.NaN) {
                            throw new Exception("Relative commands require current point");
                        }

                        while (advanceToNextToken() == TOKEN_VALUE) {
                            float y = transformY(consumeValue());
                            if (relative) {
                                y += mCurrentPoint.y;
                            }
                            p.lineTo(mCurrentPoint.x, y);
                        }
                        mCurrentPoint.set(tempPoint1);
                        break;
                    }

                    case 'Z':
                    case 'z': {
                        // 封闭path
                        p.close();
                        break;
                    }
                }
            }

            return p;
        } catch (Exception e) {

        }
        return null;
    }

    private int advanceToNextToken() {
        while (mIndex < mLength) {
            char c = mPathString.charAt(mIndex);
            if ('a' <= c && c <= 'z') {
                return (mCurrentToken = TOKEN_RELATIVE_COMMAND);
            } else if ('A' <= c && c <= 'Z') {
                return (mCurrentToken = TOKEN_ABSOLUTE_COMMAND);
            } else if (('0' <= c && c <= '9') || c == '.' || c == '-') {
                return (mCurrentToken = TOKEN_VALUE);
            }

            ++mIndex;
        }

        return (mCurrentToken = TOKEN_EOF);
    }

    private char consumeCommand() throws Exception {
        advanceToNextToken();
        if (mCurrentToken != TOKEN_RELATIVE_COMMAND && mCurrentToken != TOKEN_ABSOLUTE_COMMAND) {
            throw new Exception("Expected command");
        }

        return mPathString.charAt(mIndex++);
    }

    private void consumeAndTransformPoint(PointF out, boolean relative) throws Exception {
        float xValue = consumeValue();
        out.x = transformX(xValue);
        if (out.x < xMin) {
            xMin = out.x;
        } else if (out.x > xMax) {
            xMax = out.x;
        }
        float yValue = consumeValue();
        out.y = transformY(yValue);
        if (out.y < yMin) {
            yMin = out.y;
        } else if (out.y > yMax) {
            yMax = out.y;
        }
        if (relative) {
            out.x += mCurrentPoint.x;
            out.y += mCurrentPoint.y;
        }
    }

    private float consumeValue() throws Exception {
        advanceToNextToken();
        if (mCurrentToken != TOKEN_VALUE) {
            throw new Exception("Expected value");
        }

        boolean start = true;
        boolean seenDot = false;
        int index = mIndex;
        while (index < mLength) {
            char c = mPathString.charAt(index);
            if (!('0' <= c && c <= '9') && (c != '.' || seenDot) && (c != '-' || !start)) {
                break;
            }
            if (c == '.') {
                seenDot = true;
            }
            start = false;
            ++index;
        }

        if (index == mIndex) {
            throw new Exception("Expected value");
        }

        String str = mPathString.substring(mIndex, index);
        try {
            float value = Float.parseFloat(str);
            mIndex = index;
            return value;
        } catch (NumberFormatException e) {
            throw new Exception("Invalid float value '" + str + "'.");
        }
    }

    public float getPathWidth() {
        return xMax - xMin;
    }

    public float getPathHeight() {
        return yMax - yMin;
    }
}
```

转到studio中将路径值保存在String中留待使用。

```
<resources>
    <string name="app_name">demo</string>
    <string name="path">
     M 85.00,34.57
           C 90.35,27.78 95.91,20.00 99.32,12.00
             100.42,9.42 101.66,3.01 102.93,1.60
             ...
             ...
             ...
           M 238.00,387.00
           C 238.00,387.00 239.00,388.00 239.00,388.00
             239.00,388.00 239.00,387.00 239.00,387.00
             239.00,387.00 238.00,387.00 238.00,387.00 Z
         
    </string>
</resources>
```

万事具备只差画出来了

#### 3.如何绘制到屏幕？
在自定义view onDraw（）方法调用系统方法

```
 public void drawPath(@NonNull Path path, @NonNull Paint paint) {
        if (path.isSimplePath && path.rects != null) {
            native_drawRegion(mNativeCanvasWrapper, path.rects.mNativeRegion, paint.getNativeInstance());
        } else {
            native_drawPath(mNativeCanvasWrapper, path.readOnlyNI(), paint.getNativeInstance());
        }
}
```

```
canvas.drawPath(mSvgPath, mPaint);
```


 
至于动画效果这里不做深入，感兴趣自行阅读引用相关的实现代码。




>引用
>Svg 使用 http://blog.csdn.net/tianjian4592/article/details/44538605
>Svg  demo   https://github.com/unclepizza/SVGTraceDemo.git

