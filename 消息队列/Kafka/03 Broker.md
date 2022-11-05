# 一、节点服役和退役

## 1.1、服役新节点

**1）新节点准备**

（1）关闭 Hadoop104，并克隆一个 105

（2）开启 hadoop105，修改 IP 地址

```bash
IPADDR=192.168.10.105
```

（3）在 hadoop105 上，修改主机名称为 hadoop105

```bash
vim /etc/hostname
hadoop105
```

（4）在 xshell 中启动hadoop，并在102，103，104启动 zookeeper 和 kafka

**2）执行负载均衡操作**

（1）创建一个均衡的主题

```bash
vim topics-to-move.json
{
	"topics": [
		{"topic": "first"}
	],
	"version": 1
}
```

（2）生成一个负载均衡的计划

```bash
bin/kafka-reassign-partitions.sh --bootstrap-server hadoop102:9092 --topics-to-move-json-file topics-to-move.json --broker-list "0,1,2,3" --generate
```

（3）创建副本存储计划（所有副本存储在 broker0、broker1、broker2、broker3 中）。

```bash
vim increase-replication-factor.json
```

（4）执行副本存储计划

```bash
bin/kafka-reassign-partitions.sh --bootstrap-server hadoop102:9092 --reassignment-json-file increase-replication-factor.json --execute
```

（5）进行验证

```bash
bin/kafka-reassign-partitions.sh --bootstrap-server hadoop102:9092 --reassignment-json-file increase-replication-factor.json --verify
```



## 1.2 退役旧节点

**1）执行负载均衡操作**

先按照退役一台节点，==生成执行计划==，然后按照服役时操作流程==执行负载均衡==

（1）创建一个均衡的主题

```bash
vim topics-to-move.json
{
	"topics": [
		{"topic": "first"}
	],
	"version": 1
}
```

（2）创建执行计划

```bash
bin/kafka-reassign-partitions.sh --bootstrap-server hadoop102:9092 --topics-to-move-json-file topics-to-move.json --broker-list "0,1,2" --generate
```

（3）创建副本存储计划（所有副本存储在 broker0、broker1、broker2、broker3 中）。

```bash
vim increase-replication-factor.json
```

（4）执行副本存储计划

```bash
bin/kafka-reassign-partitions.sh --bootstrap-server hadoop102:9092 --reassignment-json-file increase-replication-factor.json --execute
```

（5）进行验证

```bash
bin/kafka-reassign-partitions.sh --bootstrap-server hadoop102:9092 --reassignment-json-file increase-replication-factor.json --verify
```

（6）将 kafka 关闭掉即可

**服役和退役的过程基本差不多**



# 二、Kafka 副本

## 2.1 副本基本信息

（1）Kafka 副本作用：提高数据可靠性

（2）Kafka 默认副本 1 个，生产环境一般配置为 2 个，保证数据可靠性；太多副本会增加磁盘存储空间，增加网络上数据传输，降低效率

（3）Kafka 中副本分为：Leader 和 Follower。Kafka 生产者只会把数据发往 Leader，然后 Follower 找 Leader 进行同步数据。







