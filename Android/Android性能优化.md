### Android 性能优化

> 性能优化方法包含布局优化、绘制优化、内存泄露优化、响应速度优化、ListView优化、Bitmap优化、线程优化以及一些性能优化建议。

#### 布局优化

- 尽量减少布局文件的层级。
- 尽量只用FrameLayout和LinearLayout等简单高效的ViewGroup。
- 在无法实现产品效果的情况下，与其使用RelativeLayout等复杂ViewGroup，不要使用嵌套。
- 使用< include >，< merge >，ViewStub。

##### < merge >

> < merge >标签一般和< include >标签一起配合只用从而减少布局的层级。例如外层布局A是LinearLayout，include进来的布局B也是LinearLayout，那么显然被包含的布局文件中的LinearLayout是多余的。通过< merge >标签就可以去掉多余的那一层LinearLayout。例子如下：

    layout A:
    <LinearLayout>
        <include layout="@layout/B"> 
    </LinearLayout>

    layout B:
    <merge>
        <Button>
        <ImageView>
    </merge>

##### ViewStub

> ViewStub继承自View，它非常轻量级，且宽高都是0，因此不参与任何的布局和绘制过程。
> ViewStub的意义在于按需加载所需的布局文件。在实际的开发中，很多布局界面在正常情况下不会显示。比如网络异常时的界面。这个时候就没有必要在整个界面初始化的时候将其加载进来。通过ViewStub就可以做到在使用的时候再加载，提高了程序初始化的性能。

    <ViewStub
        android:id="@+id/stub_import"
        androod:inflatedId="@+id/panel_import"
        android:layout="@layout/layout_network_error"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_gravity="bottom" />

> 其中stub_import是ViewStub的id，而panel_import是layout/layout_network_error这个布局的根元素的id。可以使用一下两种方式进行加载：

    ((ViewStub) findViewById(R.id.stub_import)).setVisiblity(View.VISBILE);
    View importPanel = ((ViewStub) findViewById(R.id.stub_import).inflate());

> 通过setVisiblity和inflate加载进来后，ViewStub就会被他的内部布局替换掉。

#### 绘制优化

> View的onDraw方法要避免执行大量的操作

- onDraw中不要创建新的局部对象。
- onDraw中不要做耗时的任务。View的绘制帧率为60fps最佳。所以每帧的绘制时间不要超过16ms（1000 / 60）。

#### 内存泄露优化

> 内存泄露优化分为两个方面，一方面是在开发过程中避免写出有内存泄露的代码，另一方面是通过一些分析工具比如MAT来找出潜在的内存泄露继而解决。

##### 常见内存泄露

几个常见的内存泄露情况如下：

- 静态变量导致的内存泄露。静态变量持有Activity等对象导致Activity对象无法销毁。
- 单例模式导致的内存泄露。Activity等对象被单例模式所持有，而单例模式的特点是生命周期和Application保持一致。导致Activity对象无法被释放。
- 属性动画中无限循环的动画没有在onDestroy()中停止。动画会一直播放下去。尽管在界面上已经无法看到动画效果了。这个时候Activity的View被动画持有，而View又持有了Activity，最终导致Activity无法释放。

##### 通过工具分析内存泄露

通过工具MAT分析内存泄露。
在DDMS中生成hprof文件，转换之后在MAT工具中打开，查找分析某些尺寸特别大的对象。然后查找该对象的引用队列，定位可能的内存泄露。

#### 响应速度优化和ANR日志分析

> 响应速度优化的核心思想就是避免在主线程中做耗时的操作。
> Android规定，Activity在5秒钟，BroadcastReceiver在10秒钟之内没有响应或者没有执行完操作就会出现ANR。
> ANR出现之后，系统会在/data/anr 目录下面创建一个traces.txt文件，通过分析这个文件里面的日志信息可以定位出ANR的原因。log日志信息包括耗时操作具体语句的定位，线程信息等。

#### ListView优化

- 采用ViewHolder并且避免在getView中执行耗时的操作。
- 根据列表的滑动状态来控制任务的执行频率。如当列表快速滑动的时候不要开启大量异步任务。
- 开启硬件加速来使ListView的滑动更加流畅。
- GridView的优化策略和ListView一直。

todo详细

#### Bitmap优化

> 主要通过BitmapFactory.Options来根据需要多图片进行采集。采集过程中主要用到了BitmapFactory.Options的inSampleSize参数。

todo

#### 线程优化

> 线程优化的思想史采用线程池，避免程序中存在大量的线程、野线程。线程池可以重用内部的线程，从而避免了线程的创建和销毁所带来的性能开销。
> 线程池还能有效的控制线程池的最大并发数，避免大量的线程因互相抢占系统资源从而导致阻塞现象的发生。

todo

#### 一些性能优化建议

- 避免创建过多的对象。
- 不要过多使用枚举，枚举占用的内存空间要比整型大。
- 常量使用static final来修饰。
- 使用一些Android特有的数据结构，如SparseArray和Pair等，他没有更好的性能。
- 适当使用软引用和弱引用。
- 采用内存缓存和磁盘缓存。
- 尽量采用静态内部类，这样可以避免潜在的由于内部类而导致的内存泄露。

### 内存泄露

详见上面的章节。

### 可维护性与可扩展性

- 代码风格
    + 命名规范：Android源码的命名方式，私有成员以m开头，静态成员以s开头，常量则全部用大写字母表示
    + 代码排版
    + 注释
- 层次性和单一职责原则
- 面向扩展编程
- 设计模式：常用的单例模式、工厂模式、观察者模式。《Android源码设计模式解析与实战》

