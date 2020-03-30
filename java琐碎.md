1.类加载

![](C:%5CUsers%5C11979%5CDesktop%5C242025553_1546689689399_2A8BFCA92E7F024DFD2F7B0EA602002E.png)

类从被加载到JVM中开始 到卸载为止 整个生命周期包括 加载 验证 准备 解析 初始化 使用和卸载七个阶段 其中类加载过程包括加载 验证 准备 解析和初始化五个阶段



对象实例化例子：God为Person的父类

![image-20200329194157411](java%E7%90%90%E7%A2%8E.assets/image-20200329194157411.png)





2.接口的限制

1. 变量会被隐式的声明为public static final变量，且只能是这样的
2. 除default修饰符外其余方法均为abstract方法

3.对象内存图

​	栈区压入方法，运行完的方法出栈

​	堆区保存new出来的对象的成员变量及**方法在方法区的地址**。

​	方法区保存类的class对象，保存成员方法与变量。

![image-20200304164943802](java%E7%90%90%E7%A2%8E.assets/image-20200304164943802.png)

两个new对象：方法内存共用。

![image-20200304173117518](java%E7%90%90%E7%A2%8E.assets/image-20200304173117518.png)

两个对象指向一个引用：

![image-20200304180017515](java%E7%90%90%E7%A2%8E.assets/image-20200304180017515.png)

4.jmm内存模型

​	一组规范组成的约束

1. 可见性，某线程修改了某变量后，将其写回主内存中，其他线程可以马上知道该变量已被修改
2. 原子性
3. 有序性

![image-20200326092035515](java%E7%90%90%E7%A2%8E.assets/image-20200326092035515.png)

5.static与final区别及用途

​	static：

1. 修饰变量，静态变量随类加载完成初始化，内存中只有一个，所有类实例共享该变量
2. 修饰方法，类加载时即存在，不依赖实例，static方法必须实现，不可与abstract一起使用
3. 修饰代码块，类加载完后会执行静态代码块的内容
4. 父类静态代码块->子类静态代码块->父类非静态代码块->父类构造方法->子类非静态方法->子类构造方法



​	final：

1. 修饰变量，编译器常量：类加载过程完成初始化，只能是基本类型

    ​					运行时常量：基本类型或引用类型，引用不可变，但引用对象的内容可变（地址不可变，但值可变）

2. 修饰方法，该方法不能被继承，不可被子类修改

3. 修饰类，该类不可以被继承

4. 修饰形参，形参不可变

6.hashtaple与hashmap的区别

1. hashtable是同步的，线程安全
2. hashmap的key与value都允许null
3. 哈希值不同，hashtable使用对象的hashcode，hashmap要重新计算hash值
4. 继承的类不同
5. 数组默认大小不同，hashtable默认11，hashmap默认16，2的指数
6. 扩容大小不同，hashtable是old\*2+1，hashmap是old\*2



7.ConcurrentHashMap与HashMap

1. ConcurrentHashMap将整个hash桶进行分段，分段上有锁，线程安全。

8.String，StringBuffer，StringBuilder

1. 都是final类，不允许被继承
2. String长度不可变，另外两个可变
3. StringBuffer线程安全
4. 性能：String>StringBuilder>StringBuffer

9.多线程

1. 继承Thread，重写run方法
2. 实现Runnable接口，可以避免java单继承的局限性
3. 实现Callable接口
4. 线程池

10.ThreadLocal声明的变量即使在线程中属于全局变量，但针对每个线程而言是独立的。

11.volatile变量每次被线程访问时都会强迫线程从主内存中重新获取该变量的最新值，当该值发生变化时也会强迫线程将最新的值写回到主内存中。

12.volatile如何保证内存可见性

1. 防止指令重排
2. 每次访问变量都会进行一次刷新，每次访问的都是最新的变量

volatile不保证原子性，覆盖操作，丢失写值

13.jvm垃圾处理方法

1. 标记-清除算法

    - 标记阶段：先通过根节点，标记所有从根节点开始的对象，未被标记的为垃圾对象
    - 清除阶段：清除所有未被标记的对象

2. 复制算法

    ​	将原有的内存空间分为两份，每次只用一块，在垃圾回收时，将正在使用的内存中的存活对象复制到未使用的内存块中，然后清除正在使用内存块中的所有对象（包括存活的和未存活的）

3. 标记-整理

    - 标记阶段：从根节点开始，标记所有可达对象，未被标记的为垃圾对象
    - 整理阶段：将所有存活对象压缩到内存的一段，之后清理边界所有的空间

