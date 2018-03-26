### Android 面试知识点总结

> 网上总结的面试题中，以及面试中高频率被提到的知识点总结。偏中高级Android开发工程师。
> 整理素材来源：
> [Android 高级面试题及答案](http://www.cnblogs.com/deman/p/5860976.html#_label0)
> 《Android开发艺术探索》部分章节整理。
> 网上其他各种面试题库


### 大知识点1：

---

#### Android系统与框架

> 参考文章：
> [Android系统介绍与框架](http://blog.csdn.net/byxdaz/article/details/9457371)
> [Android框架](http://www.cnblogs.com/forlina/archive/2011/06/29/2093332.html)

##### Android 是什么
Android是Google开发的一款开源移动OS，基于Linux内核设计，使用Google自己开发的Dalvik Java虚拟机。
##### Android有几大优势：

    - 开放性
    - 厂商支持
    - Dilvik虚拟机
    - 多元化
    - 应用程序间的无界限
    - 紧密结合Google应用

##### Android系统架构
Android系统架构为四层架构，如图所示从上至下分别是：

![Android系统框架图](http://img.blog.csdn.net/20130724232645984?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYnl4ZGF6/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast "Android系统框架图")

    - 应用程序层(Application)
    - 应用程序框架层(Application Framework)
    - 系统运行库层(Libraries)
    - Linux内核层(Linux kernal)

1）应用程序层

    Android平台不仅仅是操作系统，也包含了许多应用程序，并且这些程序是可以被开发人员开发的其他程序所替代的。

2）应用程序框架层

    是Android开发的基础，很多核心应用也是通过这一层来实现其核心功能的，该层简化了组件的重用，开发人员可以直接使用其提供的组件来进行快速的程序开发，也可以通过继承来实现个性化的扩展。
    - Activity Manager（活动管理器）管理各个应用程序的生命周期以及通常的导航回退功能。
    - Window Manager（窗口管理器）管理所有的窗口程序。
    - Content Provider（内容提供器）使得不同应用程序之间存取或者分享数据。
    - View System（视图系统）构建应用程序的基本组件。
    - Notification Manager（通知管理器）使得应用程序可以在状态栏中显示自定义的提示消息。
    - Package Manager（包管理器）Android系统内的程序管理。
    - Telephony Manager（电话管理器）管理所有的移动设备功能。
    - Resource Manager（资源管理器）提供应用程序使用的各种非代码资源，如本地化字符串、图片、布局文件、颜色文件等。
    - Location Manager（位置管理器）提供位置服务。
    - XMPP Service（XMPP服务）提供Google Talk服务。

3）系统运行库层
    
    - 系统库
    - Android Runtime

4）Linux内核层

---

#### Application类与Android 应用程序生命周期

> 参考文章：
> [【Android 应用开发】 Application 使用分析](http://blog.csdn.net/shulianghan/article/details/40737419) 详细好文，非常全面！
> [Android Application详解和使用](http://blog.csdn.net/eagle1054/article/details/51839167) 好文。清晰。
> [[译]玩转Android Application的生命周期(不，不许覆盖那个Home键)](http://blog.csdn.net/qq284565035/article/details/51811590) | [原文](http://www.developerphil.com/no-you-can-not-override-the-home-button-but-you-dont-have-to/)
> [android Application类的详细介绍](http://blog.csdn.net/pi9nc/article/details/11200969)
> [Application类官方文档](https://developer.android.google.cn/reference/android/app/Application.html#onTrimMemory(int))

##### Application是什么
Application类一个可以用来保存全局变量的基本类。可以自己创建一个类继承Application并且在AndroidManifest里面注册即可。Application和Activity,Service一样,是android框架的一个系统组件，当android程序启动时系统会创建一个application对象。Application可以说是单例(singleton)模式的一个类。且application对象的生命周期是整个程序中最长的，它的生命周期就等于这个程序的生命周期。调用Context的getApplicationContext或者Activity的getApplication方法来获得一个application对象。

Application中没有onStart(),onStop()方法。但是有onCreate(),onTerminate()方法。
在4.0(API LEVEL 14)中还加入了onTrimMemory()方法。

##### Application.registerActivityLifecycleCallbacks() 与 ActivityLifecycleCallbacks

Application通过此接口提供了一套回调方法，用于让开发者对Activity的生命周期事件进行集中处理。不需要再每一个Activity的生命周期的回调函数中写多次。具体参考：[Android开发 - ActivityLifecycleCallbacks使用方法初探](http://blog.csdn.net/tongcpp/article/details/40344871)

##### Application能干什么
> 保存全局应用状态的信息，而且是单例模式，生命周期贯穿整个应用的运行。基于这一点，我们可以利用它的全局单例，保存一些系统运行时的状态信息，和数据操作的动作。例如：

- 用户登录信息保存
- Activity之间数据的传递，在Application中维护一个Map
- 保存未捕获崩溃日志到文件。UncaughtExceptionHandler类里面的uncaughtException(Thread thread, Throwable ex);方法。
- 监听Activity生命周期。ActivityLifecycleCallbacks


---

#### Activity Launch Mode；

> 一共有四种启动模式：Standard, SingleTop, SingleTask, SingleInstance.

- Standard:标准模式。
    + 默认模式
- SingleTop:栈顶复用模式。
    + 在栈顶如果已经存在要启动的activity，则不会创建新的实例，而是调用onNewIntent()方法。如果栈内已经存在要启动的activity，但不是在栈顶，则会重新创建实例并压如栈顶。在onNewIntent()方法中可以获取当前intent请求的信息。
- SingleTask:栈内复用模式
    + 当一个具有singleTask模式的activity请求启动后，系统首先会寻找是否存在该activity想要的任务栈。
    + activity想要的任务栈是通过TaskAffinity参数指定的（既任务栈的名字，每个任务栈都有名字）。
    + 如果不存在，则创建一个指定名字的任务栈，然后创建activity的实例并压入刚才创建的指定的栈中。
    + 如果已经存在activity所需要的任务栈，则检查栈中是否有activity的实例存在。如果已经有实例，则把实例调到栈顶，同时调用它的onNewIntent()方法。调到栈顶的方法就是，如果该activity之上有其他activity，则其他的activity全部出栈。没有该activity实例，就创建一个放入栈顶。
    + 在这种模式下，该activity只在一个栈中存在，多次调用都只会存在一个栈中，存在一个实例。因为栈不能重复，在栈中又只有一个实例。
- SingleInstance:单实例模式
    + 除了具有SingleTask模式的所有特性以外，还有一点就是具有此模式的activity只能单独的位于一个任务栈中。
    + SingleTask模式下的activity所在的任务栈可以有其他activity的存在，而singleInstance模式下的activity所在的任务栈只有它一个。
    + 只有一个实例，并且这个实例独立运行在一个activity任务栈中，这个task只有这个实例，不允许有别的Activity存在
    + 参考文档：http://blog.csdn.net/su20145104009/article/details/50662731

---

#### AIDL：熟悉AIDL，理解其工作原理，懂transact和onTransact的区别；

> 参考文档：
> [Android：学习AIDL，这一篇文章就够了(上)](http://blog.csdn.net/luoyanglizi/article/details/51980630)
> [Android：学习AIDL，这一篇文章就够了(下)](http://blog.csdn.net/luoyanglizi/article/details/52029091)

- AIDL（android interface definition language）接口描述语言。
- 是一种语言。
- 为了实现进程间通信，尤其是涉及多进程并发情况下的进程间通信。
- 语法基本和java一样，但是有如下几个区别
    + aidl文件后缀是.aidl不是.java。
    + 支持的数据类型
        * Java中的八种基本数据类型，包括byte，short，int，long，float，double，boolean，char。
        * String 类型。
        * CharSequence类型。
        * List类型：List中的所有元素必须是AIDL支持的类型之一，或者是一个其他AIDL生成的接口，或者是定义的parcelable（下文关于这个会有详解）。List可以使用泛型。
        * Map类型：Map中的所有元素必须是AIDL支持的类型之一，或者是一个其他AIDL生成的接口，或者是定义的parcelable。Map是不支持泛型的。
    + 就算在同一个包中也要导入（import）
    + 定向tag（参考博客）
    + 两种aidl文件（参考博客）
- 事实上，就算我们不写AIDL文件，直接按照它生成的.java文件那样写一个.java文件出来，在服务端和客户端中也可以照常使用这个.java类来进行跨进程通信。所以说AIDL语言只是在简化我们写这个.java文件的工作而已，而要研究AIDL是如何帮助我们进行跨进程通信的，其实就是研究这个生成的.java文件是如何工作的。
- 完成AIDL中非默认支持数据类型的序列化。

---
#### Binder：从Java层大概理解Binder的工作原理，懂Parcel对象的使用；

[Binder学习概要](https://www.jianshu.com/p/a50d3f2733d6)

---
#### 多进程：熟练掌握多进程的运行机制，IPC、懂Messenger、Socket等；

IPC的几种方式
- 利用intent传递bundle。
- 使用文件共享（有局限性，比如并发读写）
    + SharePreferences底层实现上采用XML文件来存储键值对。XML在每一个应用的文件目录上。高并发读写数据丢失几率大，不建议IPC使用它。
- AIDL
- Messenger
- ContentProvider
- Socket
    + 监听端口建立连接，然后就跟访问文件一样读取流。

---
#### 事件分发：弹性滑动、滑动冲突等； 

参考文档

-  [Android事件分发机制完全解析，带你从源码的角度彻底理解(上)](http://blog.csdn.net/guolin_blog/article/details/9097463)
-  [Android事件分发机制完全解析，带你从源码的角度彻底理解(下)](http://blog.csdn.net/guolin_blog/article/details/9153747)
-  [Android事件传递之子View和父View的那点事](http://www.jianshu.com/p/7ff768a77410)
-  [图解Android事件传递之View篇](http://remcarpediem.com/2016/02/04/%E5%9B%BE%E8%A7%A3Android%E4%BA%8B%E4%BB%B6%E4%BC%A0%E9%80%92%E4%B9%8BView%E7%AF%87/)
    +  非常详细完整的关于view内部事件传递所涉及的几个主要函数的流程图。
-  [图解Android事件传递之ViewGroup篇](http://remcarpediem.com/2016/02/11/%E5%9B%BE%E8%A7%A3Android%E4%BA%8B%E4%BB%B6%E4%BC%A0%E9%80%92%E4%B9%8BViewGroup%E7%AF%87/)
    +  非常详细完整的关于ViewGroup内部事件传递所涉及的几个主要函数的流程图。

---
#### 玩转View：View的绘制原理、各种自定义View； 
#### Android消息机制

    + [Android消息处理机制(Handler、Looper、MessageQueue与Message)详解(详细全面的好文)](http://www.cnblogs.com/angeldevil/p/3340644.html)
    + [Android 中线程间通信原理分析：Looper, MessageQueue, Handler](https://segmentfault.com/a/1190000006171396)


#### 动画系列：熟悉View动画和属性动画的不同点，懂属性动画的工作原理； 
#### 懂性能优化、熟悉mat等工具 
#### 懂点常见的设计模式
#### 线程安全相关知识，什么是线程安全？
#### Android网络编程
#### 热部署、热修复、插件化开发
#### 常见数据结构，常见算法，算法的时间复杂度，设计模式

---
#### Socket、Http网络编程
- socket [Android学习笔记49：Socket编程实现简易聊天室](http://www.cnblogs.com/menlsh/archive/2013/06/12/3133296.html)
- socket [读懂Java中的Socket编程](http://droidyue.com/blog/2015/03/08/sockets-programming-in-java/)
- http [Android操作HTTP实现与服务器通信](http://www.cnblogs.com/hanyonglu/archive/2012/02/19/2357842.html)
    + HttpUrlConnection
    + HttpClient(API23,6.0中废弃)


---
#### Http/Https网络编程。TCP/IP协议

---
#### Android过度绘制优化

参考文章：
> [在Android Studio下使用Hierarchy Viewer](http://oyjt.github.io/2016/04/18/%E5%9C%A8Android%20Studio%E4%B8%8B%E4%BD%BF%E7%94%A8Hierarchy%20Viewer/?utm_source=tuicool&utm_medium=referral)
> [Android UI性能优化详解](http://mrpeak.cn/android/2016/01/11/android-performance-ui) (详细全面的好文)
> [Android过度绘制优化心得](http://blog.csdn.net/moyameizan/article/details/47807327)

---

#### 热门，最新，牛逼的，常见的库的了解，总结，试验，使用等全方面的熟悉掌握。
    + [2016 Top 10 Android Library](https://zhuanlan.zhihu.com/p/24920487)

---

#### 美团点评技术团队
> [网站](http://tech.meituan.com/)
> 各种技术牛文，多看看

#### 分析自己和优秀的开发工程师的差距在哪里。不是知识点掌握上的差距，是方式方法做事上的差距
#### 去GitHub等地方找一个Android Util库。（如文件操作库啥的）


大块知识点2：
------

- 如何对Android应用进行性能分析？
- Android应用性能优化。
- Android应用从几个方面优化。
- Android布局优化
- 什么情况会导致内存泄露？（内存泄露的常见例子）
- 如何避免内存泄露？（内存泄露的优化）（如何避免OOM）（我们应该如何合理使用内存？）
- ANR是什么？
- ANR发成的原因和常见例子。
- 如何避免和解决ANR？
- Android线程间通信有哪几种方式？
- 子线程发消息到主线程进行更新 UI，除了 handler 和 AsyncTask，还有什么？
- 子线程中能不能 new handler？为什么？
- Android进程间通信有什么方式？（介绍一下Android IPC机制）
- 介绍一下Handler机制。
- 介绍一下事件分发机制
- 什么是 AIDL 以及如何使用
- Android 屏幕适配问题。（如何进行屏幕适配？）（Android屏幕适配的方法有哪些？）
- Android 中的动画有哪几类，它们的特点和区别是什么？
- 开发中都使用过哪些框架、平台。介绍一下你常用的框架。[15 个 Android 通用流行框架大全](https://www.oschina.net/news/73836/15-android-general-popular-frameworks)


细节知识点：

- Android 中如何捕获未捕获的异常
- Devik 进程，linux 进程，线程的区别
- 描述一下 android 的系统架构
- android 应用对内存是如何限制的?
- 请解释下 Android 程序运行时权限与文件系统权限的区别
- Framework 工作方式及原理，Activity 是如何生成一个 view 的，机制是什么
- 如何修改 Activity 进入和退出动画
- SurfaceView & View 的区别
- 使用过那些自定义View
- 强引用、弱引用、软引用
- 初步了解react native
- RecyclerView
- MVP,MVC,MVVM架构之间的区别
    + [MVC与MVP架构特点与区别-android](http://blog.csdn.net/shareus/article/details/51481308)
    + MVC\MVP的主要区别：所以两者的主要区别是，MVP中View不能直接访问Model，需要通过Presenter发出请求，View与Model不能直接通信。

### 答案

- 如何对Android应用进行性能分析？

> android性能主要有响应速度和UI刷新速度。关键流程分几个步骤
> 测评：对系统进行大量有针对性的测试，以得到合适的测试数据。
> 分析系统瓶颈：分析测试数据，找到其中的hotspot（热点，即bottleneck）。
> 性能优化：对hotspot相关的代码进行优化。
> 通过测试工具去寻找应用的性能瓶颈、分析性能。
> Traceview是Android平台特有的数据采集和分析工具，androidsdk自带的工具，它主要用于分析Android中应用程序的hotspot。TraceView工具的具体使用方法细节参考[Android系统性能调优工具介绍](http://www.cnblogs.com/wanqieddy/p/4494896.html)。这篇文章详细介绍了TraceView的使用方法。这里就不总结了。






