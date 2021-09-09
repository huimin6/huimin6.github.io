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

<details>
@startuml
(Broker) -down-> (NameServer) : 每30s定时发送心跳

(Producer) -down-> (NameServer) : 查询路由信息

note left of (NameServer)
每次收到心跳要
更新lastUpdateTimestamp, 
用来维护brokerLiveTable
end note
@enduml
</details>

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

## 消息发送

1.消息发送方式: 
* 同步(sync): 消息发送后需要等broker的响应结果
* 异步(async): 消息发送后, 等broker进行回调
* 单向(Oneway): 只管消息发送, 不等待响应, 也不需要回调

2.消息发送默认使用同步从方式发送, 在消息发送之前会验证
* 消息内容-不能为空
* topic的长度-必须小于127
* topic是否允许发送(系统中有些topic是不允许发送消息的)
* 消息体(msgBody)-不能为空,长度不能为0,大小不能超过4M

3.在发送消息之前需要找到对应的broker, 通过调用方法 tryToFindTopicPublishInfo(final String topic) 来查找路由信息, 
如果producer中缓存了路由信息则直接从缓存中取. 在 selectOneMessageQueue 选择某个消息队列发送消息时, 消息队列选择算法,
通过比较lastBrokerName的值避开上次发送失败的broker队列, 可以减少消息发送失败的概率.




## 消息消费

1.RocketMq的消息的consumer有两种, pull和push

2.RockerMq消息发送默认使用同步方式, 发送方式为至少投递一次, 默认失败后再重试两次, 也就是说有可能会导致消息重复, 需要开发人员去处理消息重复的问题

3.消息发送后的状态有四种, 数据刷盘超时(设置为同步刷盘时), 从Broker不可用(设置为同步master的时), 从broker可用但是同步超时, 发送成功

4.RocketMq的消息消费模式有两种: 集群消费和广播消费, 集群消费模式下一个message只会被一个消费Group中的其中一个consumer消费. 在广播消费模式中,一个message会被一个消费Group的所有consumer消费

5.消息消费的负载均衡算法有5种, 其核心都是将Topic下所有MessageQueue分配到同一个consumerGroup下的所有consumer中

  push这种方式的消息消费, 其负载均衡的策略由broker完成, pull这种消息消费者, 其负载均衡策略由使用者自己定义
  
6.ProcessQueue是messageQueue的快照, 目的是为了统计当前消息的消费情况, 消息的消费都是通过线程池异步处理的, 提交给线程池后就不好跟踪消息的消费和统计.

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

7.Producer和Consumer的底层实现是MQClientInstance 
  
8.消息过滤-FilterServer
  (1) borker所在的机器会启动多个FilterServer进程
  (2) consumer启动后, 会向filterServer上传一个过滤的java类
  (3) consumer从filterServer拉消息, FilterServer将请求转发给Broker, FilterServer从Broker收到消息后, 按照consumer上传的java过滤程序过滤消息, 
  过滤后返回给consumer
  
## 消息存储

1.RocketMq消息存储在CommitLog文件中, 文件的命名通过存储的第一个消息的偏移量来命名(如: 第一个文件命名为00000000000000000000). 文件存储的目录为 {appname}/store/commitlog/,
  每个文件的大小固定为1G, 存储达到1G就创建一个新的文件. 文件对应的类为org.apache.rocketmq.store.MappedFile, 所有的commitLog 对应MappedFileQueue

2.消息写入的核心逻辑在org.apache.rocketmq.store.DefaultMessageStore#putMessage
  在消息内容写入之前会先获取锁, 可以是PutMessageSpinLock或者PutMessageReentrantLock, 取决于配置
  
3.消息消费队列-ConsumeQueue
  mq消息的存储是乱序存储在commitlog中的, 如果遍历所有commitlog文件查找一条消息将会非常耗时, 所以就按照topic生成了对应的ComsumeQueue文件, 该文件可以看做是消息的索引文件
  ![ConsumeQueue](http://www.plantuml.com/plantuml/png/SoWkIImgAStDuKhEIImkLd3EpoikpKqDB4qjJQtcKW22dFoyT8Nqr1B_jBJYr1BFFB2KKsL8PcwgHbfcNc8EH4K9a0yqAxT0qrfVN-5bmwmN-zkVRUjurhZ-wTePJvjMF9k-xUNqBS_cBuK8FkzS-Nn2szF6_kVBTxzix-UgvN98pKi1kWC0)

<details>
@startuml
class IndexFile{
    boolean putKey(final String key, final long phyOffset, final long storeTimestamp);//插入索引记录
    
    void selectPhyOffset(final List<Long> phyOffsets, final String key, final int maxNum,
        final long begin, final long end, boolean lock);//通过索引key(=topic#消息key)查找消息
}
@enduml
</details>

4.消息索引文件IndexFile

通过过索引查询消息,只要查询到消息的物理偏移量即可, 通过ConsumeQueue查询消息也是同样的道理  
  
![IndexFile](http://www.plantuml.com/plantuml/png/SoWkIImgAStDuKhEIImkLl3CIqcjSClCIQtcKW22fFpydDJ4F8M2qXBlr4gDjCoyn1o5u9AYpBnqXUpKIXq5Y4XEFf1Va57fdvPMd5g28bfSab-K6fAPcmgqABT0qrfV_rd_fAUjIvzlMVHqpzGNwpOytJiLR1MOAClFJ54eJir9JIu9W1a7jTKdixZ4nWTef1t2fc8T1LnEoimhKSXDhF0hpTNXKe00P6SbfQPdvg4uD3KlHGVcNw18SZR8hIyRPhtOtmvnAz1m3TkI_8BCPELdspgUDQu72cW-cx_qMUS-29-hbii1Q0emC040)
 
<details>
@startuml
class ConsumeQueue{
    long getOffsetInQueueByTime(final long timestamp);// 通过消息存储时间查消息物理偏移量
}
@enduml
</details>  
  

