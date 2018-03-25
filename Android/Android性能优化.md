### Android 性能优化

> 性能优化方法包含布局优化、绘制优化、内存泄露优化、线程优化、响应速度优化、列表优化（ListView,GridView,RecycleView）、Bitmap优化以及一些性能优化建议。

#### 布局优化，绘制优化

- 尽量减少布局文件的层级。
- 尽量只用FrameLayout（帧布局）和LinearLayout等简单高效的ViewGroup。
- 在无法实现产品效果的情况下，与其使用RelativeLayout等复杂ViewGroup，不要使用嵌套。
- 使用< include >，< merge >，ViewStub。
- 防止过度绘制，使用工具Hierarchy View去检测过度绘制。Hierarchy View可以展示视图层级的树状图，可以显示每一个View的measure、layout、draw的时间。

##### < merge >

> < merge >标签一般和< include >标签一起配合使用从而减少布局的层级。例如外层布局A是LinearLayout，include进来的布局B也是LinearLayout，那么显然被包含的布局文件中的LinearLayout是多余的。通过< merge >标签就可以去掉多余的那一层LinearLayout。例子如下：

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

- onDraw中不要创建新的局部对象，因为onDraw不停的被调用，产生大量局部变量，会启动垃圾回收机制GC工作，影响性能。
- onDraw中不要做耗时的任务。View的绘制帧率为60fps最佳。所以每帧的绘制时间不要超过16ms（1000 / 60）。

#### 内存泄露优化

> 内存泄露的根本原因是长生命周期的对象持有短生命周期的对象。内存泄露优化分为两个方面，一方面是在开发过程中避免写出有内存泄露的代码，另一方面是通过一些分析工具比如MAT来找出潜在的内存泄露继而解决。

##### 常见内存泄露

几个常见的内存泄露情况如下：

- 静态变量导致的内存泄露。静态变量持有Activity等对象导致Activity对象无法销毁。
- 单例模式导致的内存泄露。Activity等对象被单例模式所持有，而单例模式的特点是生命周期和Application保持一致。导致Activity对象无法被释放。
- 属性动画中无限循环的动画没有在onDestroy()中停止。动画会一直播放下去。尽管在界面上已经无法看到动画效果了。这个时候Activity的View被动画持有，而View又持有了Activity，最终导致Activity无法释放。

内存泄露的根本原因：长生命周期的对象持有短生命周期的对象。短周期对象就无法及时释放。

    I. 静态集合类引起内存泄露

    主要是hashmap，Vector等，如果是静态集合 这些集合没有及时setnull的话，就会一直持有这些对象。

    II.remove 方法无法删除set集  Objects.hash(firstName, lastName);

    经过测试，hashcode修改后，就没有办法remove了。

    III. observer 我们在使用监听器的时候，往往是addxxxlistener，但是当我们不需要的时候，忘记removexxxlistener，就容易内存leak。

    广播没有unregisterrecevier

    IV.各种数据链接没有关闭，数据库contentprovider，io，sokect等。cursor

    V.内部类：

    java中的内部类（匿名内部类），会持有宿主类的强引用this。

    所以如果是new Thread这种，后台线程的操作，当线程没有执行结束时，activity不会被回收。

    Context的引用，当TextView 等等都会持有上下文的引用。如果有static drawable，就会导致该内存无法释放。

    VI.单例

    单例 是一个全局的静态对象，当持有某个复制的类A是，A无法被释放，内存leak。

##### 通过工具分析内存泄露

通过工具MAT分析内存泄露。
在DDMS中生成hprof文件，转换之后在MAT工具中打开，查找分析某些尺寸特别大的对象。然后查找该对象的引用队列，定位可能的内存泄露。

#### 响应速度优化和ANR日志分析

> 响应速度优化的核心思想就是避免在主线程中做耗时的操作。
> Android规定，Activity在5秒钟，BroadcastReceiver在10秒钟之内，Service在20秒内没有响应或者没有执行完操作就会出现ANR。
> ANR出现之后，系统会在/data/anr 目录下面创建一个traces.txt文件，通过分析这个文件里面的日志信息可以定位出ANR的原因。log日志信息包括耗时操作具体语句的定位，线程信息等。

#### ListView优化

- 采用ViewHolder并且避免在getView中执行耗时的操作。
- 根据列表的滑动状态来控制任务的执行频率。如当列表快速滑动的时候不要开启大量异步任务。在OnScrollListener的onScrollStateChanged方法中判断列表的滑动状态。
- 开启硬件加速来使ListView的滑动更加流畅。通过设置 android:hardwareAccelerated="true"即可为Activity开启硬件加速。
- GridView的优化策略和ListView一直。


#### Bitmap优化

> 主要通过BitmapFactory.Options来根据需要对图片进行采集。采集过程中主要用到了BitmapFactory.Options的inSampleSize参数。

todo

#### 线程优化

> 线程优化的思想是采用线程池，避免程序中存在大量的线程、野线程。线程池可以重用内部的线程，从而避免了线程的创建和销毁所带来的性能开销。
> 线程池还能有效的控制线程池的最大并发数，避免大量的线程因互相抢占系统资源从而导致阻塞现象的发生。
> 常用有四种线程池，FixedThrealPool,CachedThreadPool,ScheduledThreadPool,SingleThreadExecutor.
> ThreadPoolExecutor是线程池的真正实现，通过构造方法里面的参数配置不同的线程池。

- FixedThreadPool 只有核心线程，且数量固定，无超时时间。
- CachedThreadPool 没有核心线程，只有非核心线程，且数量无限大，有60秒超时机制。
- ScheduledThreadPool 核心线程数量固定，非核心线程数量不限制，但是非核心线程闲置后立即被回收。
- SingleThreadExecutor 有些只有一个核心线程且不超时，不需要处理线程同步问题。

#### 一些性能优化建议

- 避免创建过多的对象。
- 不要过多使用枚举，枚举占用的内存空间要比整型大。
- 常量使用static final来修饰。
- 使用一些Android特有的数据结构，如SparseArray和Pair等，他们有更好的性能。
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
- 面向对象编程OOP
- 设计模式：常用的单例模式、工厂模式、观察者模式。《Android源码设计模式解析与实战》


备注：
整理自《Android开发艺术探索》

