RocketMq的架构如下

1.NameServer的作用, Broker用来存储消息, 分发消息

2.RocketMq的消息的consumer有有两种, pull和push

3.RockerMq消息发送默认使用同步方式, 发送方式为至少投递一次, 默认失败后再重试两次, 也就是说有可能会导致消息重复, 需要开发人员去处理消息重复的问题

4.消息发送后的状态有四种, 数据刷盘超时(设置为同步刷盘时), 从Broker不可用(设置为同步master的时), 从broker可用但是同步超时, 发送成功

5.RocketMq的消息消费模式有两种: 集群消费和广播消费, 集群消费模式下一个message只会被一个消费Group中的其中一个consumer消费. 在广播消费模式中,一个message会被一个消费Group的所有consumer消费

6.

