# 《Java面试题解析》

# 多线程、并发相关

## 1. 线程池相关

### 1.1 线程池的参数

（参考《实战Java高并发程序设计（第二版）》3.2.3 刨根问底：核心线程池的内部实现 P124/420）



#### 三个例子

对于核心的几个线程池，无论是newFixedThreadPool()方法、newSingleThreadExecutor()方法，还是newCachedThreadPool()方法，虽然看起来创建的线程有着完全不同的功能特点，但其内部实现均使用了ThreadPoolExecutor类。

```java
public static ExecutorService newFixedThreadPool(int nThreads){
    return new ThreadPoolExecutor(nThreads,nThreads, 0L, TimeUnit.MILLISECONDS, new LinkedBlockingQueue<Runnable>());
}

public static ExecutorService newSingleThreadExecutor(){
    return new FinalizableDelegatedExecutorService(
        new ThreadPoolExecutor(1,1,0L,TimeUnit.MILLISECONDS, new LinkedBlockingQueue<Runnable>()));
}

public static ExecutorService newCachedThreadPool(){
    return new ThreadPoolExecutor(0, Integer.MAX_VALUE, 60L, TimeUnit.SECONDS, new SynchronousQueue<Runnable>());
}
```



#### ThreadPoolExecutor的构造函数及其参数

ThreadPoolExecutor类最重要的构造函数：

~~~java
public ThreadPoolExecutor(int corePoolSize,
                          int maximumPoolSize,
                          long keepAliveTime,
                          TimeUnit unit,
                          BlockingQueue<Runnable> workQueue,
                          ThreadFactory threadFactory,
                          RejectedExecutionHandler handler)
~~~

函数的参数含义如下。

- corePoolSize：指定了线程池中的线程数量。
- maximumPoolSize：指定了线程池中的最大线程数量。
- keepAliveTime：当线程池线程数量超过corePoolSize时，多余的空闲线程的存活时间，即超过corePoolSize的空闲线程，在多长时间内会被销毁。
- unit：keepAliveTime的单位。
- workQueue：任务队列，被提交但尚未被执行的任务。
- threadFactory：线程工厂，用于创建线程，一般用默认的即可。
- handler：拒绝策略。当任务太多来不及处理时，如何拒绝任务。

以上参数大部分都很简单，只有参数workQueue和handler需要进行详细说明。



#### 任务队列workQueue

参数workQueue指被提交但未执行的任务队列，它是一个BlockingQueue接口的对象，仅用于存放Runnable对象。根据队列功能分类，在ThreadPoolExecutor类的构造函数中可使用以下几种BlockingQueue接口。

- 直接提交的队列：该功能由SynchronousQueue对象提供。SynchronousQueue是一个特殊的BlockingQueue。SynchronousQueue没有容量，每一个插入操作都要等待一个相应的删除操作，反之，每一个删除操作都要等待对应的插入操作。如果使用SynchronousQueue，则提交的任务不会被真实的保存，而总是将新任务提交给线程执行，如果没有空闲的进程，则尝试创建新的进程，如果进程数量已经达到最大值，则执行拒绝策略。因此，使用SynchronousQueue队列，通常要设置很大的maximumPoolSize值，否则很容易执行拒绝策略。
- 有界的任务队列：有界的任务队列可以使用ArrayBlockingQueue类实现。ArrayBlockingQueue类的构造函数必须带一个容量参数，表示该队列的最大容量：`public ArrayBlockingQueue(int capacity)`
  当使用有界的任务队列时，若有新的任务需要执行，如果线程池的实际线程数小于corePoolSize，则会优先创建新的线程，若大于corePoolSize，则会将新任务加入等待队列。若等待队列已满，无法加入，则在总线程数不大于maximumPoolSize的前提下，创建新的进程执行任务。若大于maximumPoolSize，则执行拒绝策略。可见，有界队列仅当在任务队列装满时，才可能将线程数提升到corePoolSize以上，换言之，除非系统非常繁忙，否则要确保核心线程数维持在corePoolSize。
- 无界的任务队列：无界任务队列可以通过LinkedBlockingQueue类实现。与有界队列相比，除非系统资源耗尽，否则无界的任务队列不存在任务入队失败的情况。当有新的任务到来，系统的线程数小于corePoolSize时，线程池会生成新的线程执行任务，但当系统的线程数小于corePoolSize时，线程池会生成新的线程执行任务，但当系统的线程数达到corePoolSize后，就不会继续增加了。若后序仍有新的任务加入，而又没有空闲的线程资源，则任务直接进入队列等待。若任务创建和处理的速度差异很大，无界队列会保持快速增长，直到耗尽系统内存。
- 优先任务队列：优先任务队列是带有执行优先级的队列。它通过PriorityBlockingQueue类实现，可以控制任务的执行先后顺序。它是一个特殊的无界队列。无论是有界队列ArrayBlockingQueue类，还是未指定大小的无界队列LinkedBlockingQueue类都是按照先进先出算法处理任务的。而PriorityBlockingQueue类则可以根据任务自身的优先级顺序先后执行，在确保系统性能的同时，也能有很好的质量保证（总是确保高优先级的任务先执行）。



#### 三个特例的解析

回顾newFixedThreadPool()方法的实现，它返回了一个corePoolSize和maximumPoolSize大小一样的，并且使用了LinkedBlockingQueue任务队列的线程池。因为对于固定大小的线程池而言，不存在线程数量的动态变化，因此corePoolSize和maximumPoolSize可以相等。同时，它使用无界队列存放无法立即执行的任务，当任务提交非常频繁的时候，该队列可能迅速膨胀，从而耗尽系统资源。

newSingleThreadExecutor()方法返回的单线程线程池，是newFixedThreadPool()方法的一种退化，只是简单地将线程池线程数量设置为1。

newCachedThreadPool()方法返回corePoolSize为0，maximumPoolSize无穷大的线程池，这意味着在没任务时，该线程池内无线程，而当任务被提交时，该线程池会使用空闲的线程执行任务，若无空闲线程，则将任务加入SynchronousQueue队列，而SynchronousQueue队列是一种直接提交的队列，它总会迫使线程池增加新的线程执行任务。当任务执行完毕后，由于corePoolSize为0，因此空闲线程又会在指定时间内（60秒）被回收。

对于newCachedThreadPool()方法，如果同时有大量任务被提交，而任务的执行又不那么快时，那么系统便会开启等量的线程处理，这样做可能很快耗尽系统的资源。



### 1.2 优化线程池线程数量

（参考《实战Java高并发程序设计（第二版）》3.2.7 合理的选择：优化线程池线程数量 P135/420）

线程池的大小对系统的性能有一定的影响。在Java Concurrecy in Practice一书中给出了估算线程池大小的公式：

```
Ncpu = CPU的数量
Ucpu = 目标CPU的使用率，0<=Ucpu<=1
W/C = 等待时间与计算时间的比率
```

为保持处理器达到期望的使用率，最优的线程池大小等于：

```
Nthreads = Ncpu * Ucpu * (1 + W/C)
```

在Java中，可以通过如下代码取得可用的CPU数量。

```java
Runtime.getRuntime().availableProcessors()
```



## 2. 锁的优化

### 2.1 有助于提高锁性能的方法

（参考《实战Java高并发程序设计（第二版）》4.1 有助于提高锁性能的几点建议 P178/420）

#### 减少锁持有时间

只在必要时进行同步，这样就能明显减少线程持有锁的时间，提高系统的吞吐量。

例子：Pattern类的matcher()方法。matcher()方法有条件地进行锁申请，只有在表达式未编译时，进行局部的加锁。这种处理方式大大提高了matcher()方法的执行效率和可靠性。

减少锁的持有时间有助于降低锁冲突的可能性，进而提升系统的并发能力。

#### 减少锁粒度

典型的使用场景：ConcurrentHashMap类的实现。

对于ConcurrentHashMap类，它内部进一步细分了若干个小的HashMap，称之为段（SEGMENT）。默认情况下，一个ConcurrentHashMap类可以细分为16个段。如果需要在ConcurrentHashMap类中增加一个新的表项，并不是将整个HashMap加锁，而是首先根据hashcode得到该表项应该被存放到哪个段中，然后对该段加锁，并完成put()方法操作。

减少锁粒度会带来一个新的问题，即当系统需要取得全局锁时，其消耗的资源会比较多。比如ConcurrentHashMap类的size()方法，在计算总数时，先要获得所有段的锁再求和。但是，ConcurrentHashMap类的size()方法并不总是这样执行的，事实上，size()方法会先使用无锁的方式求和，如果失败才会尝试这种加锁的方法。但不管怎么说，在高并发场合ConcurrentHashMap类的size()方法的性能依然要差于同步的HashMap。

所谓减少锁粒度，就是指缩小锁定对象的范围，从而降低了锁冲突的可能性，进而提高系统的并发能力。

#### 用读写分离锁来替换独占锁

使用读写分离锁来替代独占锁是减小锁粒度的一种特殊情况。如果说减小锁粒度是通过分割数据结构实现的，那么读写分离锁则是对系统功能点的分割。

在读多写少的场合使用读写锁可以有效提升系统的并发能力。

#### 锁分离

如果将读写锁的思想进一步延伸，就是锁分离。读写锁根据读写操作功能上的不同，进行了有效的锁分离。依据应用程序的特点，使用类似的分离思想，也可以对独占锁进行分离。一个典型的案例就是java.util.concurrent.LinkedBlockingQueue的实现。

在JDK的实现中，用两把不同的锁分离了take()方法和put()方法的操作。

#### 锁粗化

虚拟机在遇到一连串连续地对同一个锁不断进行请求和释放的操作时，便会把所有的锁操作整合成对锁的一次请求，从而减少对锁的请求同步的次数，这个操作叫做锁的粗化。

性能优化就是根据运行时的真实情况对各个资源点进行权衡折中的过程。锁粗化的思想和减少锁持有时间是相反的，但在不同的场合，它们的效果并不相同，因此要根据实际情况进行权衡。



### 2.2 Java虚拟机对锁优化

（参考《实战Java高并发程序设计（第二版）》4.2 Java虚拟机对锁优化所做的努力 P185/420）

#### 锁偏向

锁偏向是一种针对对锁操作的优化手段。它的核心思想是：如果一个线程获得了锁，那么锁就进入偏向模式。当这个线程再次请求锁时，无须再做任何同步操作。这样就节省了大量有关锁申请的操作，从而提高了程序性能。因此，对于几乎没有锁竞争的场合，偏向锁有比较好的优化效果，因为连续多次极有可能是同一个线程请求相同的锁。而对于锁竞争比较激烈的场合，其效果不佳。因为在竞争激烈的场合，最有可能的情况是每次都是不同的线程来请求相同的锁。这样偏向模式会失效，因此还不如不启用偏向锁。使用Java虚拟机参数-XX:+UseBiasedLocing可以开启偏向锁。

#### 轻量级锁

如果偏向锁失败，那么虚拟机并不会立即挂起线程，它还会使用一种称为轻量级锁的优化手段。轻量级锁的操作也很方便，它只是简单地将对象头部作为指针指向持有锁的线程堆栈的内部，来判断一个线程是否持有对象锁。如果线程获得轻量级锁成功，则可以顺利进入临界区。如果轻量级锁加锁失败，则表示其他线程抢先争夺到了锁，那么当前线程的锁请求就会膨胀为重量级锁。

#### 自旋锁

锁碰撞后，为了避免线程真实地在操作系统层面挂起，虚拟机还会做最后的努力——自旋锁。当前线程暂时无法获得锁，而且什么时候可以获得锁是一个未知数，也许在几个CPU时钟周期后就可以得到锁。如果这样，简单粗暴地挂起线程可能是一种得不偿失的操作。系统会假设在不久的将来，线程可以得到这把锁。因此，虚拟机会让当前线程做几个空循环（这也是自旋的含义），在经过若干个循环后，如果可以得到锁，那么就顺利进入临界区。如果还不能获得锁，才会真正将线程在操作系统层面挂起。

#### 锁消除

锁消除是一种更彻底的锁优化。Java虚拟机在JIT编译时，通过对运行上下文的扫描，去除不可能存在共享资源竞争的锁。通过锁消除，可以节省毫无意义的请求锁时间。

锁消除涉及的一项关键技术为逃逸分析。所谓逃逸分析就是观察某一个变量是否会逃出某一个作用域。逃逸分析必须在-server模式下进行，可以使用-XX:+DoEscapeAnalysis参数打开逃逸分析。使用-XX:+EliminateLocks参数可以打开锁消除。

## 3. ThreadLocal

SimpleDateFormat不是线程安全的

### 3.1 ThreadLocal的实现原理

（参考《实战Java高并发程序设计（第二版）》4.3.2 ThreadLocal的实现原理 P189/420）

#### set()和get()方法

```java
public void set(T value){
    Thread t = Thread.currentThread();
    ThreadLocalMap map = getMap(t);
    if(map!=null) map.set(this,value);
    else createMap(t,value);
}
```

set()方法，在set时，首先获得当前线程对象，然后通过getMap()方法拿到线程的ThreadLocalMap，并将值存入ThreadLocalMap中。而ThreadLocalMap可以理解为一个Map（虽然不是，但是你可以把它简单地理解成HashMap），但是它是定义在Thread内部的成员。

而设置到ThreadLocal中的数据，也正是写入了threadLocals的这个Map。其中，key为ThreadLocal当前对象，value就是我们需要的值。而threadLocals本身就保存了当前自己所在线程的所有“局部变量”，也就是一个ThreadLocal变量的集合。

在进行get()方法操作时，自然就是将这个Map中的数据拿出来。

~~~java
public T get(){
    Thread t = Thread.currentThread();
    ThreadLocalMap map = getMap(t);
    if(map!=null){
        ThreadLocalMap.Entry e = map.getEntry(this);
        if(e!=null) return (T)e.value;
    }
    return setInitialValue();
}
~~~

get()方法先取得当前线程的ThreadLocalMap对象，然后通过将自己作为key取得内部的实际数据。

#### ThreadLocal的回收

在了解ThreadLocal的内部实现后，我们自然会引出一个问题：那就是这些变量是维护在Thread内部的（ThreadLocalMap定义所在类），这也意味着只要线程不退出，对象的引用将一直存在。

如果你希望即使回收对象，最好使用ThreadLocal.remove()方法将这个变量移除。

另外一个有趣的情况是JDK也可能允许你想释放普通变量一样释放ThreadLocal。比如，我们有时候为了加速垃圾回收，会特意写出类似obj = null的代码。

要了解这里的回收机制，我们需要进一步了解Thread.ThreadLocalMap的实现。之前我们说过，ThreadLocalMap是一个类似HashMap的东西。更准确地说，它更加类似WeakHashMap。

