RocketMq的架构如下

1.NameServer的作用主要用来管理路由信息




2.RocketMq的消息的consumer有有两种, pull和push

3.RockerMq消息发送默认使用同步方式, 发送方式为至少投递一次, 默认失败后再重试两次, 也就是说有可能会导致消息重复, 需要开发人员去处理消息重复的问题

4.消息发送后的状态有四种, 数据刷盘超时(设置为同步刷盘时), 从Broker不可用(设置为同步master的时), 从broker可用但是同步超时, 发送成功

5.RocketMq的消息消费模式有两种: 集群消费和广播消费, 集群消费模式下一个message只会被一个消费Group中的其中一个consumer消费. 在广播消费模式中,一个message会被一个消费Group的所有consumer消费

6.消息消费的负载均衡算法有5种, 其核心都是将Topic下所有MessageQueue分配到同一个consumerGroup下的所有consumer中

  push这种方式的消息消费, 其负载均衡的策略由broker完成, pull这种消息消费者, 其负载均衡策略由使用者自己定义
  
7.ProcessQueue是messageQueue的快照, 目的是为了统计当前消息的消费情况, 消息的消费都是通过线程池异步处理的, 提交给线程池后就不好跟踪消息的消费和统计.

![ProcessQueue类图](http://www.plantuml.com/plantuml/png/SoWkIImgAStDuU8AoIp9ILLG2YZAJqujBWuiJIrDVRvnzzFP-vIuk99p4ekB5P2bi8bLS-ag51HbbYMMf2e4fIQcX1TbbgJwv2TdA-G0YP2Yr5JVn085MuMyr7AWV6fUIL5YNWcAGWrD92EW6cnyylFITHHyWROafgUwLfJOAUGMfqCbkMgv75BpKe0U0000)

<details>
@startuml
Title "ProcessQueue类图"

class ProcessQueue

class ProcessQueue{
- private ReadWriteLock lockTreeMap = new ReentrantReadWriteLock()
- private TreeMap<Long, MessageExt> msgTreeMap
}
@enduml
<details>

8.Producer和Consumer的底层实现是MQClientInstance 