4. 总结

    - 效率：复制>整理>清除
    - 内存整齐度：复制=整理>清除
    - 内存利用率：整理=清除>复制

14.新生代，老年代，持久代

1. 新生代，在方法中new一个对象，方法调用完后，该对象回收，新生代对象
2. 老年代，在新生代经历了若干次垃圾回收后仍存活则会进入老年代；大对象直接进入老年代
3. 永久代，方法区，回收废弃常量，无用的类

![image-20200312225223042](java%E7%90%90%E7%A2%8E.assets/image-20200312225223042.png)

![image-20200329213203540](java%E7%90%90%E7%A2%8E.assets/image-20200329213203540.png)

![image-20200329214714638](java%E7%90%90%E7%A2%8E.assets/image-20200329214714638.png)

15. CAS

​	全称：CompareAndSwap 

​	比较真实值与期望值是否相同，若相同，则更新成想修改的值；不同则不可修改

​	![image-20200326120446606](java%E7%90%90%E7%A2%8E.assets/image-20200326120446606.png)

第一个输出true 2019

第二个输出false 2019

![image-20200326121146391](java%E7%90%90%E7%A2%8E.assets/image-20200326121146391.png)

![image-20200326121223629](java%E7%90%90%E7%A2%8E.assets/image-20200326121223629.png)

缺点：

1. ![image-20200326161119370](java%E7%90%90%E7%A2%8E.assets/image-20200326161119370.png)
2. 只能保证一个共享变量的原子操作
3. 引出了ABA问题

16.ABA问题

![image-20200326162910664](java%E7%90%90%E7%A2%8E.assets/image-20200326162910664.png)

将某个自定义的类设置为原子引用类：

![image-20200326163502919](java%E7%90%90%E7%A2%8E.assets/image-20200326163502919.png)

解决ABA问题：时间戳（版本号）原子引用

AtomicStampedReference类每次cas时比较版本号是否是最新的，若不是则修改失败

17.如何解决ArrayList线程不安全

1. 替换成Vector
2. ![image-20200326182557811](java%E7%90%90%E7%A2%8E.assets/image-20200326182557811.png)
3. CopyOnWriteArrayList

CopyOnWriteSet底层是CopyOnWriteArrayList

18.HashSet底层是HashMap，每次插入时只管key，value值都一样，都是PRESENT。

![image-20200326195345727](java%E7%90%90%E7%A2%8E.assets/image-20200326195345727.png)

19.公平锁与非公平锁

公平：排队；非公平：插队，允许抢锁

![image-20200326202643178](java%E7%90%90%E7%A2%8E.assets/image-20200326202643178.png)

括号内true，false设置公平与非公平，默认不写为非公平。

20.可重入锁（递归锁）

![image-20200326203240124](java%E7%90%90%E7%A2%8E.assets/image-20200326203240124.png)

![image-20200326203633546](java%E7%90%90%E7%A2%8E.assets/image-20200326203633546.png)

ReentrantLock/Synchronized就是可重入锁。

为了解决死锁

21.自旋锁

用循环替代阻塞，尝试获取锁，减少线程上下文切换的消耗，缺点是循环会消耗CPU

借用原子引用：AtomicReference<Thread>实现。通过cas比较。

![image-20200328170350509](java%E7%90%90%E7%A2%8E.assets/image-20200328170350509.png)



22.读写锁

23.Synchronized与Lock的区别

1. Synchronized是关键字，是jvm层面，Lock是API层面、
2. 前者不需要手动释放锁，后者需要手动释放，否则会造成死锁
3. 前者不可中断，除非抛异常，后者可中断（超时，强制中断）
4. 加锁是否公平，前者为非公平，后者可以设置
5. 后者可以精确唤醒某线程，Condition

24.可以作为GC Roots的对象：

![image-20200326232750184](java%E7%90%90%E7%A2%8E.assets/image-20200326232750184.png)



25.CountDownLatch

可以设置在若干个线程之后运行

构造方法：(int num)，设置数字

```java
countDown(); 将计数器减一
await(); 计数器没有减到0前一直等待
//例

public static void main(String[] args) throws InterruptedException {
        CountDownLatch countDownLatch=new CountDownLatch(6);
        for (int i = 0; i < 6; i++) {
            new Thread(()->{
                System.out.println(Thread.currentThread().getName()+" out");
                countDownLatch.countDown();
            },String.valueOf(i)).start();
        }
        countDownLatch.await();
        System.out.println("all out");
    }
```

26.CyclicBarrier

设置在若干线程到达之后运行，在未达到指定数量的线程到达前不运行，到达的线程阻塞。

