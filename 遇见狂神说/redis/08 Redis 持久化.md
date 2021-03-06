## Redis 持久化

Redis 是内存数据库，如果不将内存中的数据库状态保存到磁盘，那么一旦服务器进程退出，服务器中的数据库状态也会消失，所以 Redis 提供了持久化功能！

### RDB（Redis DataBase）

> 触发机制

1、save 的规则满足的情况下，会自动出发 rdb 规则

2、执行 flushall 命令，也会出发我们的 rdb 规则！

3、退出 redis，也会产生 rdb 文件！

备份就自动生成一个 dump.rdb

![image-20210223120737255](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210223120737255.png)

> 如果恢复 rdb 文件！

1、只需要将 rdb 文件放在我们的 redis 启动目录下，redis 启动的时候就会自动检查 dump.rdb

2、查看需要存在的位置

```bash
127.0.0.1:6379> config get dir
1) "dir"
2) "D:\\Program\\Redis"		# 如果在这个目录下存在 dump.rdb 文件，启动就会自动恢复其中的数据
```

> 几乎就它自己默认的配置就够用了

**优点：**

1、适合大规模的数据恢复！

2、对数据的完整性要求不高

**缺点：**

1、需要一定的时间间隔进行操作！如果redis 意外宕机了，这个最后一次秀嘎数据就没有的了！

2、fork 进程的时候，会占用一定的内存空间





## AOF （Append Only File）

将我们的所有命令都记录下来，history，恢复的时候就把这个文件全部在执行一遍

> 是什么？

以日志的形式来记录每个写操作，将 Redis 执行过程的所有指令记录下来（读操作不记录），只许追加文件但不可以改写文件，  redis 启动之初会读取该文件重新构建数据，换言之，redis 重启的话就根据日志文件的内容将写指令从前到后执行一次以完成数据的恢复

Aof 保存的是 appendonly.aof 文件

> append

默认是不开启的，我们需要手动配置！我们只需要将 appendonly 改为 yes 就开启了 aof！

重启，redis 就可以生效了！

如果这个 aof 文件有错误，这时候 redis 是启动不起来的，我们需要修复这个 aof 文件

redis 给我们提供了一个工具 `redis-check-aof  --fix`

如果文件正常，重启就可以直接恢复了



> 优点 和 缺点

优点：

1、每一次修改都同步，文件的完整性会更加好！

2、每秒同步一次，可能会丢失一秒的数据！

3、从不同步，效率最高的！

缺点：

1、相对于数据文件来说，aof 远远大于 rdb，修复的速度也比rdb慢！

2、Aof 运行效率也要比 rdb 慢，所以我们 redis 默认的配置就是 rdb 持久化！
