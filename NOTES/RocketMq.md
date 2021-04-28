# RocketMQ

RocketMq的架构如下

1.NameServer的作用主要用来管理路由信息, 为消息消费者和生产者提供路由信息

![NamesrvController](http://www.plantuml.com/plantuml/png/ZPBDRi8m48JlVWgBOzI81oYAw8-gLeLABQZt9Ta4YusDrkjSghvxWzrKb0Z1SMT6tqwycNi1bj2IMXiXr9CtQD5pz-2ii2D2dYXL4dYlHywNocjZWVJhPo_Mfbc2XGPPxxf_yn3xS47mnTPkoF69eBw7NQVHVfLEa6VmsoNiPojXOvjffiNjJQcpwOrU62-pzu017q6WA98LXJLi2CypDknso8SFZA3xE4R8htLNLNo1KLBmSWHPePnPK1H3-0zxUw5RJ1vrMvWkFa_gb-O8kfeJ7okPHUy-6HBalZHto8UEkevA4XAwlyxe7z1PpgRPIZuwcPgZhK9BFm00)

<details>
@startuml
Title "NamesrvController"

class NamesrvController{
-final KVConfigManager kvConfigManager
-final RouteInfoManager routeInfoManager
}

class RouteInfoManager{
- final HashMap<String/* topic */, List<QueueData>> topicQueueTable
- final HashMap<String/* brokerName */, BrokerData> brokerAddrTable
- final HashMap<String/* clusterName */, Set<String/* brokerName */>> clusterAddrTable;
- final HashMap<String/* brokerAddr */, BrokerLiveInfo> brokerLiveTable
- final HashMap<String/* brokerAddr */, List<String>/* Filter Server */> filterServerTable
}

NamesrvController *-right- RouteInfoManager
@enduml
</details>

NameServer和Broker之间保持长连接, Broker的状态通过brokerLiveTable保存. NameServer收到Broker发来的心跳包更新brokerLiveTable, 和路由信息(topicQueueTable, brokerAddrTable, clusterAddrTable和filterServerTable). 在更新

![NameServer和Broker之间的交互](http://www.plantuml.com/plantuml/png/SoWkIImgAStDuT9mAihFJYtILD1DoI_FqxLJqF1Bp4qDJYqg0mbQAJnRtsziKFnqtQndqxSzxP_uig7nwVxc5zitSt5f11JbfvGcuzCxV-cBzOjUR5__VCh69_iNFcjSpyMLbr-Igf2JcbQYa9-6efuBQDEpDGkVJTdsj6C3n8uNongvditUycpQXfp4ufBGWfJ4ajIGpDnKeEDp2tGKk9vFMV5ixjBdkxSywrgI1FQ6JsPPQaXYKaugLsfUYWB8BYu780leKG00)

<detail>
@startuml
(Broker) -down-> (NameServer) : 每30s定时发送心跳

(Producer) -down-> (NameServer) : 查询路由信息

note left of (NameServer)
每次收到心跳要
更新lastUpdateTimestamp, 
用来维护brokerLiveTable
end note
@enduml
</detail>

## 路由

路由信息的发现不是实时的, 是客户端定期拉取最新的路由信息, 通过RouteInfoManager中的方法 pickupTopicRouteData()来获取

RouteInfoManager中的方法 scanNotActiveBroker() 用来扫描删除不活跃的broker信息, 默认超过2分钟没有心跳就删除

![RouteInfoManager](http://www.plantuml.com/plantuml/png/LOqn3i8m34Ltdy9ZgrwY81O6680BMAbRMGrsvJXT47Sdi37-__JqnXBaKPyPKgPIy6Or-AopNKWNTdp1i9jCM1BfrUAGGdO-kgtiNG_3mpP9F-T4GTZ6MhV_ivj5AUCzN7J1-B8UtZ2oJYtx0G00)

<details>
@startuml
class RouteInfoManger {
+void scanNotActiveBroker()
+TopicRouteData pickupTopicRouteData(final String topic)
}
@enduml
</details>

## 消息消费

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
</details>

8.Producer和Consumer的底层实现是MQClientInstance 

