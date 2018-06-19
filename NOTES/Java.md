<!-- MarkdownTOC -->

- [Java](#java)
    + [多线程](#多线程)
        * [线程的生命周期](#线程的生命周期)
        * [sleep和wait](#sleep和wait)
- [Java虚拟机](#java虚拟机)
    + [垃圾回收](#垃圾回收)

<!-- /MarkdownTOC -->

# Java
## 多线程
### 线程的生命周期
线程的生命周期：新建(New)、就绪(Runnable)、运行(Running)、阻塞(Blocker)、死亡(Dead)
<div align="center"> <img src="../pictures//thread.jpg"/> </div><br>

### sleep和wait
|sleep()和wait()比较|
|-|
| sleep()是线程类(Thread)的方法，wait()是Object类的方法 |
| sleep()不释放对象锁，wait()释放对象锁 |
| sleep()暂停线程、但监控状态仍然保持，结束后会自动恢复 |
| wait()后进入等待锁定池，只有针对此对象发出notify()方法后获得对象锁进入**可运行状态**|

# Java虚拟机
## 垃圾回收
Java的垃圾回收主要是回收堆中的对象实例，什么时候回收由系统决定。在垃圾回收之前，首先要通过可达性分析判断对象是否存活，然后将死亡的对象实例所占用的内存空间回收。堆中的内存可以划分为新生代和老年代，新生代内存空间进一步还可以细分为EdenSpace、FromSurvivor、ToSurvivor，新创建的对象实例的内存都在EdenSpace上分配。垃圾回收策略现在一般都是采用分代回收机制，新生代的垃圾回收频率比较高，主要采用“复制”算法，老年代的垃圾回收频率较低，主要采用“标记清除”和“标记整理算法”。


