## Redis 发布订阅

通信	队列

Redis 发布订阅（pub/sub）是一种消息通信模式：发送者（pub）发送消息，订阅者（sub）接收消息。微信、微博、关注系统！

Redis 客户端可以订阅任意数量的频道

订阅/发布消息图：

第一个：消息发送者，第二个：频道，第三个：消息订阅者！

![image-20210223144353527](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210223144353527.png)

下图展示了频道 channel1，以及订阅这个频道的三个客户端 —— client2、client5 和 client1 之间的关系：

![image-20210223144624600](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210223144624600.png)

当有新消息通过 PUBLISH 命令发送给频道 channel1 时，这个消息就会被发送给订阅它的三个客户端：

![image-20210223144709543](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210223144709543.png)

> 测试

订阅端：

```bash
127.0.0.1:6379> subscribe xiaohong		# 订阅一个频道 xiaohong
Reading messages... (press Ctrl-C to quit)
1) "subscribe"
2) "xiaohong"
3) (integer) 1
# 等待读取推送的信息
1) "message"		# 消息
2) "xiaohong"		# 那个频道的消息
3) "hello,xiaohong"	# 消息的具体内容
1) "message"
2) "xiaohong"
3) "hello.redis"
```

发送端：

```bash
127.0.0.1:6379> auth li12345...
OK
127.0.0.1:6379> ping
PONG
127.0.0.1:6379> publish xiaohong "hello,xiaohong"	# 发布者发布消息到频道！
(integer) 1
127.0.0.1:6379> publish xiaohong "hello.redis"		# 发布者发布消息到频道！
(integer) 1
```
