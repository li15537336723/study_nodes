# 1、MQ 介绍

## 1.1 MQ作用

1. 解耦
2. 流量削峰
3. 数据分发



## 1.2 MQ的优缺点

优点：解耦，削峰，数据分发

缺点包含以下几点：

- 系统可用性降低

系统引入的外部依赖越多，系统稳定性越差，一旦MQ宕机，就会对业务造成影响

如何保证MQ的高可用？

- 系统复杂度提高

MQ的加入大大增加了系统的复杂度，以前系统间是同步的远程调用，现在是通过 MQ 进行一部调用

如果保证消息没有被重复消费？怎么处理消息丢失情况？怎么保证消息传递的顺序性？

- 一致性问题

A 系统处理完业务，通过 MQ 给 B、C、D 三个系统发消息数据，如果B系统，C系统处理成功，D处理失败。如何保证消息数据处理的一致性？





# 2 、RocketMQ集群搭建

## 2.1 各角色介绍

- Producer：消息的发送者；举例：发信者
- Consumer：消息接收者；举例：收信者
- Broker：暂存和传输消息；举例：邮局
- NameServer：管理Broker；举例：各个邮局的管理机构
- Topic：分区消息的种类；一个发送者可以发送消息给一个或者多个 Topic；一个消息的接收者可以订阅一个或者多个 Topic 消息
- Message Queue：相当于Topic的分区；用于并行发送和接收消息



## 2.2 集群模式



```bash
    # nameserver
    192.168.10.102 rockermq-nameserver1
    124.221.52.57 rockermq-nameserver2
    #broker
    192.168.10.102 rocketmq-master1
    192.168.10.102 rocketmq-slave2
    124.221.52.57 rocketmq-master2
    124.221.52.57 rocketmq-slave1
```



```bash
cat > /usr/local/rocketmq/broker-master/conf/broker.conf << EOF

listenPort=10911
brokerIP1=124.221.52.57
brokerClusterName=DefaultCluster
brokerName=broker-a
brokerRole=SYNC_MASTER
brokerId=0
deleteWhen=04
fileReservedTime=48
flushDiskType=ASYNC_FLUSH
namesrvAddr=124.221.52.57:9876;82.157.8.54:9876
EOF

cat > /usr/local/rocketmq/broker-slave/conf/broker.conf << EOF

listenPort=11911
brokerIP1=124.221.52.57
brokerClusterName=DefaultCluster
brokerName=broker-b
brokerRole=SLAVE
brokerId=1
deleteWhen=04
fileReservedTime=48
flushDiskType=ASYNC_FLUSH
namesrvAddr=124.221.52.57:9876;82.157.8.54:9876
EOF

========================
cat > /usr/local/rocketmq/broker-master/conf/broker.conf << EOF

listenPort=10911
brokerIP1=82.157.8.54
brokerClusterName=DefaultCluster
brokerName=broker-b
brokerRole=SYNC_MASTER
brokerId=0
deleteWhen=04
fileReservedTime=48
flushDiskType=ASYNC_FLUSH
namesrvAddr=124.221.52.57:9876;82.157.8.54:9876
EOF

cat > /usr/local/rocketmq/broker-slave/conf/broker.conf << EOF

listenPort=11911
brokerIP1=82.157.8.54
brokerClusterName=DefaultCluster
brokerName=broker-a
brokerRole=SLAVE
brokerId=1
deleteWhen=04
fileReservedTime=48
flushDiskType=ASYNC_FLUSH
namesrvAddr=124.221.52.57:9876;82.157.8.54:9876
EOF

docker run -d --name rmqconsole \
--restart=always \
-p 8080:8080 \
-e "JAVA_OPTS=-Drocketmq.namesrv.addr=124.221.52.57:9876;82.157.8.54:9876 \
-Dcom.rocketmq.sendMessageWithVIPChannel=false" \
apacherocketmq/rocketmq-console:2.0.0
```

























































