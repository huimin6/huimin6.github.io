<!-- MarkdownTOC -->

- [Java](#java)
    + [进程与线程](#进程与线程)
    + [多线程](#多线程)
        * [创建线程的三种方式](#创建线程的三种方式)
        * [线程的生命周期](#线程的生命周期)
        * [sleep、wait和yield](#sleep、wait和yield)
        * [线程池](#线程池)
    + [动态代理](#动态代理)
    + [NIO与IO](#nio与io)
- [Java虚拟机](#java虚拟机)
    + [垃圾回收](#垃圾回收)

<!-- /MarkdownTOC -->

# Java

## 进程与线程
1.进程与线程的区别：

(1)线程是调度的基本单位，进程是拥有资源的基本单位。

(2)进程有独立的地址空间，线程之间没有单独的地址空间，但是线程有自己的堆栈和局部变量。

(3) 一个程序至少有一个进程，一个进程至少有一个线程。

(4) 线程的划分尺度小于进程，使得多线程程序的并发性高。

(5) 进程在执行过程中拥有独立的内存单元，而多个线程共享内存，从而极大地提高了程序的运行效率。

(6) 线程在执行过程中与进程还是有区别的。每个独立的线程有一个程序运行的入口、顺序执行序列和程序的出口。但是线程不能够独立执行，必须依存在应用程序中，由应用程序提供多个线程执行控制。

(7) 从逻辑角度来看，多线程的意义在于一个应用程序中，有多个执行部分可以同时执行。但操作系统并没有将多个线程看做多个独立的应用，来实现进程的调度和管理以及资源分配。这就是进程和线程的重要区别。

## 多线程
### 创建线程的三种方式
1.继承Thread类
```
public class FirstThread extends Thread{
    private int i;
    public void run() {
        for(; i < 100; i++) {
            System.out.println(getName() + " " + i);
        }
    }
    public static void main(String[] args) {
        for(int i = 0; i < 100; i++) {
            System.out.println(Thread.currentThread().getName() + " " + i);
            if(i == 20) {
                new FirstThread().start();
                new FirstThread().start();
            }
        }
    }
}
```
2.实现Runnable接口
```
public class SecondThread implements Runnable{
    private int i;
    @Override
    public void run() {
        for(; i < 100; i++) {
            System.out.println(Thread.currentThread().getName() + " " + i);
        }
    }
    public static void main(String[] args) {
        for(int i = 0; i < 100; i++) {
            System.out.println(Thread.currentThread().getName() + " " + i);
            if(i == 20) {
                SecondThread st = new SecondThread();
                new Thread(st, "Thread-1").start();
                new Thread(st, "Thread-2").start();
            }
        }
    }
     
}
```
3.实现Callable接口
```
import java.util.concurrent.Callable;
import java.util.concurrent.FutureTask;

//实现多线程的方法三：使用Callable和Future创建线程
//创建Callable接口的实现类
public class ThirdThread implements Callable<Integer>{
    //实现Callable接口中的run()方法
    @Override
    public Integer call() throws Exception {
        // TODO Auto-generated method stub
        int i = 0;
        for(; i < 100; i++) {
            System.out.println(Thread.currentThread().getName() + " " + i);
        }
        return i;
    }
    public static void main(String[] args) {
        //创建Callable接口的实现类的实例对象
        ThirdThread tt = new ThirdThread();
        //使用FutureTask对象来包装Callable对象
        FutureTask<Integer> ft = new FutureTask<Integer>(tt);
        for(int i = 0; i < 100; i++) {
            System.out.println(Thread.currentThread().getName() + " " + i);
            if(i == 20) {
//              Thread th = new Thread(ft, "测试线程");
//              th.run();
                //直接调用run()方法后，th.start()不再执行
//              th.start();
//              new Thread(ft, "测试线程").run();
                try {
                    Thread.sleep(2);
                } catch (InterruptedException e) {
                    // TODO Auto-generated catch block
                    e.printStackTrace();
                }
                new Thread(ft, "有返回值的线程").start();
            }
        }
        try {
            System.out.println("子线程的返回值：" + ft.get() );
        } catch(Exception e) {
            e.printStackTrace();
        }
    }

}
```
### 线程的生命周期
线程的生命周期：新建(New)、就绪(Runnable)、运行(Running)、阻塞(Blocker)、死亡(Dead)
<div align="center"> <img src="../pictures//thread.jpg"/> </div><br>

### sleep、wait和yield
|sleep()、wait()和yield()比较|
|-|
| sleep()、yield()是线程类(Thread)的方法，wait()是Object类的方法 |
| sleep()、yield()不释放对象锁，wait()释放对象锁 |
| sleep()暂停线线程，会把执行机会让给其他线程，不考虑线程的优先级，yield()暂停线程，只会把线程让给同级或者优先级更高的线程 |
| sleep()会抛InterruptionException的异常，但是yield()方法不会抛出任何异常 |
| wait()后进入等待锁定池，只有针对此对象发出notify()方法后获得对象锁进入**可运行状态**|
| wait()和notify()会对对象的"锁标志"进行操作，所以它们必须在synchronized函数或synchronized代码块中进行调用。如果在non- synchronized函数或non-synchronized代码块中进行调用，虽然能编译通过，但在**运行时**会发生IllegalMonitorStateException的异常。

### 线程池
1.线程池的创建
创建线程池由ThreadPoolExecutor类来完成，该类的一个最核心的构造方法就是:
```
public class ThreadPoolExecutor extends AbstractExecutorService {
    
    public ThreadPoolExecutor(int corePoolSize, int maximumPoolSize, 
        long keepAliveTime, TimeUnit unit, BlockingQueue<Runnable> workQueue, 
        ThreadFactory threadFactory, RejectedExecutionHandler handler);
```
参数含义:

|参数|含义|
|:-:|-|
| corePoolSize | 核心池的大小，这个参数跟后面讲述的线程池的实现原理有非常大的关系。在创建了线程池后，默认情况下，线程池中并没有任何线程，而是等待有任务到来才创建线程去执行任务，除非调用了prestartAllCoreThreads()或者prestartCoreThread()方法，从这2个方法的名字就可以看出，是预创建线程的意思，即在没有任务到来之前就创建corePoolSize个线程或者一个线程。默认情况下，在创建了线程池后，线程池中的线程数为0，当有任务来之后，就会创建一个线程去执行任务，当线程池中的线程数目达到corePoolSize后，就会把到达的任务放到缓存队列当中 |
| maximumPoolSize | 线程池最大线程数，这个参数也是一个非常重要的参数，它表示在线程池中最多能创建多少个线程 |
| keepAliveTime | 表示线程没有任务执行时最多保持多久时间会终止，默认情况下，只有当线程池中的线程数大于corePoolSize时，keepAliveTime才会起作用，直到线程池中的线程数不大于corePoolSize，即当线程池中的线程数大于corePoolSize时，如果一个线程空闲的时间达到keepAliveTime，则会终止，直到线程池中的线程数不超过corePoolSize。但是如果调用了allowCoreThreadTimeOut(boolean)方法，在线程池中的线程数不大于corePoolSize时，keepAliveTime参数也会起作用，直到线程池中的线程数为0 |
| unit | 参数keepAliveTime的时间单位，有7种取值，在TimeUnit类中有7种静态属性：<br>TimeUnit.DAYS;//天<br>TimeUnit.HOURS;//小时<br>TimeUnit.MINUTES;//分钟<br>TimeUnit.SECONDS;//秒<br>TimeUnit.MILLISECONDS;//毫秒<br>TimeUnit.MICROSECONDS;//微妙<br>TimeUnit.NANOSECONDS;//纳秒 |
| workQueue  | 一个阻塞队列，用来存储等待执行的任务，这个参数的选择也很重要，会对线程池的运行过程产生重大影响，一般来说，这里的阻塞队列有几种选择：ArrayBlockingQueue，LinkedBlockingQueue，SynchronousQueue。ArrayBlockingQueue和PriorityBlockingQueue使用较少，一般使用LinkedBlockingQueue和Synchronous。线程池的排队策略与BlockingQueue有关 |
| threadFactory | 线程工厂，主要用来创建线程 |
| handler | 表示当拒绝处理任务时的策略，有以下四种取值：<br>ThreadPoolExecutor.AbortPolicy：丢弃任务并抛出RejectedExecutionException异常<br>ThreadPoolExecutor.DiscardPolicy：也是丢弃任务，但是不抛出异常 <br>ThreadPoolExecutor.DiscardOldestPolicy：丢弃队列最前面的任务，然后重新尝试执行任务（重复此过程）<br>ThreadPoolExecutor.CallerRunsPolicy：由调用线程处理该任务 |

2.线程池中的几个核心问题的实现

(1)如何实现线程的复用

线程的生命周期中需要经过新建(new)、就绪(Runnable)、运行(Running)、阻塞(Blocked)、死亡(Dead) 5种状态
<div align="center"> <img src="../pictures//Thread life cycle.jpg"/> </div><br>
Thread通过new关键字来创建一个线程，线程创建后调用start()方法启动线程，Java虚拟机会为其创建方法调用栈和程序计数器，同时将hasBeenStarted为true，之后调用start方法就会有异常。此时线程处于就绪状态，并没有开始运行，线程何时开始运行，取决于Java虚拟机中线程调度器的调度。线程获取cpu后，run()方法会被执行，不要直接调用run()方法。之后线程的状态在就绪————运行————阻塞之间切换，直到run()方法执行结束或者其他方式停止线程，线程才会进入dead状态。

因此，要想实现线程的复用，就需要让线程一直存活(就绪、运行或阻塞)，不能进入dead状态。

ThreadPoolExecutor类通过Worker类来实现线程的复用，看看worker类的简化后的代码更容易理解：
```
private final class Worker implements Runnable {
 
    final Thread thread;
    Runnable firstTask;
    
    Worker(Runnable firstTask) {
        this.firstTask = firstTask;
        this.thread = getThreadFactory().newThread(this);
    }
    
    public void run() {
        runWorker(this);
    }
    
    final void runWorker(Worker w) {
        Runnable task = w.firstTask;
        w.firstTask = null;
        while (task != null || (task = getTask()) != null){
        task.run();
    }
}
```
Worker类实现了Runnable接口，拥有Thread成员变量，这个thread就是要开启的线程。在创建Worker对象时，会创建一个新的线程，同时Worker对象自己会作为参数传入。这样当调用Thread的start()时，实际上就是调用Worker对象的run()方法，接着调用runWorker()方法。在runWorker()方法中，有一个while循环，它会从不断通过getTast()获取Runnable对象去执行。getTask()方法的简化代码如下：
```
private Runnable getTask() {
    if(一些特殊情况) {
        return null;
    }

    Runnable r = workQueue.take();

    return r;
}
```
这个workQueue就是初始化ThreadPoolExecutor时存放任务的BlockingQueue队列，这个队列里的存放的都是将要执行的Runnable任务。因为BlockingQueue是个阻塞队列，BlockingQueue.take()得到如果是空，则进入等待状态直到BlockingQueue有新的对象被加入时唤醒阻塞的线程。所以一般情况Thread的run()方法就不会结束，而是不断执行从workQueue里的Runnable任务，这就达到了线程复用的作用了。

(2)控制线程最大并发数

那Runnable对象是什么时候放入workQueue？Worker对象又是什么时候创建，Worker里的Thread的又是什么时候调用start()方法来执行Worker的run()方法的呢？有上面的分析看出Worker里的runWorker()执行任务时是一个接一个，串行进行的，那并发是怎么体现的呢？

很容易想到是在execute(Runnable runnable)时会做上面的一些任务。看下execute方法里是怎么做的。execute()方法简化后的代码：
```
public void execute(Runnable command) {
    if (command == null)
        throw new NullPointerException();

     int c = ctl.get();
    // 当前线程数 < corePoolSize
    if (workerCountOf(c) < corePoolSize) {
        // 直接启动新的线程。
        if (addWorker(command, true))
            return;
        c = ctl.get();
    }

    // 活动线程数 >= corePoolSize
    // runState为RUNNING && 队列未满
    // workQueue.offer(command)表示添加到队列，如果添加成功返回true，否则返回false
    if (isRunning(c) && workQueue.offer(command)) {
        int recheck = ctl.get();
        // 再次检验是否为RUNNING状态
        // 非RUNNING状态 则从workQueue中移除任务并拒绝
        if (!isRunning(recheck) && remove(command))
            reject(command);// 采用线程池指定的策略拒绝任务
        // 两种情况：
        // 1.非RUNNING状态拒绝新的任务
        // 2.队列满了启动新的线程失败（workCount > maximumPoolSize）
    } else if (!addWorker(command, false))
        reject(command);
}
```
addWorker()方法的简化代码：
```
private boolean addWorker(Runnable firstTask, boolean core) {

    int wc = workerCountOf(c);
    if (wc >= (core ? corePoolSize : maximumPoolSize)) {
        return false;
    }

    w = new Worker(firstTask);
    final Thread t = w.thread;
    t.start();
}
```
根据代码再来看上面提到的线程池工作过程中的添加任务的情况：

(1)如果正在运行的线程数量小于 corePoolSize，那么马上创建线程运行这个任务；<br>
(2)如果正在运行的线程数量大于或等于 corePoolSize，那么将这个任务放入队列；<br>
(3)如果这时候队列满了，而且正在运行的线程数量小于 maximumPoolSize，那么还是要创建非核心线程立刻运行这个任务；<br>
(4)如果队列满了，而且正在运行的线程数量大于或等于 maximumPoolSize，那么线程池会抛出异常RejectExecutionException。

通过addWorker如果成功创建新的线程成功，则通过start()开启新线程，同时将firstTask作为这个Worker里的run()中执行的第一个任务。

虽然每个Worker的任务是串行处理，但如果创建了多个Worker，因为共用一个workQueue，所以就会并行处理了。

(3)线程管理

通过线程池可以很好的管理线程的复用，控制并发数，以及销毁等过程,线程的复用和控制并发上面已经讲了，而线程的管理过程已经穿插在其中了，也很好理解。

在ThreadPoolExecutor有个ctl的AtomicInteger变量。通过这一个变量保存了两个内容：

(1)所有线程的数量<br>
(2)每个线程所处的状态<br>
其中低29位存线程数，高3位存runState，通过位运算来得到不同的值。
```
private final AtomicInteger ctl = new AtomicInteger(ctlOf(RUNNING, 0));

//得到线程的状态
private static int runStateOf(int c) {
    return c & ~CAPACITY;
}


//得到Worker的的数量
private static int workerCountOf(int c) {
    return c & CAPACITY;
}


// 判断线程是否在运行
private static boolean isRunning(int c) {
    return c < SHUTDOWN;
}
```
这里主要通过shutdown和shutdownNow()来分析线程池的关闭过程。首先线程池有五种状态来控制任务添加与执行。主要介绍以下三种：

(1)RUNNING状态：线程池正常运行，可以接受新的任务并处理队列中的任务；<br>
(2)SHUTDOWN状态：不再接受新的任务，但是会执行队列中的任务；<br>
(3)STOP状态：不再接受新任务，不处理队列中的任务<br>
shutdown这个方法会将runState置为SHUTDOWN，会终止所有空闲的线程，而仍在工作的线程不受影响，所以队列中的任务人会被执行。shutdownNow方法将runState置为STOP。和shutdown方法的区别，这个方法会终止所有的线程，所以队列中的任务也不会被执行了。

这篇博客写的很好：http://silencedut.com/2016/06/25/从使用到原理学习Java线程池/

## 动态代理
与静态代理相比，动态代理的好处就是不需要为每个类都生成代理类，可以在运行过程中动态生成代码

1.静态代理

(1)定义抽象主题接口
```
package com.atguigu.java;

/**
 * 接口
 * 抽象主题
 */
public interface IMath {
    //加
    int add(int n1, int n2);

    //减
    int sub(int n1, int n2);

    //乘
    int mut(int n1, int n2);

    //除
    int div(int n1, int n2);

}
```
(2)主题类，算术类，实现抽象接口
```
package com.atguigu.java;

/**
 * 被代理的目标对象
 *真实主题
 */
public class Math implements IMath {
    //加
    public int add(int n1,int n2){
        int result=n1+n2;
        System.out.println(n1+"+"+n2+"="+result);
        return result;
    }
    
    //减
    public int sub(int n1,int n2){
        int result=n1-n2;
        System.out.println(n1+"-"+n2+"="+result);
        return result;
    }
    
    //乘
    public int mut(int n1,int n2){
        int result=n1*n2;
        System.out.println(n1+"X"+n2+"="+result);
        return result;
    }
    
    //除
    public int div(int n1,int n2){
        int result=n1/n2;
        System.out.println(n1+"/"+n2+"="+result);
        return result;
    }
}
```
(3)代理类
```
package com.atguigu.java;

import java.util.Random;

/**
 * 静态代理类
 */
public class MathProxy implements IMath {

    //被代理的对象
    IMath math=new Math();
    
    //加
    public int add(int n1, int n2) {
        //开始时间
        long start=System.currentTimeMillis();
        lazy();
        int result=math.add(n1, n2);
        Long span= System.currentTimeMillis()-start;
        System.out.println("共用时："+span);
        return result;
    }

    //减法
    public int sub(int n1, int n2) {
        //开始时间
        long start=System.currentTimeMillis();
        lazy();
        int result=math.sub(n1, n2);
        Long span= System.currentTimeMillis()-start;
        System.out.println("共用时："+span);
        return result;
    }

    //乘
    public int mut(int n1, int n2) {
        //开始时间
        long start=System.currentTimeMillis();
        lazy();
        int result=math.mut(n1, n2);
        Long span= System.currentTimeMillis()-start;
        System.out.println("共用时："+span);
        return result;
    }
    
    //除
    public int div(int n1, int n2) {
        //开始时间
        long start=System.currentTimeMillis();
        lazy();
        int result=math.div(n1, n2);
        Long span= System.currentTimeMillis()-start;
        System.out.println("共用时："+span);
        return result;
    }

    //模拟延时
    public void lazy()
    {
        try {
            int n=(int)new Random().nextInt(500);
            Thread.sleep(n);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}
```
(4)运行测试
```
package com.atguigu.java;

public class Test {
    
    IMath math=new MathProxy();
    @org.junit.Test
    public void test01()
    {
        int n1=100,n2=5;
        math.add(n1, n2);
        math.sub(n1, n2);
        math.mut(n1, n2);
        math.div(n1, n2);
    }
}
```
(5)**小结**

通过静态代理，是否完全解决了上述的4个问题：

已解决：

1)解决了“开闭原则（OCP）”的问题，因为并没有修改Math类，而扩展出了MathProxy类

2)解决了“依赖倒转（DIP）”的问题，通过引入接口

3)解决了“单一职责（SRP）”的问题，Math类不再需要去计算耗时与延时操作，但从某些方面讲MathProxy还是存在该问题

未解决：

4)如果项目中有多个类，则需要编写多个代理类，工作量大，不好修改，不好维护，不能应对变化。

如果要解决上面的问题，可以使用动态代理。

2.JDK动态代理

使用JDK内置的Proxy实现，只需要一个代理类，而不是针对每个类编写代理类。

在上一个示例中修改代理类MathProxy如下：
```
package com.atguigu.java1;

import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;
import java.lang.reflect.Proxy;
import java.util.Random;

/**
 * 动态代理类
 */
public class DynamicProxy implements InvocationHandler {

    //被代理的对象
    Object targetObject;
    
    /**
     * 获得被代理后的对象
     * @param object 被代理的对象
     * @return 代理后的对象
     */
    public Object getProxyObject(Object object){
        this.targetObject=object;
        return Proxy.newProxyInstance(
                targetObject.getClass().getClassLoader(), //类加载器
                targetObject.getClass().getInterfaces(),  //获得被代理对象的所有接口
                this);  //InvocationHandler对象
        //loader:一个ClassLoader对象，定义了由哪个ClassLoader对象来生成代理对象进行加载
        //interfaces:一个Interface对象的数组，表示的是我将要给我需要代理的对象提供一组什么接口，如果我提供了一组接口给它，那么这个代理对象就宣称实现了该接口(多态)，这样我就能调用这组接口中的方法了
        //h:一个InvocationHandler对象，表示的是当我这个动态代理对象在调用方法的时候，会关联到哪一个InvocationHandler对象上，间接通过invoke来执行
    }
    
    
    /**
     * 当用户调用对象中的每个方法时都通过下面的方法执行，方法必须在接口
     * proxy 被代理后的对象
     * method 将要被执行的方法信息（反射）
     * args 执行方法时需要的参数
     */
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        //被织入的内容，开始时间
        long start=System.currentTimeMillis();
        lazy();
        
        //使用反射在目标对象上调用方法并传入参数
        Object result=method.invoke(targetObject, args);
        
        //被织入的内容，结束时间
        Long span= System.currentTimeMillis()-start;
        System.out.println("共用时："+span);
        
        return result;
    }
    
    //模拟延时
    public void lazy()
    {
        try {
            int n=(int)new Random().nextInt(500);
            Thread.sleep(n);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }

}
```
测试运行
```
package com.atguigu.java1;

public class Test {
    
    //实例化一个MathProxy代理对象
    //通过getProxyObject方法获得被代理后的对象
    IMath math=(IMath)new DynamicProxy().getProxyObject(new Math());
    @org.junit.Test
    public void test01()
    {
        int n1=100,n2=5;
        math.add(n1, n2);
        math.sub(n1, n2);
        math.mut(n1, n2);
        math.div(n1, n2);
    }
    
    IMessage message=(IMessage) new DynamicProxy().getProxyObject(new Message());
    @org.junit.Test
    public void test02()
    {
        message.message();
    }
}
```
**小结**：
JDK内置的Proxy动态代理可以在运行时动态生成字节码，而没必要针对每个类编写代理类。中间主要使用到了一个接口InvocationHandler与Proxy.newProxyInstance静态方法。

使用内置的Proxy实现动态代理有一个问题：被代理的类必须实现接口，未实现接口则没办法完成动态代理。

3.CGLIB动态代理

CGLIB(Code Generation Library)是一个开源项目,是一个强大的，高性能，高质量的Code生成类库，它可以在运行期扩展Java类与实现Java接口，通俗说cglib可以在运行时动态生成字节码。

(1)引用cglib，通过maven

<div align="center"> <img src="../pictures//cglib.png"/> </div><br>

(2)使用cglib完成动态代理，大概的原理是：cglib继承被代理的类，重写方法，织入通知，动态生成字节码并运行，因为是继承所以final类是没有办法动态代理的。具体实现如下：
```
package com.atguigu.java2;

import java.lang.reflect.Method;
import java.util.Random;

import net.sf.cglib.proxy.Enhancer;
import net.sf.cglib.proxy.MethodInterceptor;
import net.sf.cglib.proxy.MethodProxy;

/*
 * 动态代理类
 * 实现了一个方法拦截器接口
 */
public class DynamicProxy implements MethodInterceptor {

    // 被代理对象
    Object targetObject;

    //Generate a new class if necessary and uses the specified callbacks (if any) to create a new object instance. 
    //Uses the no-arg constructor of the superclass.
    //动态生成一个新的类，使用父类的无参构造方法创建一个指定了特定回调的代理实例
    public Object getProxyObject(Object object) {
        this.targetObject = object;
        //增强器，动态代码生成器
        Enhancer enhancer=new Enhancer();
        //回调方法
        enhancer.setCallback(this);
        //设置生成类的父类类型
        enhancer.setSuperclass(targetObject.getClass());
        //动态生成字节码并返回代理对象
        return enhancer.create();
    }

    // 拦截方法
    public Object intercept(Object object, Method method, Object[] args, MethodProxy methodProxy) throws Throwable {
        // 被织入的横切内容，开始时间 before
        long start = System.currentTimeMillis();
        lazy();

        // 调用方法
        Object result = methodProxy.invoke(targetObject, args);

        // 被织入的横切内容，结束时间
        Long span = System.currentTimeMillis() - start;
        System.out.println("共用时：" + span);
        
        return result;
    }

    // 模拟延时
    public void lazy() {
        try {
            int n = (int) new Random().nextInt(500);
            Thread.sleep(n);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }

}
```
(3)测试运行：
```
package com.atguigu.java2;

public class Test {
    //实例化一个DynamicProxy代理对象
    //通过getProxyObject方法获得被代理后的对象
    Math math=(Math)new DynamicProxy().getProxyObject(new Math());
    @org.junit.Test
    public void test01()
    {
        int n1=100,n2=5;
        math.add(n1, n2);
        math.sub(n1, n2);
        math.mut(n1, n2);
        math.div(n1, n2);
    }
    //另一个被代理的对象,不再需要重新编辑代理代码
    Message message=(Message) new DynamicProxy().getProxyObject(new Message());
    @org.junit.Test
    public void test02()
    {
        message.message();
    }
}
```
(4)**小结**

使用cglib可以实现动态代理，即使被代理的类没有实现接口，但被代理的类必须不是final类。

如果项目中有些类没有实现接口，则不应该为了实现动态代理而刻意去抽出一些没有实例意义的接口，通过cglib可以解决该问题。
## NIO与IO
1.区别

(1)IO是面向流(stream)的，NIO是面向缓冲区(channel和buffer)的；

(2)IO只能一次从流中读取或者写入1个字节或者几个字节，没有缓冲，NIO是先读入或者写入缓冲区，然后再处理，相对来说更灵活一些；

(3)IO是阻塞的，当调用read()和write()方法时，直到读取到数据或者数据完全写入，否则就会一直处于阻塞状态，NIO是非阻塞的，从某个通道发送请求读取数据，如果没有数据，线程就会去执行其他任务，不需要等待，通常线程会某个通道的IO空闲时间去执行其他通道的IO操作；

(4)Java NIO的选择器允许一个单独的线程来监视多个输入通道，你可以注册多个通道使用一个选择器，然后使用一个单独的线程来“选择”通道：这些通道里已经有可以处理的输入，或者选择已准备写入的通道。这种选择机制，使得一个单独的线程很容易来管理多个通道。

# Java虚拟机
## 垃圾回收
Java的垃圾回收主要是回收堆中的对象实例，什么时候回收由系统决定。在垃圾回收之前，首先要通过可达性分析（根节点是虚拟机栈中引用的对象、方法区中类静态属性引用的对象、方法区中常量引用的对象、本地方法栈中JNI引用的对象）判断对象是否存活，然后将死亡的对象实例所占用的内存空间回收。堆所占用的内存可以划分为新生代和老年代，新生代内存空间进一步还可以细分为EdenSpace、FromSurvivor、ToSurvivor，新创建的对象实例的内存大多数（启用本地线程分配缓冲的按照线程优先在TLAB上分配，大对象（很长的字符串和数组）则直接进入老年代）都在EdenSpace上分配。垃圾回收策略现在一般都是采用分代回收机制，新生代中，对象的存活率低，垃圾回收频率比较高，主要采用“复制”算法，老年代中对象的存活率高，垃圾回收频率较低，而且没有额外的空间对它进行分配担保，主要采用“标记-清理”和“标记-整理算法”。