ThreadLocalMap的实现使用了弱引用。弱引用是比强引用弱得多的引用。Java虚拟机在垃圾回收时，如果发现弱引用，就会立即回收。ThreadLocalMap内部由一系列Entry构成，每一个Entry都是WeakReference<ThreadLocal\>。

```java
static class Entry extends WeakReference<ThreadLocal>{
    /** The value associated with this ThreadLocal. */
    Object value;
    Entry(ThreadLocal k, Object v){
        super(k);
        value = v;
    }
}
```

这里的参数k就是Map的key，v就是Map的value，其中k也是ThreadLocal实例，作为弱引用使用。因此，虽然这里使用ThreadLocal作为Map的key，但是实际上，它并不真正持有ThreadLocal的引用。而当ThreadLocal的外部强引用被回收时，ThreadLocalMap中的key就会变成null。当系统进行ThreadLocalMap清理时（比如将新的变量加入表中，就会自动进行一次清理），就会将这些垃圾数据回收



## 4. Future

### 4.1 Future的实现

（参考《实战Java高并发程序设计（第二版）》5.5.3 JDK中的Future模式 P252/420）

#### Future模式的基本结构

Future接口类似于订单或者说是契约。通过它，你可以得到真实的数据。RunnableFuture继承了Future和Runnable两个接口，其中run()方法用于构造真实的数据。它有一个具体的实现FutureTask类。FutureTask类有一个内部类Sync，一些实质性的工作会委托Sync类实现，而Sync类最终会调用Callable接口，完成实际数据的组装工作。

Callable接口只有一个方法call(),它会返回需要构造的实际数据。这个Callable接口也是Future框架和应用程序之间的重要接口。要实现自己的业务系统，通常需要实现自己的Callable对象。此外FutureTask类也与应用密切相关，通常可以使用Callable实例构造一个FutureTask实例，并将它提交给线程池。



## 5. Fork/Join

（参考《实战Java高并发程序设计（第二版）》3.2.9 分而治之：Fork/Join框架 P140/420）

Fork一词的原始含义是吃饭用的叉子，也有分叉的意思。在Linux平台中，方法fork()用来创建子进程，使得系统进程可以多一个执行分支。在Java中也沿用了类似的命名方式。

而join()方法的含义在之前的章节中已经解释过，这里表示等待。也就是使用fork()方法后系统多了一个执行分支（线程），所以需要等待这个执行分支执行完毕，才有可能得到最终的结果，因此join()方法就表示等待。

在实际使用中，如果毫无顾忌地使用fork()方法开启线程进行处理，那么很有可能导致系统开启过多的线程而严重影响性能。所以，在JDK中，给出了一个ForkJoinPool线程池，对于fork()方法并不急着开启线程，而是提交给ForkJoinPool线程池进行处理，以节省系统资源。

由于线程池的优化，提交的任务和线程数量并不是一对一的关系。在绝大多数情况下，一个物理线程实际上是需要处理多个逻辑任务的。因此，每个线程必然需要拥有一个任务队列。因此，在实际执行过程中，可能遇到这么一种情况：线程A已经把自己的任务都执行完了，而线程B还是一堆任务等着处理，此时，线程A就会“帮助”线程B，从线程B的任务队列中哪一个任务过来处理，尽可能地达到平衡。一个值得注意的地方是，当一个线程试图“帮助”其他线程时，总是从任务队列的底部开始获取数据，而线程试图执行自己的任务时，则是从相反的顶部开始获取数据。因此，这种行为也十分有利于避免数据竞争。

下面我们来看一个ForkJoinPool线程池的一个重要的接口：

`public <T> ForkJoinTask<T> submit(ForkJoinTask<T> task)`

你可以向ForkJoinPool线程池提交一个ForkJoinTask任务。所谓ForkJoinTask任务就是支持fork()方法分解及join()方法等待的任务。ForkJoinTask任务有两个重要的子类，RecursiveAction类和RecursiveTask类。它们分别表示没有返回值的任务和可以携带返回值的任务。

在使用Fork/Join框架时需要注意，如果任务划分层次很多，一直得不到返回，那么可能出现两种情况。第一，系统内的线程数量越积越多，导致性能严重下降。第二，函数的调用层次变多，最终导致栈溢出。不同版本的JDK内部实现机制可能有差异，从而导致其表现不同。

此外，ForkJoin线程池使用一个无锁的栈来管理空闲进程。如果一个工作进程暂时取不到可用的任务，则可能被挂起，挂起的线程将会被压入由线程池维护的栈中。待将来有任务可用时，再从栈中唤醒这些线程。



## 6. synchronized底层实现



## 7. volatile



## 8. JUC包



## 9. 线程安全

# 设计模式



## 1. 单例模式

