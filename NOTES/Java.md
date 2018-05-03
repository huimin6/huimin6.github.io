<!-- MarkdownTOC -->

- [Java](#java)
    + [多线程](#多线程)
        * [线程的生命周期](#线程的生命周期)
        * [sleep和wait](#sleep和wait)

<!-- /MarkdownTOC -->

# Java
## 多线程
### 线程的生命周期
线程的生命周期：新建(New)、就绪(Runnable)、运行(Running)、阻塞(Blocker)、死亡(Dead)
<div align="center"> <img src="../pictures//thread.jpg"/> </div><br>

### sleep和wait
|sleep和wait比较|
|-|
| sleep是线程类(Thread)的方法，wait是Object类的方法 |
| sleep不释放对象锁，wait释放对象锁 |
| sleep暂停线程、但监控状态仍然保持，结束后会自动恢复 |
| wait后进入等待锁定池，只有针对此对象发出notify方法后获得对象锁进入*可运行状态* |


