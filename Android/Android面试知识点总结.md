### Android 面试知识点总结

> 网上总结的面试题中，以及面试中高频率被提到的知识点总结。偏中高级Android开发工程师。
> 整理素材来源：
> [Android 高级面试题及答案](http://www.cnblogs.com/deman/p/5860976.html#_label0)
> 《Android开发艺术探索》部分章节整理。
> 网上其他各种面试题库

大知识点1：
------

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
- AIDL：熟悉AIDL，理解其工作原理，懂transact和onTransact的区别；
- Binder：从Java层大概理解Binder的工作原理，懂Parcel对象的使用；

---
- 多进程：熟练掌握多进程的运行机制，懂Messenger、Socket等；



---
#### 事件分发：弹性滑动、滑动冲突等； 

答案：
##### 参考文档
-  [Android事件分发机制完全解析，带你从源码的角度彻底理解(上)](http://blog.csdn.net/guolin_blog/article/details/9097463)
-  [Android事件分发机制完全解析，带你从源码的角度彻底理解(下)](http://blog.csdn.net/guolin_blog/article/details/9153747)
-  [Android事件传递之子View和父View的那点事](http://www.jianshu.com/p/7ff768a77410)
-  [图解Android事件传递之View篇](http://remcarpediem.com/2016/02/04/%E5%9B%BE%E8%A7%A3Android%E4%BA%8B%E4%BB%B6%E4%BC%A0%E9%80%92%E4%B9%8BView%E7%AF%87/)
    +  非常详细完整的关于view内部事件传递所涉及的几个主要函数的流程图。
-  [图解Android事件传递之ViewGroup篇](http://remcarpediem.com/2016/02/11/%E5%9B%BE%E8%A7%A3Android%E4%BA%8B%E4%BB%B6%E4%BC%A0%E9%80%92%E4%B9%8BViewGroup%E7%AF%87/)
    +  非常详细完整的关于ViewGroup内部事件传递所涉及的几个主要函数的流程图。

---
- 玩转View：View的绘制原理、各种自定义View； 
- 动画系列：熟悉View动画和属性动画的不同点，懂属性动画的工作原理； 
- 懂性能优化、熟悉mat等工具 
- 懂点常见的设计模式
- 线程安全相关知识，什么是线程安全？

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

### 答案

- 如何对Android应用进行性能分析？

> android性能主要有响应速度和UI刷新速度。关键流程分几个步骤
> 测评：对系统进行大量有针对性的测试，以得到合适的测试数据。
> 分析系统瓶颈：分析测试数据，找到其中的hotspot（热点，即bottleneck）。
> 性能优化：对hotspot相关的代码进行优化。
> 通过测试工具去寻找应用的性能瓶颈、分析性能。
> Traceview是Android平台特有的数据采集和分析工具，androidsdk自带的工具，它主要用于分析Android中应用程序的hotspot。TraceView工具的具体使用方法细节参考[Android系统性能调优工具介绍](http://www.cnblogs.com/wanqieddy/p/4494896.html)。这篇文章详细介绍了TraceView的使用方法。这里就不总结了。