构造方法：(int num,Runable runable)，指定需要num个线程到达后，才会运行runable线程。

```java
//例
public static void main(String[] args) throws InterruptedException {

    CyclicBarrier cyclicBarrier=new CyclicBarrier(7,()->{
        System.out.println("I am back");
    });
    for (int i = 0; i < 7; i++) {
        new Thread(()->{
            try {
                System.out.println(Thread.currentThread().getName()+" come in");
                cyclicBarrier.await();
                System.out.println(Thread.currentThread().getName()+" in it");
            } catch (InterruptedException e) {
                e.printStackTrace();
            } catch (BrokenBarrierException e) {
                e.printStackTrace();
            }
        },String.valueOf(i)).start();
    }
}

```

![image-20200328095052324](java%E7%90%90%E7%A2%8E.assets/image-20200328095052324.png)

27.Semaphore信号量

构造方法：(int num); 设置num大小的信号量

acquire(); 相当于P操作

realease(); V操作

```java
public static void main(String[] args) throws InterruptedException {
    Semaphore semaphore=new Semaphore(3);
    for (int i = 0; i < 6; i++) {
        new Thread(()->{
            try {
                semaphore.acquire();
                System.out.println(Thread.currentThread().getName()+" come in");
                System.out.println(Thread.currentThread().getName()+" out");
            } catch (InterruptedException e) {
                e.printStackTrace();
            } finally {
                semaphore.release();
            }
        },String.valueOf(i)).start();
    }
}
```

28. 阻塞队列接口BlockingQueue

![image-20200328103909470](java%E7%90%90%E7%A2%8E.assets/image-20200328103909470.png)

![image-20200328105207865](java%E7%90%90%E7%A2%8E.assets/image-20200328105207865.png)

![image-20200328111423908](java%E7%90%90%E7%A2%8E.assets/image-20200328111423908.png)

29.线程池

![image-20200328223310723](java%E7%90%90%E7%A2%8E.assets/image-20200328223310723.png)

```java
public static void main(String[] args) {
    ExecutorService threadPool= Executors.newFixedThreadPool(5);
    //ExecutorService threadPool=Executors.newCachedThreadPool();
    try{
        for (int i = 0; i < 100; i++) {
            threadPool.execute(()->{
                System.out.println(Thread.currentThread().getName()+" 办理业务");
            });
        }
    }catch (Exception e){
        e.printStackTrace();
    }finally {
        threadPool.shutdown();
    }
}
```



![image-20200328230441189](java%E7%90%90%E7%A2%8E.assets/image-20200328230441189.png)

线程池底层构造方法：

![image-20200328230922993](java%E7%90%90%E7%A2%8E.assets/image-20200328230922993.png)

![image-20200328231505915](java%E7%90%90%E7%A2%8E.assets/image-20200328231505915.png)

申请线程的过程：

1. 若线程池中线程数未达常驻核心线程数，则消耗一个常驻线程
2. 否则进入任务队列等待
3. 若任务队列也满，若线程池中线程数未达max容量，则扩容，将新增的线程依次分配给任务队列中的任务，若任务队列有空余位置，则该任务进入任务队列等待
4. 否则，实行拒绝策略

拒绝策略：

1. ![image-20200329104557876](java%E7%90%90%E7%A2%8E.assets/image-20200329104557876.png)
2. CallerRunspolicy：将任务回退给调用线程自己解决
3. ![image-20200329104705761](java%E7%90%90%E7%A2%8E.assets/image-20200329104705761.png)
4. ![image-20200329104718541](java%E7%90%90%E7%A2%8E.assets/image-20200329104718541.png)

![image-20200329104944617](java%E7%90%90%E7%A2%8E.assets/image-20200329104944617.png)

实际工程中不使用Executors中的线程池创建方法，因为其阻塞队列设置大小为int.max，无限向阻塞队列加任务，会造成OOM

```java
import java.util.concurrent.*;

public class ThreadPoolDemo {

    public static void main(String[] args) {
        /*
        * 核心数：2个
        * 最大数：5个
        * 多余空闲线程存活时间：5s
        * 存活时间单位：秒
        * 工作队列：链表形式的大小为3的阻塞队列
        * 默认的线程生产工厂
        * 拒绝策略：默认的，直接抛RejectedExecutionException停止
        *
        * */
        ExecutorService threadPool=new ThreadPoolExecutor(
                2,
                5,
                5,
                TimeUnit.SECONDS,
                new LinkedBlockingDeque<Runnable>(3),
                Executors.defaultThreadFactory(),
                new ThreadPoolExecutor.AbortPolicy());
        try{
            for (int i = 1; i < 90; i++) {
                threadPool.execute(()->{
                    System.out.println(Thread.currentThread().getName()+" come in");
                });
            }
        }catch(Exception e){
            e.printStackTrace();
        }finally {
            threadPool.shutdown();
        }
        
    }
}
```