（参考[《在java中写出完美的单例模式》](https://www.cnblogs.com/dongyu666/p/6971783.html) 、《Effective Java》（第3版）第3条：用私有构造器或者枚举类型强化Singleton属性 P24/306、[《为什么最好的单例模式是枚举单例》](https://blog.csdn.net/qq_36642340/article/details/82055786)、[《Java并发笔记——单例与双重检测》](https://www.cnblogs.com/NaLanZiYi-LinEr/p/7492571.html)）

### 1.1 懒汉式(延迟加载)

#### 简单版本

~~~java
public class Singleton{
    private static Singleton instance;
    private Singleton(){}
    public static Singleton getInstance(){
        if(instance==null){
            instance = new Singleton();
        }
        return instance;
    }
}
~~~

问题在于，当多线程工作的时候，如果有多个线程同时运行到if (instance == null)，都判断为null，那么两个线程就各自会创建一个实例——这样一来，就不是单例了。

#### synchronized版本

~~~java
public class Singleton{
    private static Singleton instance;
    private Singleton(){}
    public static synchronized Singleton getInstance(){
        if(instance == null){
            instance = new Singleton();
        }
        return instance;
    }
}
~~~

这种写法也有一个问题：给getInstance方法加锁，虽然会避免了可能会出现的多个实例问题，但是会强制除进入临界区的线程之外的所有线程等待，实际上会对程序的执行效率造成负面影响。

#### 双重检查版本

~~~java
public class Singleton {
    private static Singleton instance;
    private Singleton() {}
    public static Single3 getInstance() {
        if (instance == null) {
            synchronized (Singleton.class) {
                if (instance == null) {
                    instance = new Singleton();
                }
            }
        }
        return instance;
    }
}
~~~

但是这种双重检测机制在JDK1.5之前是有问题的。要弄清楚为什么这个版本可能出现的问题，首先，我们需要弄清楚几个概念：原子操作、指令重排。

简单来说，原子操作（atomic）就是不可分割的操作，在计算机中，就是指不会因为线程调度被打断的操作。

指令重排简单来说，就是计算机为了提高执行效率，会做的一些优化，在不影响最终结果的情况下，可能会对一些语句的执行顺序进行调整。

问题主要在于instance = new Singleton()这句，这并非是一个原子操作：

1. 给 singleton 分配内存

2. 调用 Singleton 的构造函数来初始化成员变量，形成实例

3. 将singleton对象指向分配的内存空间（执行完这步 singleton才是非 null 了）

但是在 JVM 的即时编译器中存在指令重排序的优化。也就是说上面的第二步和第三步的顺序是不能保证的，最终的执行顺序可能是 1-2-3 也可能是 1-3-2。如果是后者，则在 3 执行完毕、2 未执行之前，被线程二抢占了，这时 instance 已经是非 null 了（但却没有初始化），所以线程二会直接返回 instance，然后使用，然后顺理成章地报错。

这里的关键在于——线程T1对instance的写操作没有完成，线程T2就执行了读操作。

#### 终极版本

~~~java
public class Singleton {
    private static volatile Singleton instance;
    private Singleton() {}
    public static Singleton getInstance() {
        if (instance == null) {
            synchronized (Singleton.class) {
                if (instance == null) {
                    instance = new Singleton();
                }
            }
        }
        return instance;
    }
}
~~~

 JDK1.5之后，可以使用volatile关键字修饰变量来解决无序写入产生的问题，因为volatile关键字的一个重要作用是禁止指令重排序，即保证不会出现内存分配、返回对象引用、初始化这样的顺序，从而使得双重检测真正发挥作用。把instance声明为volatile之后，对它的写操作就会有一个内存屏障（什么是内存屏障？），这样，在它的赋值完成之前，就不用会调用读操作。

注意：volatile阻止的不singleton = new Singleton()这句话内部[1-2-3]的指令重排，而是保证了在一个写操作（[1-2-3]）完成之前，不会调用读操作（if (instance == null)）。

不过，非要挑点刺的话还是能挑出来的，就是这个写法有些复杂了，不够优雅、简洁。

### 1.2 饿汉式（非延迟加载）

如上所说，饿汉式单例是指：指全局的单例实例在类装载时构建的实现方式。

由于类装载的过程是由类加载器（ClassLoader）来执行的，这个过程也是由JVM来保证同步的，所以这种方式先天就有一个优势——能够免疫许多由多线程引起的问题。

#### 公有域方法

~~~java
public class Singleton{
    public static final Singleton INSTANCE = new Singleton();
    private Singleton(){}
}
~~~

要提醒一点：享有特权的客户端可以借助AccessibleObject.setAccessable方法，通过反射机制调用私有构造器。如果要抵御这种攻击，可以修改构造器，让它在被要求创建第二个实例的时候抛出异常。

公有域方法的主要优势在于，API很清楚地表明了这个类是一个Singleton：公有的静态域是final的，所以该域总是包含相同的对象引用。第二个优势在于它很简单。

#### 静态工厂方法

~~~java
public class Singleton{
    private static final Singleton INSTANCE = new Singleton();
    private Singleton(){}
    public static Singleton getInstance(){
        return INSTANCE;
    }
}
~~~

静态工厂方法的优势之一在于，它提供了灵活性：在不改变其API的前提下，我们可以改变这个类是否应该为Singleton的想法。工厂方法返回该类的唯一实例，但是，它很容易被修改，比如改成为每个调用该方法的线程返回一个唯一的实例。第二个优势是，如果应用程序需要，可以编写一个泛型Singleton工厂。使用静态工厂的最后一个优势是，可以通过方法引用作为提供者，比如Singleton::instance就是一个Supplier<Singleton\>。除非满足以上任意一种优势，否则还是优先考虑公有域的方法。



它们的缺点也就只是饿汉式单例本身的缺点所在了——由于INSTANCE的初始化是在类加载时进行的，而类的加载是由ClassLoader来做的，所以开发者本来对于它初始化的时机就很难去准确把握：

1. 可能由于初始化的太早，造成资源的浪费
2. 如果初始化本身依赖于一些其他数据，那么也就很难保证其他数据会在它初始化之前准备好。

当然，如果所需的单例占用的资源很少，并且也不依赖于其他数据，那么这种实现方式也是很好的。

### 1.3 Effective Java的写法

#### 静态内部类

《Effective Java》一书的第一版中推荐了一个写法：

~~~java
public class Singleton {
    private static class SingletonHolder {
        private static final Singleton INSTANCE = new Singleton();
    }
    private Singleton (){}
    public static final Singleton getInstance() {
        return SingletonHolder.INSTANCE;
    }
}
~~~

这种写法非常巧妙：

- 对于内部类SingletonHolder，它是一个饿汉式的单例实现，在SingletonHolder初始化的时候会由ClassLoader来保证同步，使INSTANCE是一个真·单例。 
- 同时，由于SingletonHolder是一个内部类，只在外部类的Singleton的getInstance()中被使用，所以它被加载的时机也就是在getInstance()方法第一次被调用的时候。

——它利用了ClassLoader来保证了同步，同时又能让开发者控制类加载的时机。从内部看是一个饿汉式的单例，但是从外部看来，又的确是懒汉式的实现。

简直是神乎其技。

#### 枚举

实现Singleton还可以声明一个包含单个元素的枚举类型：

~~~java
// Effective Java 第二版推荐写法
public enum SingleInstance {
    INSTANCE;
    public void fun1() { 
        // do something
    }
}
// 使用
SingleInstance.INSTANCE.fun1();
~~~

由于创建枚举实例的过程是线程安全的，所以这种写法也没有同步的问题。

枚举类还能能防止利用反射方式获取枚举对象，调用反射newInstance方法时会检查是否为枚举类，如果是将报错，错误如下：`Exception in thread "main" java.lang.IllegalArgumentException: Cannot reflectively create enum objects`

枚举类能防止使用序列化与反序列化获取新的枚举对象。在序列化的时候Java仅仅是将枚举对象的name属性输出到结果中，反序列化的时候则是通过java.lang.Enum的valueOf方法来根据名字查找枚举对象。同时，编译器是不允许任何对这种序列化机制的定制的，因此禁用了writeObject、readObject、readObjectNoData、writeReplace和readResolve等方法。

Effective Java（第3版）中的评价：这种方法在功能上与公有域方法相似，但更加简洁，无偿提供了序列化机制，绝对防止多次实例化，即使是在面对复杂的序列化或者反射攻击的时候。虽然这种方法还没有广泛采用，但是单元素的枚举类型经常称为实现Singleton的最佳方法。注意，如果Singleton必须扩展一个超类，而不是扩展Enum的时候，则不宜使用这个方法（虽然可以声明枚举去实现接口）。

# 数据结构、集合相关

## 1. HashMap、HashTable、TreeMap、LinkedHashMap、ConcurrentHashMap

（参考[《浅谈hashMap、hashTable、ConcurrentHashMap（1.6、1.8）》][https://www.imooc.com/article/details/id/19737]、《实战Java高并发程序设计（第二版）》3.3.2 线程安全的HashMap P147/420）

### 1.1 HashMap

HashMap是最常用的集合类框架之一，它实现了Map接口，所以存储的元素也是键值对映射的结构，并允许使用null值和null键，其内元素是无序的，如果要保证有序，可以使用LinkedHashMap。

在hashMap的size达到`initialCapacity * loadFactor`时会对hashMap进行扩容，值得注意的是hashMap的size大小总是2的幂次方。初始容量和加载因子的选取也是影响HashMap性能的原因之一，加载因子过高虽然减少了空间开销，但同时也增加了查找某个条目的时间；加载因子过低也可能引起HashMap容易执行rehash操作。

当hashMap扩容的时候会调用transfer方法，它是采用头插法来转移旧值到新的hashMap中去，假如转移前链表顺序是 1->2->3，那么转移后就会变成 3->2->1，那么在多线程的情况下就可能造成 1->2 的同时 2->1 的环形链表，进而形成死循环。

那在多线程下使用HashMap我们可以采用如下几种方案：

- 在外部包装HashMap，实现同步机制。
  使用集合的工具类 Collections的静态方法synchronizedMap()，在这个方法中创建了工具类 Collections中的内部类SynchronizedMap的实例来实现HashMap的线程安全：Map m = Collections.synchronizedMap(new HashMap(...));，这里就是对HashMap做了一次包装
  synchronizedMap()方法会生成一个名为SynchronizedMap的Map。它使用委托，将自己所有Map相关功能交给传入的HashMap实现，而自己则主要负责保证线程安全。SynchronizedMap内包装了一个Map m。通过Object mutex实现对这个Map m的互斥操作。

  ~~~java
  public V get(Object key){
      synchronized(mutex){return m.get(key);}
  }
  ~~~

  而其他所有相关的Map操作都会使用这个mutex进行同步，从而实现线程安全。

- 使用java.util.HashTable，效率最低

- 使用java.util.concurrent.ConcurrentHashMap，相对安全，效率较高。

### 1.2 HashTable

HashTable和HashMap很相似，但HashTable是线程安全的，同时HashTable中的key和value都不能为null。

在Hashtable中很多操作Hashtable的方法都加上了synchronized关键字来保证线程安全，比如：get和put方法。

从上面可以看出Hashtable和Collections.synchronizedMap(hashMap)基本上是对读写进行加锁操作，一个线程在读写元素，其余线程必须等待，性能是十分糟糕的，而且Hashtable已经过时了。

### 1.3 ConcurrentHashMap

为了提高高并发下的性能，我们来看下并发安全的ConcurrentHashMap。ConcurrentHashMap之所以高效是因为它更好的降低了锁的粒度，锁加在了每个Segment上而不是直接加在整个HashMap上，参数concurrencyLevel是用户估计的并发级别，就是说你觉得最多有多少线程共同修改这个map，根据这个来确定Segment数组的大小，concurrencyLevel默认为16。ConcurrentHashMap的key和value都不能为null。

先来看下ConcurrentHashMap 在JDK1.6中的版本。

ConcurrentHashMap采用分段锁的机制，实现并发的更新操作，底层采用数组+链表+红黑树的存储结构。

其包含两个核心静态内部类 Segment (默认分为16段) 和HashEntry。

Segment继承ReentrantLock用来充当锁的角色，每个 Segment 对象守护每个散列映射表的若干个桶。

HashEntry 用来封装映射表的键 / 值对；
每个桶是由若干个 HashEntry 对象链接起来的链表。

一个 ConcurrentHashMap 实例中包含由若干个 Segment 对象组成的数组，如图所示：


![img](https://img.mukewang.com/598581d50001106005020537.png)

ConcurrentHashMap 在JDK1.8中的版本

而在JDK1.8中的实现已经抛弃了Segment分段锁机制，利用CAS+Synchronized来保证并发更新的安全，底层依然采用数组+链表+红黑树的存储结构。

CAS，Compare and Swap即比较并替换，CAS有三个操作数：内存值V、旧的预期值A、要修改的值B，当且仅当预期值A和内存值V相同时，将内存值修改为B并返回true，如果不相同则证明内存值在并发的情况下被其它线程修改过了，则不作任何修改，返回false，等待下次再修改。

table：默认为null，初始化发生在第一次插入操作，默认大小为16的数组，用来存储Node节点数据，扩容时大小总是2的幂次方。

Node：保存key，value及key的hash值的数据结构。

1.6中采用ReentrantLock 分段锁的方式，使多个线程在不同的segment上进行写操作不会发现阻塞行为；1.8中直接采用了内置锁synchronized。



## 2. 排序

### 2.1 归并排序



### 2.2 快速排序



### 2.3 外排序



# 虚拟机相关

## 1. java内存区域

（参考《深入理解Java虚拟机：JVM高级特性与最佳实践（第二版）》 2.2 运行时数据区域 P61/457）

运行时数据区：

- 由所有线程共享的数据区：方法区、堆
- 线程隔离的数据区：虚拟机栈、本地方法栈、程序计数器

执行引擎 -> 本地库接口 -> 本地方法库

### 1.1 程序计数器

由于Java虚拟机的多线程是通过线程轮流切换并分配处理器执行时间的方式来实现的，在任何一个确定的时刻，一个处理器（对于多核处理器来说是一个内核）都只会执行一条线程中的指令。因此，为了线程切换后能恢复到正确的执行位置，每条线程都需要有一个独立的程序计数器，每条线程之间计数器互不影响，独立存储，我们称这类内存区域为“线程私有”的内存。

如果线程正在执行的是一个Java方法，这个计数器记录的是正在执行的虚拟机字节码指令的地址；如果正在执行的是Native方法，这个计数器值则为空（Undefined）。此内存区域是唯一一个在Java虚拟机规范中没有规定任何OutOfMemoryError情况的区域。

### 1.2 Java虚拟机栈

与程序计数器一样，Java虚拟机栈也是线程私有的，它的生命周期与线程相同。虚拟机栈描述的是Java方法执行的内存模型：每个方法在执行的同时都会创建一个栈帧用于存储局部变量表、操作数栈、动态链接、方法出口等信息。每一个方法从调用直至执行完成的过程，就对应着一个栈帧在虚拟机栈中入栈到出栈的过程。

经常有人把Java内存区分为堆内存（Heap）和栈内存（Stack），这种分发比较粗糙，Java内存区域的划分实际上远比这复杂。这里所指的“栈”就是现在讲的虚拟机栈，或者说是虚拟机栈中局部变量表部分。

局部变量表存放了编译期可知的各种基本数据类型（boolean、byte、char、short、int、float、long、double）、对象引用（reference类型，它不等于对象本身，可能是一个指向对象起始地址的引用指针，也可能是指向一个代表对象的句柄或其他与此对象相关的位置）和returnAddress类型（指向一条字节码指令的地址）。

其中64位长度的long和double类型的数据会占用2个局部变量空间（Slot），其余的数据类型只占用1个。局部变量表所需的内存空间在编译期间完成分配，当进入一个方法时，这个方法需要在帧中分配多大的局部变量空间是完全确定的，在方法运行期间不会改变局部变量表的大小。

在Java虚拟机规范中，对这个区域规定了两种异常状况：如果线程请求的栈深度大于虚拟机所允许的深度，将抛出StackOverflowError异常；如果虚拟机栈可以动态扩展（当前大部分的Java虚拟机都可动态扩展，只不过Java虚拟机规范中也允许固定长度的虚拟机栈），如果扩展时无法申请到足够的内存，就会抛出OutOfMemoryError异常。

### 1.3 本地方法栈

本地方法栈（Native Method Stack）与虚拟机栈所发挥的作用是非常相似的，它们之间的区别不过是虚拟机栈执行Java方法（也就是字节码）服务，而本地方法栈则为虚拟机使用到的Native方法服务。在虚拟机规范中对本地方法栈中使用的语言、使用方式与数据结构并没有强制规定，因此具体的虚拟机可以自由实现它。甚至有的虚拟机（譬如Sun HotSpot虚拟机）直接就把本地方法栈和虚拟机栈合二为一。与虚拟机栈一样，本地方法栈区域也会抛出StackOverflowError和OutOfMemoryError异常。

### 1.4 Java堆

对于大多数应用来说，Java堆（Java Heap）是Java 虚拟机所管理的内存中最大的一块。Java堆是被所有线程贡献的一块内存区域，在虚拟机启动时创建。此内存区域的唯一目的就是存放对象实例，几乎所有的对象实例都在这里分配内存。这一点在Java虚拟机规范中的描述是：所有的对象实例以及数组都要在堆上分配，但是随着JIT编译器的发展与逃逸分析技术逐渐成熟，栈上分配、标量替换优化技术将会导致一些微妙的变化发生，所有的对象都分配在堆上也渐渐变得不是那么“绝对”了。

Java堆是垃圾收集器管理的主要区域，因此很多时候也被称作“GC堆”。从内存回收的角度来看，由于现在收集器基本都采用分代收集算法，所以Java堆中还可以细分为：新生代和老年代；再细致一点的有Eden空间、From Survivor空间、To Survivor空间等。从内存分配的角度来看，线程共享的Java堆中可能划分出多个线程私有的分配缓冲区（Thread Local Allocation Buffer，TLAB）。不过无论如何划分，都与存放内容无关，无论哪个区域，存储的都仍然是对象实例，进一步划分的目的是为了更好地回收内存，或者更快地分配内存。

根据Java虚拟机规范的规定，Java堆可以处于物理上不连续的内存空间中，只有逻辑上是连续的即可，就像我们的磁盘空间一样。在实现时，既可以实现成固定大小的，也可以是可扩展的，不过当前主流的虚拟机都是按照可扩展来实现的（通过-Xmx和-Xms来控制）。如果堆中没有内存完成实例分配，并且堆也无法再扩展时，将会抛出OutOfMemoryError异常。

### 1.5 方法区

方法区（Method Area）与Java堆一样，是各个线程共享的内存区域，它用于存储已被虚拟机加载的类信息、常量、静态变量、即时编译器编译后的代码等数据。虽然Java虚拟机规范把方法区描述为堆的一个逻辑部分，但是它却有一个别名叫做Non-Heap（非堆），目的应该是与Java堆区分开来。

对于习惯在HotSpot虚拟机上开发、部署程序的开发者来说，很多人都更愿意把方法区称为“永久代”（Permanent Generation），本质上两者并不等价，仅仅是因为HotSpot虚拟机的设计团队选择把GC分代收集扩展至方法区，或者说使用永久代来实现方法区而已，这样HotSpot的垃圾收集器可以像管理Java堆一样管理这部分内存，能够省去专门为方法区编写内存管理代码的工作。

> jdk1.8中则把永久代给完全删除了，取而代之的是 MetaSpace(元空间)。
>
> **为什么废弃永久代（PermGen）**
>
> 1. 由于永久代内存经常不够用或发生内存泄露，爆出异常*java.lang.OutOfMemoryError: PermGen*
> 2. 永久代会为 GC 带来不必要的复杂度，并且回收效率偏低。
> 3. Oracle 可能会将HotSpot 与 JRockit 合二为一。

Java虚拟机规范对方法区的限制非常宽松，除了和Java堆一样不需要连续的内存和可以选择固定大小或者可扩展外，还可以选择不实现垃圾收集。相对而言，垃圾收集行为在这个区域是比较少出现的，但并非数据进入了方法区就如永久代的名字一样“永久”存在了。这区域的内存回收目标主要是针对常量池的回收和对类型的卸载，一般来说这区域的回收“成绩”比较难以令人满意，尤其是类型的卸载，条件相当苛刻，但是这部分区域的回收确实是必要的。在Sun公司的BUG列表中，曾出现过的若干个严重的BUG就是由于低版本的HotSpot虚拟机对此区域未完全回收而导致内存泄漏。

根据Java虚拟机规范的规定，当方法区无法满足内存分配需求时，将抛出OutOfMemoryError异常。

### 1.6 运行时常量池

运行时常量池（Runtime Constant Pool）是方法区的一部分。Class文件中除了有类的版本、字段、方法、接口等描述信息外，还有一项信息是常量池（Constant Pool Table），用于存放编译期生成的各种字面量和符号引用，这部分内容将在类加载后进入方法区的运行时常量池中存放。

Java虚拟机对Class文件每一部分（自然也包括常量池）的格式都有严格的规定，每一个字节用于存储哪种数据都必须符合规范上的要求才会被虚拟机认可、装载和执行，但对于运行时常量池，Java虚拟机规范没有做任何细节的要求，不同的提供商实现的虚拟机可以按照自己的需要来实现这个内存区域。不过，一般来说，除了保存Class文件中描述的符号引用外，还会把翻译出来的直接引用也存储在运行时常量池中。

运行时常量池相当于Class文件常量池的另外一个重要特征是具备动态性，Java 语言并不要求常量一定只有编译器才能产生，也就是并非预置入Class文件中常量池的内容才能进入方法区运行时常量池，运行期间有可能将新的常量放入池中，这种特性被开发人员利用得比较多的便是String类的intern()方法。

既然运行时常量池是方法区的一部分，自然受到方法区内存的限制，当常量池无法再申请到内存时会抛出OutOfMemoryError异常。

## 2.  垃圾收集

### 2.1 判断对象是否存活

（参考《深入理解Java虚拟机：JVM高级特性与最佳实践（第二版）》 3.2 对象已死吗 P85/457）

#### 引用计数算法

引用计数算法（Reference Counting）的实现简单，判定效率也很高，在大部分情况下它都是不错的算法，也有一些比较著名的应用案例，例如微软公司的COM（Component Object Model）技术、使用ActionScript 3 的 FlashPlayer、Python语言和在游戏脚本领域被广泛运用的Squirrel中都使用了引用计数算法进行内存管理。但是，至少主流的Java虚拟机里面没有选用引用计数算法来管理内存，其中最主要的原因是它很难解决对象之间互相循环引用的问题。

#### 可达性分析算法

在主流的商用程序语言（Java，C#，甚至包括前面提到的古老的Lisp）的主流实现中，都是称通过可达性分析（Reachability Analysis）来判断对象是否存活的。这个算法的基本思路就是通过一系列的称为“GC Roots”的对象作为起始点，从这些节点开始向下搜索，搜索所走过的路径称为引用链（Reference Chain），当一个对象到GC Roots没有任何引用链相连（用图论的话来说，就是从GC Roots到这个对象不可达）时，则证明此对象是不可用的。

Java语言中，可作为GC Roots的对象包括下面几种：

- 虚拟机栈（栈帧中的本地变量表）中引用的对象。
- 方法区中类静态属性引用的对象。
- 方法区中常量引用的对象。
- 本地方法栈中JNI（即一般说的Native方法）引用的对象。

### 2.2 垃圾收集算法

（参考《深入理解Java虚拟机：JVM高级特性与最佳实践（第二版）》 3.3 垃圾收集算法 P92/457）

#### 标记-清除算法

最基础的收集算法是“标记-清除”（Mark-Sweep）算法，如同它的名字一样，算法分为“标记”和“清除”两个阶段：首先标记出所有需要回收的对象，在标记完成后统一回收所有被标记的对象，它的标记过程其实在前一节讲述对象标记判定时已经介绍过了。之所以说它是最基础的收集算法，是因为后续的收集算法都是基于这种思路并对其不足进行改进而得到的。

它的主要不足有两个：

- 一个是效率问题，标记和清除两个过程效率都不高；
- 另一个是空间问题，标记清除之后会产生大量不连续的内存碎片，空间碎片太多可能会导致以后在程序运行过程中需要分配较大对象时，无法找到足够的连续内存而不得不提前触发另一次垃圾收集动作。

#### 复制算法

为了解决效率问题，一种称为“复制”（Copying）的收集算法出现了，它将可用内存按容量划分为大小相等的两块，每次只使用其中的一块。当这一块的内存用完了，就将还存活着的对象复制到另外一块上面，然后再把已使用的内存空间一次清理掉。这样使得每次都是对整个半区进行内存回收，内存分配时也就不用考虑内存碎片等复杂情况，只有移动堆顶指针，按顺序分配内存即可，实现简单，运行高效。

只是这种算法的代价是将内存缩小为原来的一半，未免太高了一点。

现在商业虚拟机都采用这种收集算法来回收新生代，IBM的公司专门研究表明，新生代中的对象98%是“朝生夕死”的，所以并不需要按照1：1的比例来划分内存空间，而是将内存分为一块较大的Eden空间和两块较小的Survivor空间，每次使用Eden和其中一块Survivor。（这里说明一下，在HotSpot中这种分代方式从最初就是这种布局，与IBM的研究并没有什么实际联系）当回收时，将Eden和Survivor中还存活着的对象一次性地复制到另一块Survivor空间上，最后清理掉Eden和刚才使用过的Survivor空间。

HotSpot虚拟机默认Eden和Survivor的大小比例是8：1，也就是每次新生代中可用内存空间为整个新生代容量的90%（80%+10%），只有10%的内存会被“浪费”。当然，98%的对象回收只是一般场景下的数据，我们没有办法保证每次回收都只有不多于10%的对象存活，当Survivor空间不够时，需要依赖其他内存（这里指老年代）进行分配担保（Handle Promotion）

内存的分配担保是指：如果另一块Survivor空间没有足够空间存放上一次新生代收集下来的存活对象时，这些对象将直接通过分配担保机制进入老年代。关于对新生代进行分配担保的内容，在本章稍后在讲解垃圾收集器执行规则时还会再详细讲解。

#### 标记-整理算法

复制收集算法在对象存活率较高时就要进行较多的复制操作，效率将会变低。更关键的是，如果不想浪费50%的空间，就需要有额外的空间进行分配担保，以应对被使用的内存中所有对象都100%存活的极端情况，所以在老年代一般不能直接选用这种算法。

根据老年代的特点，有人提出了另外一种“标记-整理”（Mark-Compact）算法，标记过程仍然与“标记-清除”算法一样，但后续步骤不是直接对可回收对象进行清理，而是让所有存活对象都向一端移动，然后直接清理掉端边界以外的内存。

#### 分代收集算法

当前商业虚拟机的垃圾收集都采用“分代收集”（Generational Collection）算法，这种算法并没有什么新思想，只是根据对象存活周期的不同将内存划分为几块。一般是把Java堆分为新生代和老年代，这样就可以根据各个年代的特点采用最适当的收集算法。在新生代中，每次垃圾收集时都发现有大批对象死去，只有少量存活，那就选用复制算法，只需要付出少量存活对象的复制成本就可以完成收集。而老年代中因为对象存活率高，没有额外空间对它进行分配担保，就必须使用“标记-清理”或者“标记-整理”算法来回收。



### 2.3 垃圾收集器

（参考《深入理解Java虚拟机：JVM高级特性与最佳实践（第二版）》 3.5 垃圾收集器 P98/457）

#### Serial收集器

Serial收集器是最基本、发展历史最悠久的收集器，曾经（在JDK 1.3.1之前）是虚拟机新生代收集的唯一选择。大家看名字就会知道，这个收集器是一个单线程的收集器，但它的“单线程”的意义并不仅仅说明它只会使用一个CPU或一条收集线程去完成垃圾收集工作，更重要的是在它进行垃圾收集时，必须暂停其他所有的工作进程，直到它收集结束。

> Serial / Serial Old收集器：
>
> 新生代采取复制算法，暂停所有用户线程，单个GC线程；
>
> 老年代采用标记-整理算法，暂停所有用户线程，单个GC线程。

实际上到现在为止，它依然是虚拟机运行在Client模式下的默认新生代收集器。它也有着优于其他收集器的地方：简单而高效（与其他收集器的单线程比），对于限定单个CPU的环境来说，Serial收集器由于没有线程交互的开销，专心做垃圾收集自然可以获得最高的单线程收集效率。在用户的桌面应用场景中，分配给虚拟机的内存一般来说不会很大，收集几十兆甚至一两百兆的新生代（仅仅是新生代使用的内存，桌面应用基本上不会再大了），停顿时间完全可以控制在几十毫秒最多一百多毫秒以内，只要不是频繁发生，这点停顿是可以接受的。所以，Serial收集器对于运行在Client模式下的虚拟机来说是一个很好的选择。

#### ParNew收集器

ParNew收集器其实就是Serial收集器的多线程版本，除了使用多条线程进行垃圾收集之外，其余行为包括Serial收集器可用的所有控制参数（例如：-XX:SurvivorRatio、-XX:PretenureSizeThreshold、-XX:HandlePromotionFailure等）、收集算法、Stop The World、对象分配规则、回收策略等都与Serial收集器完全一样，在实现上，这两种收集器也共用了相当多的代码。

> ParNew / Serial Old收集器：
>
> 新生代：采用复制算法，暂停所有用户线程，多个GC线程；
>
> 老年代：采取标记-整理算法，暂停所有用户线程，单个GC线程。

ParNew收集器除了多线程收集以外，其他与Serial收集器相比并没有太多创新之处，但它却是许多运行在Server模式下的虚拟机中首选的新生代收集器，其中有一个与性能无关但很重要的原因是，除了Serial收集器外，目前只有它能与CMS收集器配合工作。在JDK1.5时期，HotSpot推出了一款在强交互应用中几乎可认为有划时代意义的垃圾收集器——CMS收集器（Concurrent Mark Sweep），这款收集器是HotSpot虚拟机中第一款真正意义上的并发（Concurrent）收集器，它第一次实现了让垃圾收集线程与用户线程（基本上）同时工作。

不幸的是，CMS作为老年代的收集器，却无法与JDK 1.4.0中已经存在的新生代收集器Parallel Scavenger配合工作，所以在JDK 1.5中使用CMS来收集老年代的时候，新生代只能选择ParNew或者Serial收集器中的一个。ParNew收集器也是使用-XX:+UseConcMarkSweepGC选项后的默认新生代收集器，也可以使用-XX:+UseParNewGC选项来强制指定它。

ParNew收集器在单CPU的环境中绝对不会有比Serial收集器更好的效果，甚至由于存在线程交互的开销，该收集器在通过超线程技术实现的两个CPU的环境中都不能百分之百地保证可以超越Serial收集器。当然，随着可以使用的CPU的数量的增加，它对于GC时系统资源的有效利用还是很有好处的。它默认开启的收集线程数与CPU的数量相同，在CPU非常多的环境下，可以使用-XX:ParallelGCThreads参数来限制垃圾收集的线程数。

#### Parallel Scavenger 收集器

Parallel Scavenger收集器是一个新生代收集器，它也是使用复制算法的收集器，又是并行的多线程收集器……看上去和ParNew都一样，那它有什么特别之处呢？

Parallel Scavenger收集器的特点是它的关注点与其他收集器不同，CMS等收集器的关注点是尽可能地缩短垃圾收集时用户线程的停顿时间，而Parallel Scavenger收集器的目标则是达到一个可控制的吞吐量（Throughput）。所谓吞吐量就是CPU用于运行用户代码的时间与CPU总消耗时间的比值，即吞吐量=运行用户代码时间/(运行用户代码时间+垃圾收集时间)，虚拟机总共运行了100分钟，其中垃圾收集花掉1分钟，那吞吐量就是99%。

停顿时间越短就越是和需要与用户交互的程序，良好的响应速度能提升用户体验，而高吞吐量则可以高效率地利用CPU时间，尽快完成程序的运算任务，主要适合在后台运算而不需要太多交互的任务。

自适应调节策略也是Parallel Scavenger收集器与ParNew收集器的一个重要区别。开启-XX:+UseAdaptiveSizePolicy后，就不需要手工指定新生代的大小（-Xmn）、Eden与Survivor区的比例（-XX:SuvivorRatio）、晋升老年代对象年龄（-XX:PretenureSizeThreshold）等细节参数了，虚拟机会根据当前系统的运行情况收集性能监控信息，动态调整这些参数以提供最合适的停顿时间或者最大的吞吐量，这种调节方式称为GC自适应的调节策略（GC Ergonomics）。

#### Serial Old 收集器

Serial Old是Serial收集器的老年代版本，它同样是一个单线程收集器，使用“标记-整理”算法。这个收集器的主要意义也是在于给Client模式下的虚拟机使用。

如果在Server模式下，那么他主要还有两大用途：

- 一种用途是在JDK 1.5以及之前的版本中与Parallel Scavenger收集器搭配使用；（需要说明一下，Parallel Scavenger收集器架构中本身有PS MarkSweep收集器来进行老年代收集，并非直接使用了Serial Old收集器，但是这个PS MarkSweep收集器与Serial Old的实现非常接近，所以在官方的许多资料中都是直接以Serial Old代替PS MarkSweep进行讲解）
- 另一种用途就是作为CMS收集器的后备预案，在并发收集发生Concurrent Mode Failure时使用。

这两点都将在后面的内容中详细讲解。

#### Parallel Old 收集器

Parallel Old是Parallel Scavenger收集器的老年代版本，使用多线程和“标记-整理”算法。这个收集器是在JDK1.6中才开始提供的，在此之前，新生代的Parallel Scavenger收集器一直处于比较尴尬的状态。原因是，如果新生代选择了Parallel Scavenger收集器，老年代除了Serial Old（PS MarkSweep）收集器外别无选择（还记得上面说过Parallel Scavenger收集器无法与CMS收集器配合工作吗？）。由于老年代Serial Old 收集器在服务端应用性能上的“拖累”，使用了Parallel Scavenger收集器也未必能在整体应用上获得吞吐量最大化的效果，由于单线程的老年代收集中无法充分利用服务器多CPU的处理能力，在老年代很大而且硬件比较高级的环境中，这种组合的吞吐量甚至还不一定有ParNew加CMS组合“给力”。

直到Parallel Old收集器出现后，“吞吐量优先”收集器终于有了比较名副其实的应用组合，在注重吞吐量以及CPU资源敏感的场合，都可以优先考虑Parallel Scavenge加Parallel Old 收集器。

> Parallel Scavenger / Parallel Old 收集器：
>
> 新生代：多个GC线程
>
> 老年代：多个GC线程

#### CMS收集器

CMS（Concurrent Mark Sweep）收集器是一种以获取最短回收停顿时间为目标的收集器。目前很大一部分的Java应用集中在互联网或者B/S系统的服务端上，这类应用尤其重视服务的响应速度，希望系统停顿时间最短，以给用户带来较好的体验。CMS收集器就非常符合这类应用的需求。

从名字（包含“Mark Sweep”）上就可以看出，CMS收集器是基于“标记-清除”算法实现的，它的运作过程相对于前面几种收集器来说复杂一些，整个过程分为4个步骤，包括：

- 初始标记（CMS initial mark）
- 并发标记（CMS concurrent mark）
- 重新标记（CMS remark）
- 并发清除（CMS concurrent sweep）

其中，初始标记、重新标记这两个步骤仍然需要“Stop The World”。初始标记仅仅只是标记一下GC Roots能直接关联到的对象，速度很快，并发标记阶段就是进行GC Roots Tracing的过程，而重新标记阶段则是为了修正并发标记期间因用户程序继续运作而导致标记产生变动的那一部分对象的标记记录，这个阶段的停顿时间一般会比初始标记稍长一些，但远比并发标记的时间短。 

由于整个过程中耗时最长的并发标记和并发清除过程收集器线程都可以与用户线程一起工作，所以，从总体上来说，CMS收集器的内存回收过程是与用户线程一起并发执行的。

CMS是一款优秀的收集器，它的主要优点在名字上已经体现出来了：并发收集、低停顿，Sun公司的一些官方文档中也称之为并发低停顿收集器（Concurrent Low Pause Collector）。但是CMS还远达不到完美的程度，它有以下3个明显的缺点：

- CMS收集器对CPU资源非常敏感。其实，面向并发设计的程序都对CPU资源比较敏感。在并发阶段，它虽然不会导致用户线程停顿，但是会因为占用了一部分线程（或者说CPU资源）而导致应用程序变慢，总吞吐量会降低。
- CMS收集器无法处理浮动垃圾（Floating Garbage），可能出现“Concurrent Mode Failure”失败而导致另一次Full GC的产生。由于CMS并发清理阶段用户进程还在运行着，伴随程序运行自然就还会有新的垃圾不断产生，这一部分垃圾出现在标记过程之后，CMS无法在当次收集中处理掉它们，只好留待下一次GC时再清理掉。这一部分垃圾就称为“浮动垃圾”。也是由于在垃圾收集阶段用户进程还需要运行，那也就还需要预留有足够的内存空间给用户进程使用，因此CMS收集器不能像其他收集器那样等到老年代几乎完全被填满再进行收集，需要预留一部分空间提供并发收集时的程序运作使用。
- CMS是一款基于“标记-清除”算法实现的收集器，这意味着收集结束时会有大量空间碎片产生。空间碎片过多时，将会给大对象分配带来很大麻烦，往往会出现老年代还有很大空间剩余，但是无法找到足够大的连续空间来分配当前对象，不得不提前触发一次Full GC。为了解决这个问题，CMS提供了一个-XX:+UseCMSCompactAtFullCollection开关参数（默认就是开启的），用于在CMS收集器顶不住要进行FullGC时开启内存碎片的合并整理过程。

#### G1收集器

G1（Garbage-First）收集器是当今收集器技术发展的最前沿成果之一，早在JDK1.7刚刚确立项目目标，Sun公司给出的JDK 1.7 RoadMap 里面，它就被视为JDK 1.7中HotSpot虚拟机的一个重要的进化特征。直至JDK 7u4，Sun公司才认为它达到足够成熟的商用程度，移除了“Experimental”的标识。

G1是一款面向服务端应用的垃圾收集器。HotSpot开发团队赋予它的使命是（在比较长期的）未来可以替换掉JDK 1.5中发布的CMS收集器。与其他GC收集器相比，G1具备如下特点。

- 并行与并发：G1能充分利用多CPU、多核环境下的硬件优势，使用多个CPU（CPU或者CPU核心）来缩短Stop-The-World停顿的时间，部分其他收集器原本需要停顿Java线程执行的GC动作，G1收集器仍然可以通过并发的方式让Java程序继续执行。
- 分代收集：与其他收集器一样，分代概念在G1中依然得以保留。虽然G1可以不需要其他收集器配合就能独立管理整个GC堆，但它能够采用不同的方式去处理新创建的对象和已经存活了一段时间、熬过多次GC的旧对象以获取更好的收集效果。
- 空间整合：与CMS的“标记-清理”算法不同，G1从整体来看是基于“标记-整理”算法实现的收集器，从局部（两个Region之间）上来看是基于“复制”算法实现的，但无论如何，这两种算法都意味着G1运作期间不会产生内存空间碎片，收集后能提供规整的可用内存。这种特性有利于程序长时间运行，分配大对象时不会因为无法找到连续内存空间而提前触发下一次GC。
- 可预测的停顿：这是G1相对于CMS的另一大优势，降低停顿时间是G1和CMS共同的关注点，但G1除了追求低停顿外，还能建立可预测的停顿时间模型，能让使用者明确指定在一个长度为M毫秒的时间片段内，消耗在垃圾收集上的时间不得超过N毫秒，这几乎已经是实时Java（RTSJ）的垃圾收集器的特征了。

在G1之前的其他收集器进行收集的范围都是整个新生代或者老年代，而G1不再是这样。使用G1收集器时，Java堆的内存布局就与其他收集器有很大差别，它将整个Java堆划分为多个大小相等的独立区域（Region），虽然还保留有新生代和老年代的概念，但新生代和老年代不再是物理隔离的了，它们都是一部分Region（不需要连续）的集合。

G1收集器之所以能建立可预测的停顿时间模型，是因为它可以有计划地避免在整个Java堆中进行全区域的垃圾收集。G1跟踪各个Region里面的垃圾堆积的价值大小（回收所获得的空间大小以及回收所需时间的经验值），在后台维护一个优先列表，每次根据允许的收集时间，优先回收价值最大的Region（这也就是Garbage-First名称的来由）。这种使用Region划分内存空间以及有优先级的区域回收方式，保证了G1收集器在有限的时间内可以获取尽可能高的收集效率。

在G1收集器中，Region之间的对象引用以及其他收集器的新生代和老年代之间的对象引用，虚拟机都是使用Remembered Set来避免全堆扫描的。G1中每个Region都有一个与之对应的Remembered Set来避免全堆扫描的。G1中每个Region都有一个与之对应的Remembered Set，虚拟机发现程序在对Reference类型的数据进行写操作时，会产生一个Write Barrier暂时中断写操作，检查Reference引用的对象是否处于不同的Region之中（在分代的例子中就是检查是否老年代中的对象引用了新生代中的对象），如果是，便通过CardTable把相关引用信息记录到被引用对象所属的Region的Remembered Set之中。当进行内存回收时，在GC根节点的枚举范围中加入Remembered Set 即可保证不对全堆扫描也不会有遗漏。

如果不计算维护Remembered Set的操作，G1收集器的运作大致可划分为以下几个步骤：

- 初始标记（Initial Marking）
- 并发标记（Concurrent Marking）
- 最终标记（Final Marking）
- 筛选回收（Live Data Counting and Evacuation）

### 2.4 内存分配与回收策略

（参考《深入理解Java虚拟机：JVM高级特性与最佳实践（第二版）》 3.6 内存分配与回收策略 P114/457）

对象的内存分配，往大方向讲，就是在堆上分配（但有可能经过JIT编译后被拆散为标量类型并间接地栈上分配），对象主要分配在新生代的Eden区上，如果启动了本地线程分配缓冲，将按线程优先在TLAB上分配。少数情况下也可能会直接分配在老年代中，分配的规则并不是百分之百固定的，其细节取决于当前使用的是哪一种垃圾收集器组合，还有虚拟机中与内存相关的参数的设置。

接下来我们将会讲解几条最普遍的内存分配规则，并通过代码去验证这些规则。本节下面的代码在测试时使用Client模式虚拟机运行，没有手工指定收集器组合，换句话说，验证的是在使用Serial/Serial Old 收集器下（ParNew/Serial Old收集器组合的规则也基本一致）的内存分配和回收策略。

#### 对象优先在Eden分配

大多数情况下，对象在新生代Eden区中分配。当Eden区没有足够空间进行分配时，虚拟机将发起一次Minor GC。

>新生代GC（Minor GC）：指发生在新生代的垃圾收集动作，因为Java对象大多都具备朝生夕灭的特性，所以Minor GC非常频繁，一般回收速度也较快。
>
>老年代GC（Major GC / Full GC）：指发生在老年代的GC，出现了Major GC，经常会伴随至少一次的Minor GC（但非绝对的，在Parallel Scavenger收集器的收集策略里就有直接进行Major GC的策略选择过程）。Major GC 的速度一般会比Minor GC慢10倍以上。

#### 大对象直接进入老年代

所谓的大对象是指，需要大量连续内存空间的Java 对象，最典型的大对象就是那种很长的字符串以及数组（笔者列出的例子中的byte[]数组就是典型的大对象）。大对象对虚拟机的内存分配来说就是一个坏消息（替Java 虚拟机抱怨一句，比遇到一个大对象更加坏的消息就是遇到一群“朝生夕灭”的“短命大对象”，写程序的时候应当避免），经常出现大对象很容易导致内存还有不少空间时就提前触发垃圾收集以获取足够的连续空间来“安置”它们。

#### 长期存活对象将进入老年代

既然虚拟机采用了分代收集的思想来管理内存，那么内存回收时就必须能识别哪些对象应放入新生代，哪些对象应放入老年代中。为了做到这点，虚拟机给每个对象定义了一个对象年龄（Age）计数器。如果对象在Eden出生并经过第一次Minor GC后仍然存活，并且能够被Survivor容纳的话，将被移动到Survivor空间中，并且对象年龄设为1.对象在Survivor区每熬过一次Minor GC，年龄就会增加1岁，当它的年龄增加到一定程度（默认为15岁），就会被晋升到老年代中。对象晋升老年代的年龄阈值，可以通过参数-XX:MaxTenuringThreshold设置。

#### 动态对象年龄判定

为了能更好的适应不同程序的内存状况，虚拟机并不是永远地要求对象的年龄必须达到了MaxTenuringThreshold才能晋升老年代，如果在Survivor空间中相同年龄所有对象大小的总和大于Survivor空间的一半，年龄大于或等于该年龄的对象就可以直接进入老年代，无需等到MaxTenuringThreshold中要求的年龄。

#### 空间分配担保

在发生Minor GC之前，虚拟机会先检查老年代最大可用的连续空间是否大于新生代所有对象总空间，如果这个条件成立，那么Minor GC可以确保是安全的。如果不成立，则虚拟机会查看HandlePromotionFailure设置值是否允许担保失败。如果允许，那么会继续检查老年代最大可用空间是否大于历次晋升到老年代对象的平均大小，如果大于，将尝试着进行一次Minor GC，尽管这次Minor GC是有风险的；如果小于，或者HandlePromotionFailure设置不允许冒险，那这时也要改为进行一次Full GC。

下面解释一下“冒险”是冒了什么风险，前面提到过，新生代使用复制收集算法，但为了内存利用率，只使用其中一个Survivor空间来作为轮换备份，因此当出现大量对象在Minor GC后仍然存活的情况（最极端的情况就是内存回收后新生代中所有对象都存活），就需要老年代进行分配担保，把Survivor无法容纳的对象直接进入老年代。

一共有多少对象会活下来在实际完成内存回收之前是无法明确知道的，所以只好取之前每次回收晋升到老年代对象容量的平均大小值作为经验值，与老年代的剩余空间进行比较，决定是否进行Full GC 来让老年代腾出更多空间。

取平均值来进行比较其实仍然是一种动态概率的手段，也就是说，如果某次Minor GC存活后的对象突增，远远高于平均值的话，依然会导致担保失败（HandlePromotionFailure）。如果出现了HandlePromotionFailure失败，那就只好在失败后重新发起一次Full GC。虽然担保失败时绕的圈子是最大的，但大部分情况下还是会将HandlePromotionFailure开关打开，避免Full GC过于频繁。

JDK 6 Update 24之后，HandlePromotionFailure参数不会再影响到虚拟机的空间分配担保策略。JDK 6 Update 24之后的规则变为只要老年代的连续空间大于新生代对象总大小或者历次晋升的平均大小就会进行Minor GC，否则将进行Full GC



## 3. 虚拟机类加载机制

### 3.1 类加载的时机



### 3.2 类加载的过程



### 3.3 类加载器



## 4.虚拟机调优



## 5. 虚拟机性能监控与故障处理工具



## 6. Java内存模型

# IO相关



## 1. NIO

（参考《深入分析Java Web技术内幕 修订版》2.4 NIO的工作方式 P43（68/491））

### 1.1 BIO带来的挑战

BIO即阻塞I/O，不管是磁盘I/O还是网络I/O,数据在写入OutputStream或者从InputStream读取时都有可能会阻塞，一旦有阻塞，线程将会失去CPU的使用权，这在当前的大规模访问量和有性能要求的情况下是不能被接受的。虽然当前的网络I/O有一些解决办法，如一个客户端对应一个处理线程，出现阻塞时只是一个线程阻塞而不会影响其他线程工作，还有为了减少系统线程的开销，采用线程池的办法来减少线程创建和回收的成本。

但是在一些使用场景下仍然是无法解决的。如当前一些需要大量HTTP长连接的情况，像淘宝现在使用的Web 旺旺，服务端需要同时保持几百万的HTTP连接，但并不是每时每刻这些连接都在传输数据，在这种情况下不可能同时创建这么多线程来保持连接。即使线程的数量不是问题，也仍然有一些问题是无法避免的，比如我们想给某些客户端更高的服务优先级时，很难通过设计线程优先级来完成。另外一种情况是，每个客户端的请求在服务端可能需要访问一些竞争资源，这些客户端在不同线程中，因此需要同步，要实现这种同步操作远比用单线程复杂得多。

以上这些情况都说明，我们需要另外一种新的I/O操作方式。

### 1.2 NIO的工作机制

有两个关键类：Channel和Selector，它们是NIO中的两个核心概念。

Channel比Socket更加具体，可以把它比作某种具体的交通工具，如汽车或高铁，而可把Selector比作一个车站的车辆运行调度系统，也就是它可以轮询每个Channel的状态。这里还有一个Buffer类，它也比Stream更加具体，我们可以将它比作车上的座位。与Stream不同，Stream只能代表一个座位，至于是什么座位由你自己去想象，也就是你不知道自己上的是什么车，因为你并不能选择。而这些信息都已经被封装在了运输工具（Socket）里面。

NIO引入了Channel、Buffer和Selector，就是想把这些信息具体化，让程序员有机会去控制它们。例如我们可以控制Buffer的容量、是否扩容以及如何扩容。

理解了这些概念之后，我们看一下它们实际上是如何工作的，下面是一段典型的NIO代码：

~~~java
public void selector() throws IOException{
    ByteBuffer buffer = ByteBuffer.allocate(1024);
    Selector selector = Selector.open();
    ServerSocketChannel ssc = ServerSocketChannel.open();
    ssc.configureBlocking(false);//设置为非阻塞方式
    ssc.socket().bind(new InetSocketAddress(8080));
    ssc.register(selector, SelectionKey.OP_ACCEPT);//注册监听的事件
    while(true){
        Set selectedKeys = selector.selectedKeys();//取得所有key集合
        Iterator it = selectedKeys.iterator();
        while(it.hasNext()){
            SelectionKey key = (SelectionKey) it.next();
            if((key.readyOps() & SelectionKey.OP_ACCEPT)==SelectionKey.OP_ACCEPT){
                ServerSocketChannel ssChannel = (ServerSocketChannel)key.channel();
                SocketChannel sc = ssChannel.accept();//接收到服务端的请求
                sc.configureBlocking(false);
                sc.register(selector,SelectionKey.OP_READ);
                it.remove();
            }else if((key.readyOps() & SelectionKey.OP_READ) == SelectionKey.OP_READ) {
                SocketChannel sc = (SocketChannel) key.channel();
                while(true){
                    buffer.clear();
                    int n = sc.read(buffer);//读取数据
                    if(n<=0){
                        break;
                    }
                    buffer.flip();
                }
                it.remove();
            }
        }
    }
}
~~~

调用Selector的静态工厂创建一个选择器，创建一个服务端的Channel，绑定到一个Socket对象，并把这个通信信道注册到选择器上，把这个通信信道设置为非阻塞模式。然后就可以调用Selector的selectedKeys方法来检查已经注册在这个选择器上的所有通信信道是否有需要的事件发生，如果有某个事件发生，将会返回所有的SelectionKey，通过这个对象的Channel方法就可以取得这个通信信道对象，从而读取通信的数据，而这里读取的数据是Buffer，这个Buffer是我们可以控制的缓冲器。

在上面的这段程序中，将Server端的监听连接请求和处理请求的事件放在一个线程中，但是在事件应用中，我们通常会把它们放在两个线程中：一个线程专门负责监听客户端的连接请求，而且是以阻塞方式执行的；另一个线程专门负责处理请求，这个专门处理请求的线程才会真正采用NIO的方式，像Web服务器Tomcat和Jetty都是使用这个处理方式。

### 1.3 Buffer的工作方式

下面讨论Buffer如何接受和写出数据。

可以把Buffer简单地理解为一组基本数据类型的元素列表，它通过几个变量来保存这个数据的当前位置状态，也就是有4个索引：

- capacity（缓冲区数组的总长度）
- position（下一个要操作的数据元素的位置）
- limit（缓冲区数组中不可操作的下一个元素的位置，limit<=capacity）
- mark（用于记录当前position的前一个位置或者默认是0）

我们通过ByteBuffer.allocate(11)方法创建了一个11个byte的数组缓冲区，初始状态position的位置为0,capacity和limit默认都是数组长度。当我们写入5个字节时，position位置变化为5,limit和capacity不变。

这时候我们需要将缓冲区的5个字节数据写入Channel通信信道，所以我们调用byteBuffer.flip()方法，数组的状态position变成0，limit变为5，capacity不变。

这时底层操作系统就可以从缓冲区中正确读取这5个字节数据并发送出去了。在下一次写数据之前我们再调一下clear()方法，缓冲区的索引状态又回到初始位置。

这里还要说明一下mark，当我们调用mark()方法时，它将记录当前position的前一个位置，当我们调用reset时，position将恢复mark记录下来的值。

还有一点需要说明，通过Channel获取的I/O数据首先要经过操作系统的Socket缓冲区，再将数据复制到Buffer中，这个操作系统缓冲区就是底层TCP所关联的RecvQ或者SendQ队列，从操作系统缓冲区到用户缓冲区复制数据比较耗性能，Buffer提供了另外一种直接操作操作系统缓冲区的方式，即ByteBuffer.allocateDirector(size)，这个方法返回的DirectByteBuffer就是与底层存储空间关联的缓冲区，它通过Native代码操作非JVM堆的内存空间。每次创建或者释放的时候都调用一次System.gc()。注意，在使用DirectByteBuffer时可能会引起JVM内存泄露问题。

### 1.4 NIO的数据访问方式

NIO提供了比传统的文件访问方式更好的方法，NIO有两个优化方法：一个是FileChannel.transferTo、FileChannel.transferFrom；另一个是FileChannel.map。

FileChannel.transferXXX与传统的访问文件方式相比可以减少数据从内核到用户空间的复制，数据直接在内核空间中移动，在Linux中使用sendfile系统调用。

FileChannel.map将文件按照一定大小块映射为内存区域，当程序访问这个内存区域时将直接操作这个文件数据，这种方式省去了数据从内核空间向用户空间复制的损耗。这种方式适合对大文件的只读性操作，如大文件的MD5校验。但是这种方式是和操作系统的底层I/O实现相关的。

# Spring相关

## 1. AOP

### 1.1 AOP概念

### 1.2 AOP原理

### 1.3 cglib

（参考《Spring源码深度解析（第二版）》7.2.1 注册AnnotationAwareAspectJAutoProxyCreator P187/443）

proxy-target-class: SpringAOP部分使用JDK动态代理或者cglib来为目标对象创建代理（建议尽量使用JDK的动态代理）。如果被代理的目标对象实现了至少一个接口，则会使用JDK动态代理。所有该目标类型实现的接口都将被代理。若该目标对象没有实现任何接口，则创建一个cglib代理。

如果你希望强制使用cglib代理（例如希望代理目标对象的所有方法，而不只是实现自接口的方法），那也可以。但是需要考虑一下两个问题：

- 无法通知（advise）Final方法，因为它们不能被覆写。
- 你需要将cglib二进制发行包放在classpath下面。

与之相比，JDK本身就提供了动态代理，强制使用cglib代理需要将\<aop:config>的proxy-target-class属性设为true。

当需要使用cglib代理和@AspectJ自动代理支持，可以将\<aop:aspectj-autoproxy>的proxy-target-class属性设置为true。

而实现使用的过程中才会发现细节问题的差别：

- JDK动态代理：其代理对象必须是某个接口的实现，它是通过在运行期间创建一个接口的实现类来完成对目标对象的代理。
- cglib代理：实现原理类似于JDK动态代理，只是它在运行期间生成的代理对象是针对目标类扩展的子类。cglib是高效的代码生成包，底层是依靠ASM（开源的Java字节码编辑类库）操作字节码实现的，性能比JDK强。



## 2. 事务

### 2.1 事务的隔离级别

事务的隔离级别（五个）：

- `ISOLATION_DEFAULT`：默认事务隔离级别
- `ISOLATION_READ_UNCOMMITTED`：读未提交
- `ISOLATION_READ_COMMITTED`：读已提交
- `ISOLATION_REPEATABLE_READ`：重复读
- `ISOLATION_SERIALIZABLE`：串行化

> 回顾：事务并发存在的问题
>
> - 脏读：一个事务里，读取到另外一个事务未提交的数据
> - 不可重复读：一个事务里，多次读取的数据不一致：受到其它update的干扰了
> - 虚读/幻读：一个事务里，多次读取的数据不一致：受到其它insert、delete干扰了
>
> 解决事务并发问题的方案：通过设置不能的隔离级别
>
> - read uncommited：读未提交，什么问题都没有解决
> - read committed：读已提交，解决了脏读。 Oracle默认隔离级别
> - repeatable read：重复读，解决了脏读、不可重复读。MySql默认隔离级别
> - serializable：串行化，解决了所有问题

### 2.2 事务的传播行为

事务的传播行为（七个）：用于解决业务方法调用业务方法时，事务的统一性问题的

> 以下三个，是要当前事务的

- `PROPAGATION_REQUIRED`：需要有事务。默认
  - 如果已有事务，就使用这个事务
  - 如果没有事务，就创建事务。
- `PROPAGATION_SUPPORTS`：支持事务
  - 如果有事务，就使用当前事务，
  - 如果没有事务，就以非事务方式执行（没有事务）
- `PROPAGATION_MANDATORY`：强制的
  - 如果有事务，就使用当前事务
  - 如果没有事务，就抛异常

> 以下三个，是不要当前事务的

- `PROPAGATION_REQUIRES_NEW`：新建的
  - 如果有事务，就把事务挂起，再新建事务
  - 如果没有事务，新建事务
- `PROPAGATION_NOT_SUPPORTED`：不支持的
  - 如果有事务，就把事务挂起，以非事务方式执行
  - 如果没有事务，就以非事务方式执行
- `PROPAGATION_NEVER`：非事务的
  - 如果有事务，就抛异常
  - 如果没有事务，就以非事务方式执行

> 最后一个，是特殊的

- `PROPAGATION_NESTED`：嵌套的
  - 如果有事务，就在事务里再嵌套一个事务执行
  - 如果没有事务，就是类似`REQUIRED`的操作



## 3. Bean的生命周期



## 4. IOC



# 计算机网络相关

## 1. TCP

### 1.1 TCP最主要的特点

（参考《计算机网络（第7版）》谢希仁 5.3.1 TCP最主要的特点 P221/464）

- TCP是**面向连接的运输层协议**。也就是说，应用程序在使用TCP协议之前，必须先建立TCP连接。在传送数据完毕后，必须释放已经建立的TCP连接。也就是说，应用进程之间的通信好像在“打电话”：通话前要先拨号建立连接，通话结束后要挂机释放连接。
- 每一条TCP连接只能有两个**端点（endpoint）**，每一条TCP连接只能是**点对点**的（一对一）。
- TCP提供**可靠交付**的服务。通过TCP连接传送的数据，无差错、不丢失、不重复，而且按序到达。
- TCP提供**全双工通信**。TCP允许通信双方的应用进程在任何时候都能发送数据。TCP连接的两端都设有发送缓存和接收缓存，用来临时存放双向通信的数据。在发送时，应用程序在把数据传送给TCP的缓存后，就可以做自己的事，而TCP在合适的时候把数据发送出去。在接收时，TCP把收到的数据放入缓存，上层的应用进程在合适的时候读取缓存中的数据。
- **面向字节流**。TCP中的“流”（stream）指的是**流入到进程或从进程流出的字节序列**。“面向字节流”的含义是：虽然应用程序和TCP的交互是一次一个数据块（大小不等），但TCP把应用程序交下来的数据仅仅看成是一连串的**无结构的字节流**。TCP不保证接受方应用程序所收到的数据块和发送方应用程序所发出的数据块具有对应大小的关系（例如，发送方应用程序交给发送方的TCP共10个数据块，但接收方的TCP可能只用了4个数据块就把收到的字节流交付上层的应用程序）。但接收方应用程序收到的字节流必须和发送方应用程序发出的字节流完全一样。当然，接收方的应用程序必须有能力识别收到的字节流，把它还原成有意义的应用层数据。

### 1.2 TCP的运输连接管理（三次握手、四次挥手）

（参考《计算机网络（第7版）》谢希仁 5.9 TCP的运输连接管理 P248/464）

TCP是面向连接的协议。运输连接是用来传送TCP报文的。TCP运输连接的建立和释放是每一次面向连接的通信中必不可少的过程。因此，运输连接就有三个阶段，即：**连接建立**、**数据传送**和**连接释放**。运输连接的管理就是使运输连接的建立和释放都能正常地进行。

在TCP连接建立过程中要解决一下三个问题：

1. 要使每一方能够确知对方的存在。
2. 要允许双方协商一些参数（如最大窗口值、是否使用窗口扩大选项和时间戳选项以及服务质量等）。
3. 能够对运输实体资源（如缓存大小、连接表中的项目等）进行分配。

#### 1.2.1 TCP的连接建立（三次握手）

TCP建立连接的过程叫做握手，握手需要在客户和服务器之间交换三个TCP报文段。

假定主机A运行的是TCP客户程序，而B运行的是TCP服务器程序。最初两端的TCP进程都处于CLOSED（关闭）状态。注意，在本例中，A主动打开连接，而B被动打开连接。

- 一开始，B的TCP服务器进程先创建传输控制块TCB，准备接受客户进程的连接请求。然后服务器进程就处于LISTEN（收听）状态，等待客户的连接请求。如有，即做出响应。
- A的TCP客户进程也是首先创建传输控制模块TCB。然后，在打算建立TCP连接时，向B发出连接请求报文段，这时首部中的同步位SYN=1，同时选择一个初始序号seq = x。TCP规定，SYN报文段（即SYN=1的报文段）不能携带数据，但要消耗掉一个序号，这时，TCP客户进程进入SYN-SENT（同步已发送）状态。
- B收到连接请求报文段后，如同意建立连接，则向A发送确认。在确认报文段中应把SYN位和ACK位都置1，确认号是ack = x+1，同时也为自己选择一个初始序号seq = y。请注意，这个报文段也不能携带数据，但同样要消耗掉一个序号。这时TCP服务器进程进入SYN-RCVD（同步收到）状态。
- TCP客户进程收到B的确认后，还要向B给出确认。确认报文段的ACK置1，确认号ack = y + 1，而自己的序号seq = x + 1。TCP的标准规定，ACK报文段可以携带数据。但**如果不携带数据则不消耗序号**，在这种情况下，下一个数据报文段的序号仍是seq = x+1。这时，TCP连接已经建立，A进入ESTABLISHED（已建立连接）状态。
- 当B收到A的确认后，也进入ESTABLISHED状态。

上面给出的连接建立过程叫做**三报文握手**（三次握手）。请注意，B发送给A的报文段，也可以拆成两个报文段。可以先发送一个确认报文段（ACK = 1， ack = x+1），然后在发送一个同步报文段（SYN = 1，seq = y）。这样的过程就变成了**四报文握手**，但效果是一样的。

为什么A最后还要发送一次确认呢？这主要是为了防止已失效的连接请求报文段突然又传送到了B因而产生错误。

所谓“已失效的连接请求报文段”是这样产生的。考虑一种正常情况，A发出连接请求，但因连接请求报文丢失而未收到确认。于是A再重传一次连接请求。后来收到了确认，建立了连接。数据传输完毕后，就释放了连接。A共发送了两个连接请求报文段，其中第一个丢失，第二个到达了B，没有“已失效的请求报文段”。

现假定出现一种异常情况，即A发出的第一个连接请求报文并没有丢失，而是在某些网络结点长时间滞留了，以致延误到连接释放以后的某个时间才到达B。本来这是一个早已失效的报文段。但B收到此失效的连接请求报文段后，就误认为A又发出了一次新的连接请求。于是就向A发出确认报文段，同意建立连接。假定不采用报文握手，那么只要B发出确认，新的连接就建立了。

由于现在A并没有发出建立连接的请求，因此不会理睬B的确认，也不会向B发送数据。但B却认为新的运输连接已经建立了，并一直等待A发来数据。B的许多资源就这样白白浪费了。

采用三报文握手的办法，可以防止上述现象的发生。例如在刚才的异常情况下，A不会像B的确认发出确认。B由于收不到确认，就知道A并没有要求建立连接。

#### 1.2.2 TCP的连接释放（四次挥手）

TCP连接释放过程比较复杂，我们仍结合双方状态的改变来阐明连接释放的过程。

- 数据传输后，通信双方都可释放连接。现在A和B都处于ESTABLISHED状态。A的应用进程先向其TCP发出连接释放报文段，并停止再发送数据，主动关闭TCP连接。A把连接释放报文段首部的终止控制位FIN置1，其序号seq = u,它等于前面已传送过的数据的最后一个字节的序号加1.这时A进入FIN-WAIT-1（终止等待1）状态，等待B的确认。请注意，TCP规定，FIN报文段即使不携带数据，它也消耗掉一个序号。
- B收到连接释放报文段后即发出确认，确认号是ack = u+1，而这个报文段自己的序号是v，等于B之前已传送过的数据的最后一个字节的序号加1.然后B进入CLOSE-WAIT（关闭等待）状态。TCP服务器进程这时应通知高层应用进程，因而从A到B这个方向的连接就释放了，这时的TCP连接处于**半关闭（half-close）**状态，即A已经没有数据要发送了，但B若发送数据，A仍要接受。也就是说，从B到A这个方向的连接并未关闭，这个状态可能会持续一段时间。
- A收到来自B的确认后，就进入FIN-WAIT-2（终止等待2）状态，等待B发出的连接释放报文段。
- 若B已经没有要向A发送的数据，其应用进程就通知TCP释放连接。这时B发出连接释放报文段必须使FIN=1.现假定B的序号为w（在半关闭状态B可能又发送了一些数据）。B还必须重复上次已发送过的确认号ack = u+1。这时B就进入LAST-ACK（最后确认）状态，等待A的确认。
- A在收到B的连接释放报文段后，必须对此发出确认。在确认报文段中把ACK置1，确认号ack = w+1，而自己的序号是seq = u+1(根据TCP标准，前面发送过的FIN报文段要消耗一个序号)。然后进入到TIME-WAIT（时间等待）状态。请注意，现在TCP连接还没有释放掉。必须经过**时间等待计时器（TIME-WAIT timer）**设置的时间2MSL后，A才进入到CLOSED状态。时间MSL叫做**最长报文段寿命（Maximum Segment Lifetime）**，RFC 793建议设为2分钟。但这个完全是从工程上来考虑的，对于现在的网络，MSL=2分钟可能太长了一些。因此TCP允许不同的实现可根据具体情况使用更小的MSL值。因此，从A进入到TIME-WAIT状态后，要经过4分钟才能进入到CLOSED状态，才能开始建立下一个新的连接。当A撤销相应的传输控制块TCB后，就结束了这次的TCP连接。

为什么A在TIME-WAIT状态必须等待2MSL时间呢？这有两个理由

第一，为了保证A发送的最后一个ACK报文段能到达B。这个ACK报文段有可能丢失，因而使处在LAST-ACK状态的B收不到对已发送FIN+ACK报文段的确认。B会超时重传这个FIN+ACK的报文段，而A能在2MSL时间内收到这个重传的FIN+ACK报文段。接着A重传一次确认，重新启动2MSL计时器，最后，A和B都正常进入到CLOSED状态。如果A在TIME-WAIT状态下不等待一段时间，而是在发送完ACK报文段后立即释放连接，那么就无法收到B重传的FIN+ACK报文段，因而也不会再发送一次确认报文段。这样，B就无法按照正常步骤进入CLOSED状态。

第二，防止上一节提到的“已失效的连接请求报文段”出现在本连接中。A在发送完最后一个ACK报文段后，再经过时间2MSL，就可以使本连接持续的时间内所产生的所有报文段都从网络中消失。这样就可以使下一个新的连接中不会出现这种旧的连接请求报文段。

B只要收到了A发出的确认，就进入CLOSED状态。同样，B在撤销相对应的传输控制块TCB后，就结束了这次TCP连接。我们注意到，B结束TCP连接的时间要比A早一些。

上述的TCP连接释放过程是四报文握手（四次挥手）。

除时间等待计时器外，TCP还设有一个**保活计时器（keepalive timer）**。设想有这样的情况：客户已主动与服务器建立了TCP连接。但后来客户端的主机突然出故障。显然，服务器以后就不能再收到客户发来的数据。因此，应当有措施使服务器不要再白白等待下去。这就是使用保活计时器。服务器每收到一次客户的数据，就重新设置保活计时器，时间的设置通常是两小时。若两小时没有收到客户的数据，服务器就发送一个探测报文段，以后则每隔75秒钟发送一次。若一连发送10个探测报文段后仍无客户的响应，服务器就认为客户端出了故障，接着就关闭这个连接。

### 1.3 TCP的拥塞控制

（参考《计算机网络（第7版）》谢希仁 5.8 TCP的拥塞控制 P239/464）

#### 1.3.1 拥塞控制的一般原理

在计算机网络中的链路容量（即带宽）、交换结点中的缓存和处理机等，都是网络的资源。在某些时间，若对网络中某一资源的需求超过了该资源所能提供的可用部分，网络的性能就要变坏。这种情况就叫做**拥塞（congestion）**。可以把出现网络拥塞的条件写成如下的关系式：`∑对资源的需求>可用资源`

若网络有许多资源同时呈现供应不足，网络的性能就要变坏，整个网络的吞吐量将随输入负荷的增大而下降。

网络拥塞往往是由许多因素引起的。例如，当某个结点缓存的容量太小时，到达该节点的分组因无存储空间暂存而不得不被丢弃。现在设想该结点缓存的容量扩展到非常大，于是凡到达该结点的分组均可在结点的缓存队列中排队，不受任何限制。由于输出链路的容量和处理机的速度并未提高，因此在这队列中的绝大多数分组的排队等待时间将会大大增加，结果上层软件只好把它们进行重传（因为早就超时了）。由此可见，简单地扩大缓存的存储空间同样会造成网络资源的严重浪费，因而解决不了网络拥塞的问题。

又如，处理机处理的速率太慢可能引起网络的拥塞。简单地将处理机的速率提高，可能会使上述情况缓解一些，但往往又会将瓶颈转移到其他地方。问题的实质往往是整个系统的各个部分不匹配。只有所有的部分都平衡了，问题才会得到解决。

拥塞常常趋于恶化。如果一个路由器没有足够的缓存空间，它就会丢弃一些新到的分组。但当分组被丢弃时，发送这一分组的源点就会重传这一分组，甚至可能还要重传多次。这样会引起更多的分组流入网络和被网络中的路由器丢弃。可见拥塞引起的重传并不会缓解网络的拥塞，反而会加剧网络的拥塞。

拥塞控制与流量控制的关系密切，它们之间也存在着一些差别。所谓**拥塞控制**就是**防止过多的数据注入到网络中，这样可以使网络中的路由器或链路不致过载**。拥塞控制所要做的都有一个前提，就是**网络能够承受现有的网络负荷**。拥塞控制是一个**全局性的过程**，涉及到所有的主机、所有的路由器，以及降低网络传输性能有关的所有因素。但TCP连接的端点只要迟迟不能收到对方的确认信息，就猜想在当前网络中的某处很可能发生了拥塞，但这时却无法知道拥塞到底发生在网络的何处，也无法知道发生拥塞的具体原因。（是访问某个服务器的通信量过大？还是某个地区出现自然灾害？）

相反，**流量控制往往是指点对点通信量的控制**，是一个**端到端**的问题（接收端控制发送端）。流量控制所要做的就是抑制发送端发送数据的速率，以便使接收端来得及接收。

拥塞控制和流量控制之所以常常被弄混，是因为某些拥塞控制算法是向发送端发送控制报文，并告诉发送端，网络已出现麻烦，必须放慢发送速率。这点又和流量控制是很相似的。

#### 1.3.2 TCP的拥塞控制方法

TCP进行拥塞控制的算法有四种，即**慢开始（slow-start）**、**拥塞避免（congestion avoidance）**、**快重传（fast retransmit）**和**快恢复（fast recovery）**（见2009年9月公布的草案标准RFC 5681）。下面就介绍这些算法的原理。为了集中精力讨论拥塞控制，我们假定：

1. 数据是单方向传送的，对方只传送确认报文。
2. 接收方总是有足够大的缓存空间，因而发送窗口的大小由网络的拥塞程度来决定。

##### 慢开始

下面讨论的拥塞控制也叫做**基于窗口**的拥塞控制。为此，发送方维持一个叫做**拥塞窗口**cwnd（congestion window）的状态变量。拥塞窗口的大小取决于网络的拥塞程度，并且动态地在变化。**发送方让自己的发送窗口等于拥塞窗口**。

发送方控制拥塞窗口的原则是：只要网络没有出现拥塞，拥塞窗口就可以再增大一些，以便把更多的分组发送出去，这样就可以提高网络的利用率。但只要网络出现拥塞或者有可能出现拥塞，就必须把拥塞窗口减少一些，以减少注入到网络中的分组数，以便缓解网络出现的拥塞。

发送方又是如何知道网络发生了拥塞呢？我们知道，当网络发生拥塞时，路由器就要丢弃分组。因此只要发送方没有按时收到应该到达的确认报文，也就是说，只要出现了超时，就可以猜想网络可能出现了拥塞。现在通信线路的传输质量一般都很好，因传输出差错而丢弃分组的概率是很小的（远小于1%）。因此，判断网络拥塞的依据就是出现了超时。

下面将讨论拥塞窗口cwnd的大小是怎样变化的。我们从“慢开始算法”讲起。

**慢开始**算法的思路是这样的：当主机开始发送数据时，由于并不清楚网络的负荷情况，所以如果立即把大量字节数据字节注入到网络，那么就有可能引起网络发生拥塞。经验证明，较好的方法是先探测一下，即**由小到大逐渐增大发送窗口**，也就是说，**由小到大逐渐增大拥塞窗口数值**。

旧的规定是这样的：在刚刚开始发送报文段时，先把初始拥塞窗口cwnd设置为1至2个发送方的最大报文段SMSS（Sender Maximum Segment Size）的数值，但新的RFC 5681把初始拥塞窗口cwnd设置为不超过2至4个SMSS的数值。具体规定如下：

- 若SMSS>2190字节，则设置初始拥塞窗口cwnd = 2×SMSS字节，且**不得超过**2个报文段。
- 若（SMSS>1095字节）且（SMSS≤2190字节），则设置初始拥塞窗口cwnd = 3×SMSS字节，且**不得超过**3个报文段。
- 若SMSS≤1095字节，则设置初始拥塞窗口cwnd = 4×SMSS字节，且**不得超过**4个报文段。

可见这个规定就是限制初始拥塞窗口的字节数。

慢开始规定，在每收到一个**对新的报文段的确认**后，可以把拥塞窗口增加最多一个SMSS的数值。更具体些，就是`拥塞窗口cwnd每次的增加量 = min(N,SMSS)`

其中N是原先未被确认的、但现在被刚收到的确认报文段所确认的字节数。不难看出，当N<SMSS时，拥塞窗口每次的增加量要小于SMSS。

用这样的方法逐步增大发送方的拥塞窗口cwnd，可以使分组注入到网络的速率更加合理。下面用例子说明慢开始算法的原理。请注意，虽然实际上TCP是用字节数作为窗口大小的单位。但为叙述方便起见，我们**用报文段的个数作为窗口大小的单位**，这样可以使用较小的数字来阐明拥塞控制的原理。

在一开始发送方先设置cwnd = 1，发送第一个报文段M~1~，接收方收到后确认M~1~ 。发送方收到对M~1~ 的确认后，把cwnd从1增加到2，于是发送方接着发送M~2~ 和M~3~ 两个报文段。接收方收到后发回对M~2~ 和M~3~ 的确认。发送方每收到一个**对新报文段的确认**（重传的不算在内）就使发送方的拥塞窗口加1，因此发送方在收到两个确认后，cwnd就从2增大到4，并可发送M~4~ \~ M~7~ 共4个报文段。因此使用慢开始算法后，**每经过一个传输轮次（transmission round），拥塞窗口cwnd就加倍。**

这里我们使用了一个名词——**传输轮次**。一个传输轮次所经历的时间其实就是往返时间RTT（请注意，RTT并非是恒定的数值）。使用“传输轮次”是更加强调：把拥塞窗口cwnd所允许发送的报文段都连续发送出去，并收到了对已发送的最后一个字节的确认。例如，拥塞窗口cwnd的大小是4个报文段，那么这时的往返时间RTT就是发送方连续发送4个报文段，并收到这4个报文段的确认，总共经历的时间。

我们还要指出，慢开始的“慢”并不是指cwnd的增长速率慢，而是指在TCP开始发送报文段时先设置cwnd = 1，使得发送方在开始时只发送一个报文段（目的是试探一下网络的拥塞情况），然后再逐渐增大cwnd。这当然比设置大的cwnd值一下子把许多报文段注入到网络要“**慢得多**”。这对防止网络出现拥塞是一个非常好的方法。

在TCP的实际运行中，发送方只要收到一个对新报文段的确认，其拥塞窗口cwnd就立即加1，并可以立即发送新的报文段，而不需要等这个轮次中所有的确认都收到后再发送新的报文段。

##### 拥塞避免

为了防止拥塞窗口cwnd增长过大引起网络拥塞，还需要设置一个**慢开始门限**ssthresh状态变量（如何设置ssthresh，后面还要讲）。慢开始门限ssthresh的用法如下：

- 当cwnd<ssthresh时，使用上述的慢开始算法。
- 当cwnd>ssthresh时，停止使用慢开始算法而改用拥塞避免算法。
- 当cwnd=ssthresh时，既可以使用慢开始算法，也可以使用拥塞避免算法。

**拥塞避免**算法的思路是让拥塞窗口cwnd缓慢地增大，即每经过一个往返时间RTT就把发送方的拥塞窗口cwnd加1，而不是像慢开始阶段那样加倍增长。因此在拥塞避免阶段就有“**加法增大**”AI（Additive Increase）的特点。这表明在拥塞避免阶段，拥塞窗口cwnd**按线性规律缓慢增长**，比慢开始算法的拥塞窗口增长速率缓慢得多。

但请注意，“拥塞避免”并非完全能够避免了拥塞。“拥塞避免”是说把拥塞窗口控制为按线性规律增长，使网络比较不容易出现拥塞。

##### 快重传

当发送方一连收到3个对同一个报文段的重复确认（记为3-ACK）的情况，关于这个问题要解释如下。

有时，个别报文段会在网络中丢失，但实际上网络并未发生拥塞。如果发送方迟迟收不到确认，就会产生超时，就会误认为网络发生了拥塞，这就导致发送放错误地启动慢开始，把拥塞窗口cwnd又设置为1，因而降低了传输效率。

采用快重传算法可以让发送方**尽早知道发生了个别报文段的丢失**。快重传算法首先要求接收方不要等待自己发送数据时才进行捎带确认，而是要**立即发送确认**，即使收到了**失序的报文段**也要立即发出对已收到的报文段的重复确认。快重传算法规定，发送方只要**一连收到3个重复确认**，就知道接收方确实没收到报文段，因而应当**立即进行重传**（即“快重传”），这样就不会出现超时，发送方也就不会误认为出现了网络拥塞。使用快重传可以使整个网络的吞吐量提高约20%。

##### 快恢复

在快重传后，发送方知道现在只是丢失了个别报文段。于是不启动慢开始，而是执行**快恢复**算法。这是，发送方调整门限值ssthresh = cwnd/2，同时设置拥塞窗口cwnd = ssthresh，并开始执行拥塞避免算法。

TCP Reno版本，表示区别于老的TCP Tahao版本。

请注意，也有的快恢复实现是把快恢复开始时的拥塞窗口cwnd值再增大一些（增大3个报文段的长度），即等于新的ssthresh+3×MSS。这样做的理由是：既然发送方收到3个重复的确认，就表明有3个分组已经离开了网络。这3个分组不再消耗网络的资源而是停留在接收方的缓存中（接收方发送出3个重复的确认就证明了这个事实）。可见现在网络中并不是堆积了分组而是减少了3个分组。因此可以适当把拥塞窗口扩大些。

在拥塞避免阶段，拥塞窗口是按线性规律增大的，这常称为**加性增大**AI（Additive Increase）。而一旦出现超时或3个重复的确认，就要把门限值设置为当前拥塞窗口值的一半，并大大减少拥塞窗口的数值。这常称为“**乘性减小**”MD（Multiplicative Decrease）。两者合在一起就是所谓的AIMD算法。

采用这样的拥塞控制方法使得TCP的性能有明显的改进。

~~~mermaid
graph TD
连接建立-->slowstart[慢开始: 拥塞窗口cwnd=1按指数规律增大, cwnd>=ssthresh]
slowstart-->congestionAvoidance[拥塞避免: 拥塞窗口cwnd按线性规律增大]

slowstart--3个重复ACK-->3ACK[ssthresh = cwnd/2, cwnd = ssthresh]
congestionAvoidance--3个重复ACK-->3ACK
3ACK-->congestionAvoidance

congestionAvoidance-->连接终止

congestionAvoidance--超时-->timeout[ssthresh = cwnd/2, cwnd = 1]
slowstart--超时-->timeout
timeout-->slowstart
~~~

在这一节的开始我们就假定了接收方总是有足够大的缓存空间，因而发送窗口的大小由网络的拥塞程度来决定。但实际上接收方的缓存空间总是有限的。接收方根据自己的接受能力设定了接收方窗口rwnd，并把这个窗口值写入TCP首部中的窗口字段，传送给发送方。因此，**接收方窗口**又称为**通知窗口**（advertised window）。因此，从接收方对发送方的流量控制的角度考虑，**发送方的发送窗口一定不能超过对方给出的接收方窗口值rwnd**。

如果把本节所讨论的拥塞控制和接收方对发送方的流量控制一起考虑，那么很显然，发送方的窗口的上限值应当取为接收方窗口rwnd和拥塞窗口cwnd这两个变量中较小的一个，也就是说：`发送方窗口的上限值 = Min[rwnd, cwnd]`



## 2. SSL、TLS



## 3. HTTP

### 1.1 HTTP幂等设计



### 1.2 HTTP访问流程



### 1.3 HTTP状态码





# 操作系统相关

## 1. 进程间通信方式



## 2. 内存分配算法



## 3. 缓存淘汰算法



# Linux相关

## 1. I/O多路复用（select、poll、epoll）



## 2. Linux命令



# Java基础

## 1. 异常继承体系



## 2. 变量初始化顺序



# 编译原理相关

## 1. 编译型语言和解释型语言



# 数据库相关

## 1. MySQL引擎



### 1.1 InnoDB存储引擎

（参考《高性能MySQL（第三版）》 1.5.1  InnoDB存储引擎 P52/800）

InnoDB是MySQL的默认事务型引擎，也是最重要、使用最广泛的存储引擎。它被设计用来处理大量的短期（short-lived）事务，短期事务大部分情况是正常提交的，很少会被回滚。InnoDB的性能和自动崩溃恢复特性，使得它在非事务型存储的需求中也很流行。

- InnoDB的数据存储在表空间（tablespace）中，表空间是由InnoDB管理的一个黑盒子，由一系列的数据文件组成。在MySQL 4.1以后的版本中，InnoDB可以将每个表的数据和索引存放在单独的文件中。InnoDB也可以使用裸设备作为表空间的存储介质，但现代的文件系统使得裸设备不再是必要的选择。
- InnoDB采用MVCC来支持高并发，并且实现了四个标准的隔离级别。其默认级别是REPEATABLE READ（可重复读），并且通过间隙锁（next-key locking）策略防止幻读的出现。间隙锁使得InnoDB不仅仅锁定查询涉及的行，还会对索引中的间隙进行锁定，以防止幻影行的插入。
- InnoDB表是基于聚簇索引建立的。InnoDB的索引结构和MySQL的其他存储引擎有很大的不同，聚簇索引对主键查询有很高的性能，不过它的二级索引（secondary index，非主键索引）中必须包含主键列，所以如果主键列很大的话，其他的所有索引都会很大。因此，若表中的索引较多的话，主键应当尽可能的小。InnoDB的存储格式是平台独立的，也就是说可以将数据和索引文件从Intel平台复制到PowerPC或者Sun SPARC平台。
- InnoDB内部做了很多优化，包括从磁盘读取数据时采用的可预测性预读，能够自动在内存中创建hash索引以加速读操作的自适应哈希索引（adaptive hash index），以及能够加速插入操作的插入缓冲区（insert buffer）等。
- InnoDB通过一些机制和工具支持真正的热备份，Oracle提供的MySQL Enterprise Backup、Persona提供的开源的XtraBackup都可以做到这一点，MySQL的其他存储引擎不支持热备份，要获取一致性视图需要停止对所有表的写入，而在读写混合场景中，停止写入可能也意味着停止读取。



### 1.2 MyISAM存储引擎

（参考《高性能MySQL（第三版）》 1.5.2  MyISAM存储引擎 P53/800）

在MySQL 5.1及以前的版本，MyISAM是默认的存储引擎。MyISAM提供了大量的特性，包括全文索引、压缩、空间函数（GIS）等，但MyISAM不支持事务和行级锁，而且有一个毫无疑问的缺陷就是崩溃后无法安全恢复。对于只读的数据，或者表比较小、可以忍受修复（repair）操作，则依然可以继续使用MyISAM（但请不要默认使用MyISAM，而是应当默认使用InnoDB）。

MyISAM会将表存储在两个文件中：数据文件和索引文件，分别以.MYD和.MYI为扩展名。MyISAM表可以包含动态或者静态（长度固定）行。MySQL会根据表的定义来决定采用何种行格式。MyISAM表可以存储的行记录数，一般受限于可用的磁盘空间，或者操作系统中单个文件的最大尺寸。

#### MyISAM特性

作为MySQL最早的存储引擎之一，MyISAM有一些已经开发出来很多年的特性，可以满足用户的实际需求。

**加锁和并发**

MyISAM对整张表加锁，而不是针对行。读取时会对需要读到的所有表加共享锁，写入时则对表加排他锁。但是在表有读取查询的同时，也可以往表中插入新的记录（这被称为并发插入，CONCURRENT INSERT）。

**修复**

对于MyISAM表，MySQL可以手工或者自动执行检查和修复操作，但这里说的修复和事务恢复以及崩溃恢复是不同的概念。执行表的修复可能导致一些数据丢失，而且修复操作是非常慢的。可以通过CHECK TABLE mytable检查表的错误，如果有错误可以通过执行REPAIR TABLE mytable进行修复。另外，如果MySQL服务器已经关闭，也可以通过myisamchk命令行工具进行检查和修复操作。

**索引特性**

对于MyISAM表，即使是BLOB和TEXT等长字段，也可以基于其前500个字符创建索引。MyISAM也支持全文索引，这是一种基于分词创建的索引，可以支持复杂的查询。

**延迟更新索引键**

创建MyISAM表的时候，如果指定了DELAY_KEY_WRITE选项，在每次修改执行完成时，不会立刻将修改的索引数据写入磁盘，而是会写到内存中的键缓冲区（in-memory key buffer），只有在清理键缓冲区或者关闭表的时候才会将对应的索引块写入到磁盘。这种方式可以极大地提升写入性能，但是在数据库或者主机崩溃时会造成索引损坏，需要执行修复操作。延迟更新索引键的特性，可以在全局设置，也可以为单个表设置。

#### MyISAM压缩表

如果表在创建并导入数据以后，不会再进行修改操作，那么这样的表或许适合采用MyISAM压缩表。

可以使用myisampack对MyISAM表进行压缩（也叫做打包pack）。压缩表是不能进行修改的（除非先将表解除压缩，修改数据，然后再次压缩）。压缩表可以极大地减少磁盘空间占用，因此也可以减少磁盘I/O，从而提升查询性能。压缩表也支持索引，但索引也是只读的。

以现在硬件能力，对大多数应用场景，读取压缩表数据时的解压带来的开销影响并不大，而影响I/O带来的好处则要大得多。压缩时表中的记录是独立压缩的，所以读取单行的时候不需要去解压整个表（甚至也不解压行所在的整个页面）

#### MyISAM性能

MyISAM引擎设计简单，数据以紧密格式存储，所以在某些场景下的性能很好。MyISAM有一些服务器级别的性能扩展限制，比如对索引键缓冲区（key cache）的Mutex锁，MariaDB基于段（segment）的索引键缓冲区机制来避免该问题。但MyISAM最典型的性能问题还是表锁的问题，如果你发现所有的查询都长期处于“Locked”状态，那么毫无疑问表锁就是罪魁祸首。

### 1.3 MySQL内建的其他存储引擎

#### Archive引擎



#### Blackhole引擎



#### CSV引擎



#### Federated引擎



#### Memory引擎



#### Merge引擎



#### NDB集群引擎



## 2. ACID



## 3. 索引

### 3.1 索引的类型

（参考《高性能MySQL（第三版）》 5.1.1 索引的类型 P178/800）

#### B-Tree索引

当人们谈论索引的时候，如果没有特别指明类型，那多半说的是B-Tree索引，它使用B-Tree数据结构来存储数据。大多数MySQL引擎都支持这种索引。Archive引擎是一个例外。

我们使用术语“B-Tree”是因为MySQL在CREATE TABLE和其他语句中也使用该关键字。不过，底层的存储引擎也可能使用不同的存储结构，例如，NDB集群存储引擎内部实际上使用了T-Tree结构存储这种索引，即使其名字是BTREE；InnoDB则使用的是B+Tree。

可以使用B-Tree索引的查询类型。B-Tree索引适用于全键值、键值范围或键前缀查找。其中键前缀查找只适用于根据最左前缀的查找。

- **全值匹配**：全值匹配指的是和索引中的所有列进行匹配
- **匹配最左前缀**：只使用索引的第一列。
- **匹配列前缀**：也可以只匹配某一列的值的开头部分。
- **匹配范围值**
- **精确匹配某一列并范围匹配另外一列**
- **只访问索引的查询**

因为索引树中的节点是有序的，所以除了按值查找之外，索引还可以用于查询中的ORDER BY操作（按顺序查找）。一般来说，如果B-Tree可以按照某种方式查找到值，那么也可以按照这种方式用于排序。所以，如果ORDER BY子句满足前面列出的几种查询类型，则这个索引也可以满足对于的排序需求。

下面是一些关于B-Tree索引的限制：

- 如果不是按照索引的最左列开始查找，则无法使用索引。
- 不能跳过索引中的列。
- 如果查询中有某个列的范围查询，则其右边所有列都无法使用索引优化查找。



#### 哈希索引

哈希索引（hash index）基于哈希表实现，只有精确匹配索引所有列的查询才有效。

InnoDB引擎有一个特殊的功能叫做“自适应哈希索引（adaptive hash index）”。当InnoDB注意到某些索引值被使用得非常频繁时，它会在内存中基于B-Tree索引之上再创建一个哈希索引，这样就让B-Tree索引也具有哈希索引的一些优点，比如快速的哈希查找。这是一个完全自动的、内部的行为，用户无法控制或者配置，不过如果有必要，完全可以关闭该功能。



### 3.2 索引的优点

（参考《高性能MySQL（第三版）》 5.2 索引的类型 P188/800）

最常见的B-Tree索引，按照顺序存储数据，所以MySQL可以用来做ORDER BY和GROUP BY操作。因为数据是有序的，所以B-Tree也就会将相关的列值都存储在一起。最后，因为索引中存储了实际的列值，所以某些查询只使用索引就能够完成全部查询。据此特性，总结下来索引有如下三个优点：

1. 索引大大减小了服务器需要扫描的数据量
2. 索引可以帮助服务器避免排序和临时表
3. 索引可以将随机I/O变为顺序I/O

Lahdenmaki和Leach在Relational Database Index Design and the Optimizers一书中介绍了如何评价一个索引是否适合某个查询的“三星系统”（three-star system）：索引将相关的记录放到一起则获得一星；如果索引中的数据顺序和查找中的排列顺序一致则获得二星；如果索引中的列包含了查询中需要的全部列则获得“三星”。



### 3.3 高性能的索引策略

（参考《高性能MySQL（第三版）》 5.3 高性能的索引策略 P189/800）

#### 独立的列

如果查询中的列不是独立的，则MySQL就不会使用索引。“独立的列”是指索引列不能是表达式的一部分，也不能是函数的参数。

例如，下面这个查询无法使用actor_id列的索引：`mysql> SELECT actor_id FROM sakila.actor WHERE actor_id + 1 = 5;`

我们应该养成简化WHERE条件的习惯，始终将索引列单独放在比较符号的一侧。

下面是另一个常见的错误：`mysql> SELECT ... WHERE TO_DAYS(CURRENT_DATE) - TO_DAYS(date_col) <= 10;`



## 4. MySQL分布式锁、MVCC

（参考《高性能MySQL（第三版）》 1.4  多版本并发控制 P48/800）

MySQL的大多数事务型存储引擎实现的都不是简单的行级锁。基于提升并发性能的考虑，它们一般都同时实现了多版本并发控制（MVCC）。不仅是MySQL，包括Oracle、PostgreSQL等其他数据库系统也都实现了MVCC，但各自的实现机制不尽相同，因为MVCC没有一个统一的实现标准。

MVCC的实现，是通过保存数据在某个时间点的快照来实现的。也就是说，不管需要执行多长时间，每个事务看到的数据都是一致的。根据事务开始时间的不同，每个事务对同一张表，同一时刻看到的数据可能是不一样的。

前面说到不同存储引擎的MVCC实现是不同的，典型的有乐观（optimistic）并发控制和悲观（pessimistic）并发控制。下面我们通过InnoDB的简化版行为来说明MVCC是如何工作的。

InnoDB的MVCC是通过在每行记录后面保存两个隐藏的列来实现的。这两个列，一个保存了行的创建时间，一个保存行的过期时间（或删除时间）。当然存储的并不是实际的时间值，而是系统版本号（system version number）。每开始一个新的事务，系统版本号都会自动递增。事务开始时刻的系统版本号会作为事务的版本号，用来和查询到每行记录的版本号进行比较。下面看一下在REPEATABLE READ隔离级别下，MVCC具体是如何操作的。

**SELECT**

InnoDB会根据一下两个条件检查每行目录：

- InnoDB只查找版本早于当前事务版本的数据行（也就是，行的系统版本号小于或等于事务的系统版本号），这样可以确保事务读取的行，要么是在事务开始前已经存在的，要么是事务自身插入或者修改过的。
- 行的删除版本要么未定义，要么大于当前事务版本号。这可以确保事务读取到的行，在事务开始之前未被删除。

只有符合上述两个条件的记录，才能返回作为查询结果。

**INSERT**

InnoDB为新插入的每一行保存当前系统版本号作为行版本号。

**DELETE**

InnoDB为删除的每一行保存当前系统版本号作为行删除标识。

**UPDATE**

InnoDB为插入一行新纪录，保存当前系统版本号作为行版本号，同时保存当前系统版本号到原来的行作为行删除标识。



保存这两个额外系统版本号，使大多数读操作都可以不用加锁。这样设计使得读数据操作很简单，性能很好，而且也能保证只会读取到符合标准的行。不足之处是每行记录都需要额外的存储空间，需要做更多的行检查工作，以及一些额外的维护工作。

MVCC只在REPEATABLE READ和READ COMMITED 两个隔离级别下工作。其他两个隔离级别都和MVCC不兼容，因为READ COMMITED 总是读取最新的数据行，而不是符合当前事务版本的数据行。而SERIALIZABLE 则会对所有读取的行都加锁。



## 5. 表的设计



## 6. 分库分表



## 7. 面对大数据，数据库层的解决办法



## 8. 事务隔离级别



## 9. 数据库优化



## 10. count(1),count(0),count(*)的区别







# 分布式相关

## 1. 远程过程调用



## 2. Dubbo原理



# Redis相关

## 1. AOF和RDB



## 2. Redis的数据结构



# 算法、手写代码相关

## 1. 数组的全排列



## 2. 写一个死锁程序



## 3. 判断一棵树是否是AVL



## 4. 一篇文章获取出现次数最多的字母？如果是单词呢？ 如果是一本书呢？如果是要在上亿个号码中找出出现最多的呢？说出你的思路，把你能想到的方法都说出来



## 5. 将一棵树从右边看过去的节点依次从上到下输出



## 6. 最大连续子序列和



## 7. 很长的二进制串，求模3的余数



# 机器学习相关

## 1. 聚类算法



## 2. 分类算法