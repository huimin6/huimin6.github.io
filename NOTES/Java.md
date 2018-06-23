<!-- MarkdownTOC -->

- [Java](#java)
    + [多线程](#多线程)
        * [线程的生命周期](#线程的生命周期)
        * [sleep、wait和yield](#sleep、wait和yield)
    + [动态代理](#动态代理)
- [Java虚拟机](#java虚拟机)
    + [垃圾回收](#垃圾回收)

<!-- /MarkdownTOC -->

# Java
## 多线程
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
| wait()和notify()会对对象的"锁标志"进行操作，所以它们必须在synchronized函数或synchronized代码块中进行调用。如果在non- synchronized函数或non-synchronized代码块中进行调用，虽然能编译通过，但在**运 行时**会发生IllegalMonitorStateException的异常。

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

1、解决了“开闭原则（OCP）”的问题，因为并没有修改Math类，而扩展出了MathProxy类

2、解决了“依赖倒转（DIP）”的问题，通过引入接口

3、解决了“单一职责（SRP）”的问题，Math类不再需要去计算耗时与延时操作，但从某些方面讲MathProxy还是存在该问题

未解决：

4、如果项目中有多个类，则需要编写多个代理类，工作量大，不好修改，不好维护，不能应对变化。

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

# Java虚拟机
## 垃圾回收
Java的垃圾回收主要是回收堆中的对象实例，什么时候回收由系统决定。在垃圾回收之前，首先要通过可达性分析（根节点是虚拟机栈中引用的对象、方法区中类静态属性引用的对象、方法区中常量引用的对象、本地方法栈中JNI引用的对象）判断对象是否存活，然后将死亡的对象实例所占用的内存空间回收。堆所占用的内存可以划分为新生代和老年代，新生代内存空间进一步还可以细分为EdenSpace、FromSurvivor、ToSurvivor，新创建的对象实例的内存大多数（启用本地线程分配缓冲的按照线程优先在TLAB上分配，大对象（很长的字符串和数组）则直接进入老年代）都在EdenSpace上分配。垃圾回收策略现在一般都是采用分代回收机制，新生代中，对象的存活率低，垃圾回收频率比较高，主要采用“复制”算法，老年代中对象的存活率高，垃圾回收频率较低，而且没有额外的空间对它进行分配担保，主要采用“标记-清理”和“标记-整理算法”。