线程池参数设置：

获取CPU核心数

```java
System.out.println(Runtime.getRuntime().availableProcessors());
```

CPU密集型：![image-20200329111743471](java%E7%90%90%E7%A2%8E.assets/image-20200329111743471.png)

IO密集型：两种

1. ![image-20200329111836652](java%E7%90%90%E7%A2%8E.assets/image-20200329111836652.png)
2. ![image-20200329111912417](java%E7%90%90%E7%A2%8E.assets/image-20200329111912417.png)

30.死锁

必要条件：

1. 互斥
2. 请求与保持
3. 不可剥夺
4. 循环等待

显示所有java应用程序：jps -l

jstack "id"找到死锁查看

31.java8之后的jvm：

![image-20200329115920798](java%E7%90%90%E7%A2%8E.assets/image-20200329115920798.png)



![image-20200329122038209](java%E7%90%90%E7%A2%8E.assets/image-20200329122038209.png)



32.HotSpot虚拟机中，对象的内存布局

```undefined
1.对象头
2.实例数据
3.对齐填充
```

```undefined
假设当前为32bit，在对象未被锁定情况下。25bit为存储对象的哈希码、4bit用于存储分代年龄，2bit用于存储锁标志位，1bit固定为0。
```

![image-20200329120739791](java%E7%90%90%E7%A2%8E.assets/image-20200329120739791.png)

![image-20200329120814571](java%E7%90%90%E7%A2%8E.assets/image-20200329120814571.png)

33.JVM参数

![image-20200329143950583](java%E7%90%90%E7%A2%8E.assets/image-20200329143950583.png)

![image-20200329144148295](java%E7%90%90%E7%A2%8E.assets/image-20200329144148295.png)

-XX参数设置Boolean类型，+true，-false

![image-20200329145349943](java%E7%90%90%E7%A2%8E.assets/image-20200329145349943.png)

设置Key-value：

![image-20200329145848893](java%E7%90%90%E7%A2%8E.assets/image-20200329145848893.png)

![image-20200329151505783](java%E7%90%90%E7%A2%8E.assets/image-20200329151505783.png)

![image-20200329155444250](java%E7%90%90%E7%A2%8E.assets/image-20200329155444250.png)





![image-20200329151613178](java%E7%90%90%E7%A2%8E.assets/image-20200329151613178.png)

![image-20200329152132403](java%E7%90%90%E7%A2%8E.assets/image-20200329152132403.png)

# 容器类

## HashMap

![image-20200329193633434](java%E7%90%90%E7%A2%8E.assets/image-20200329193633434.png)

![image-20200329193722152](java%E7%90%90%E7%A2%8E.assets/image-20200329193722152.png)

![image-20200329193759859](java%E7%90%90%E7%A2%8E.assets/image-20200329193759859.png)

HashMap长度一定为2^n，因为hash时本质是：对象.hashcode&(2^n-1)，(2^n-1)一定是全1的，那么就可以保证分别是均匀的，如果不用2^n的话，某些位是0，那这个下标的桶永远都不会被用到。



线程安全的例子：Hashtable，ConcurrentHashMap，Collections.synchronizedMap()

## ArrayList

线程不安全，扩容为1.5倍old

## CopyOnWriteArrayList

线程安全， 通过ReentrantLock获取对象锁的方式来实现线程安全。

读写分离

写的时候允许读，写的时候加锁，copy一份新数组操作，写完后再复制给旧数组，适合读多写少场景。

缺点：

- 内存占用：在写操作时需要复制一个新的数组，使得内存占用为原来的两倍左右；
- 数据不一致：读操作不能读取实时性的数据，因为部分写操作的数据还未同步到读数组中。

## LinkedList

双向链表实现

## ConcurrentHashMap

JDK8之后：使用了 CAS 操作来支持更高的并发度，在 CAS 操作失败时使用内置锁 synchronized。 synchronized只锁定当前链表或红黑二叉树的首节点，这样只要hash不冲突，就不会产生并发，效率又提升N倍。

## HashSet

底层是HashMap

## 阻塞队列

![image-20200330234907398](java%E7%90%90%E7%A2%8E.assets/image-20200330234907398.png)



