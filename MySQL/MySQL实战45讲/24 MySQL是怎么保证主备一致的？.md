# 24 | MySQL是怎么保证主备一致的

binlog 可以用来归档，也可以用来做主备同步

## MySQL 主备的基本原理

![image-20220322083537284](https://lixianghong.oss-cn-beijing.aliyuncs.com/typore/image-20220322083537284.png)

在状态 1 中，客户端的读写都直接访问节点 A，而节点 B 是 A 的备库，只是将 A 的更新都同步过来，到本地执行。这样可以保持节点 B 和 A 的数量是相同的。



接下来，**看看节点 A 到节点 B 这条线的内部流程是什么样的？**下图中画出一个 update 语句在节点 A 执行，然后同步到节点 B 的完整流程图

<img src="https://lixianghong.oss-cn-beijing.aliyuncs.com/typore/image-20220322085304717.png" alt="image-20220322085304717" style="zoom:50%;" />

改图，包含了 binlog 和 redo log 的写入机制。主库接收到客户端的更新请求后，执行内部事务的更新逻辑，同时写 binlog。

备库 B 跟主库 A 之间维持了一个长连接。主库 A 内部有一个线程，专门用于服务备库 B 的这个长连接。一个事务日志同步的完整过程如下：

1. 在备库 B 上通过change master 命令，设置主库 A 的IP、端口、用户名、密码、以及要从那个位置开始请求binlog ，这个位置包含文件名和日志偏移量。
2. 在备库 B 上执行 start slave 命令，这个时候备库会启动两个线程，io-thread 和 sql-thread。其中 io-thread 负责与主库建立连接
3. 主库 A 校验完用户名、密码后，开始按照备库 B 传过来的位置，读取本地 binlog，发给 B。
4. 备库 B 拿到 binlog 后，写到本地文件，称为中转日志（relay log）。
5. sql-thread 读取中转日志，解析出日志里的命令，并执行。



## 循环复制问题

binlog 的特性确保了在备库执行相同的 binlog ，可以得到与主库相同的状态。

因此，我们可以认证正常情况下主备的数据是一致的。也就是说，上面图中 A、B 两个节点的内容是一致的。上图中画的是 M-S 结构，但实际生产上使用的比较多的是双 M 结构，也就是下面这张图中主备切换流程。

![image-20220322103103249](https://lixianghong.oss-cn-beijing.aliyuncs.com/typore/image-20220322103103249.png)

两个图中区别就是多了一条线。



**解决两个节点间循环复制的问题**

1. 规定两个库的 server id 必须不同，如果相同，则它们之间不能设定为主备关系；
2. 一个备库接到 binlog 并在重放的过程中，生成与原 binlog 的 server id 相同的新的 binlog；
3. 每个库在接收到从自己主库发过来的日之后，先判断server id ，如果跟自己的相同，表示这个日志是自己生成的，就直接丢弃这个日志。

**按照这个逻辑，如果我们设置了双 M 结构，日志的执行流就会变成这样：**

1. 从节点 A 更新的事务，binlog 里面记录的都是 A 的 server id；
2. 传到节点 B 执行一次以后，节点 B 生成的 binlog 的server id 也是 A 的server id；
3. 在传回给节点 A， A 判断到这个 server id 与自己的相同，就不会再处理这个日志，所以，死循环在这里就会断掉。







































































































































