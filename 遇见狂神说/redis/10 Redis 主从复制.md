## Redis 主从复制

主从复制的作用主要包括：

1、数据冗余：主从复制实现了数据的热备份，是持久化之外的一种数据冗余方式

2、故障恢复：当主节点出现问题时，可以又从节点提供服务，实现快速的故障恢复；实际上是一种服务的冗余

3、负载均衡：在主从 复制的基础上，配合读写分离，可以由主节点提供写服务，由从阶段提供读服务（即写 Redis 数据时应用连接注解点，读 Redis 数据时应用连接 从节点）分担服务器负载；尤其是在写少读多的场景下，通过多个从节点分担负载，可以大大提高Redis服务器的并发量

4、高可用（集群）基石：除了上述作用以外，主从复制还是哨兵和集群能够实施的基础，因此说主从复制是 Redis 高可用的基础

一般来说，要将 Redis 运用于工程项目种，只使用一台 Redis 是万万不能的（宕机，一主二从），

![image-20210223150908823](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210223150908823.png)

主从复制，读写分离！80% 的情况下都是在进行读操作！减缓服务器的压力！架构中经常使用！一主二从！

只要在公司中，主从复制就是必须要使用的，因为在真实的项目中不可能单机使用 Redis！

## 环境配置

只配置从库，不用配置主库！

```bash
127.0.0.1:6379> info replication
# Replication
role:master		# 角色	master
connected_slaves:0	# 
master_replid:28bb023111d619dcc97247d66692fa801ef5a01b
master_replid2:0000000000000000000000000000000000000000
master_repl_offset:0
second_repl_offset:-1
repl_backlog_active:0
repl_backlog_size:1048576
repl_backlog_first_byte_offset:0
repl_backlog_histlen:0
```

复制 3 个配置文件，然后修改对应的信息

1、端口

2、pid 名字

3、log 文件名字

4、dump.rdb





### 宕机后手动配置主机



如果主机断开了连接，我们可以使用 `SLAVEOF no one` 让自己变成主机！

























