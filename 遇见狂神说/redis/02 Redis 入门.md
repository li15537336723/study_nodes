# Redis入门

## 概述

> Redis 是什么？

Redis(**Re**mote **Di**ctionary **S**erver) 即远程字典服务

是一个开源的使用 ANSI C语言编写，支持网络，珂基于内存亦可持久化日志型，Key-Value数据库，并提供多种语言的API



Redis 会周期性的把更新的数据写入磁盘或者把修改操作写入追加的记录文件，并且在此基础上实现了 master-slave(主从)同步。免费和开源！是当下最热门的 NoSQL 技术之一！也被人们称之为结构化数据库！

> Redis 能干嘛！

1、内存存储，持久化，内存中是断电即失、所以说持久化很重要（rdb、aof）

2、效率高，可以用于高速缓存

3、发布订阅系统

4、地图信息分析

5、计时器，计数器（浏览量！）

6、...........

> 特性

1、多样的数据类型

2、持久化

3、集群

4、事物

...........

> 学习中需要用到的东西

1、狂神的公众号：狂神说

2、中文官网：http://www.redis.cn/

3、下载地址：通过官网下载即可

![image-20210222093436579](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210222093436579.png)



注意：Window 在 Github上下载（停更很久了！）

==**Redis 推荐都是在 Linux 服务其上搭建的！**==



## Windows 安装

1、下载redis安装包

2、下载完毕得到压缩包

3、解压到自己电脑上的环境目录下就可以了！redis十分小，只有5M

4、开启 redis

![image-20210222094720843](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210222094720843.png)

5、使用 redis 客户端来连接 redis 

![](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210222094956354.png)



## 测试性能

简单测试一下

```bash
# 测试：100个并发链接    100000请求
redis-benchmark -h localhost -p 6379 -c 100 -n 100000
```



## 基础知识

redis 默认有 16 个数据库

默认使用的是第 0 个，可以使用 select 进行切换数据库

```bash
127.0.0.1.6379> select 3 # 切换数据库
ok
127.0.0.1.6379> dbsize  # 查看数据库大小
(integer) 0
```

![image-20210222105750537](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210222105750537.png)

```bash
127.0.0.1.6379[3]> keys *		# 查看数据库所有的 key
"name"
```

清除当前数据库	`flushdb`

清除全部数据库的内容 `flushall`

```bash
127.0.0.1.6379[3]> flushdb
ok
127.0.0.1.6379[3]> keys *
(empty list or set)
```



> Redis 是单线程的！

明白 Redis 是很快的，官方表示，redis 是基于内存操作，CPU 不是 Redis 性能瓶颈，Redis 的瓶颈是根据及其的内存和网络贷款，既然可以使用单线程来实现，就是用单线程了，所有就是用单线程恶略

Redis 是 C语言写的，官方提供的数据为 100000+ 的QPS，完全不比同样是使用 key-value 的 Memecache 差！

**Redis 为什么单线程还这么快 ？**

误区一：高性能的服务器一定是多线程的？

误区二：多线程(CPU上下文会切换)一定比单线程效率高！

CPU > 内存 > 硬盘 速度要有所了解











