# 消息发送样例

- 导入MQ客户端依赖

```xml
<dependency>
    <groupId>org.apache.rocketmq</groupId>
    <artifactId>rocketmq-client</artifactId>
    <version>4.4.0</version>
</dependency>
```

- 消息发送者步骤分析

```
1.创建消息生产者producer，并制定生产者组名
2.指定Nameserver地址
3.启动producer
4.创建消息对象，指定主题Topic、Tag和消息体
5.发送消息
6.关闭生产者producer
```

- 消息消费者步骤分析

```
1.创建消费者Consumer，制定消费者组名
2.指定Nameserver地址
3.订阅主题Topic和Tag
4.设置回调函数，处理消息
5.启动消费者consumer
```



```
server.1=124.221.52.57:2181:3881
server.2=124.221.52.57:2182:3882
server.3=124.221.52.57:2183:3883
```





