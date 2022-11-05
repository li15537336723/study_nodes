# 一、MapReduce 概述

## 1.1 定义

MapReduce 是一个==分布式运算程序==的编程框架，是用户开发”基于 Hadoop 的数据分析应用“的核心框架。

MapReduce 核心功能是==将用户编写的业务逻辑代码==和==自带默认组件==整合成一个完整的==分布式运算程序==，并发运行在一个 Hadoop 集群上。

## 1.2 优势劣势

MapReduce: 自己处理业务相关代码 + 自身的默认代码

**优点：**

1、易于编程

2、良好扩展性

3、高容错性

4、适合海量数据计算（TB / PB）几千台服务器共同计算

**缺点**

1、不擅长实时计算

2、不擅长流式计算

不擅长 DAG 有向图五环计算 Spark



## 1.3 WordCount 案例









![image-20220610155050980](https://lixianghong.oss-cn-beijing.aliyuncs.com/typore/image-20220610155050980.png)

常用数据序列化类型

![](https://lixianghong.oss-cn-beijing.aliyuncs.com/typore/image-20220610155455683.png)











# 二、序列化



# 三、核心框架原理

![image-20220611132653617](https://lixianghong.oss-cn-beijing.aliyuncs.com/typore/image-20220611132653617.png)

**MapReducer 详细工作流程**

![image-20220611153124392](https://lixianghong.oss-cn-beijing.aliyuncs.com/typore/image-20220611153124392.png)

有两次排序，环形缓冲区溢出阶段有快速排序。



**MapTask 工作机制**

1、read 阶段

2、Map 阶段

3、Collect 阶段

4、溢写阶段

5、Merge 阶段

**ReduceTask 工作机制**

1、copy 阶段

2、Sort 阶段

3、 Reduce 阶段





















## 3.1 输入的数据 InputFormat









## 3.2 Shuffle

Map 方法之后，Reduce 方法之气那的数据处理过程称之为 Shuffle。







## 3.3 输出数据 OutputFormat

OutpuFormat 是 MapReduce 输出的基类，所有实现 MapReduce 输出都实现了 OutputFormat 接口。



## 3.4 Join

**1）使用场景**

Map Join 适用于一张十分小，一张十分大的场景

**2）优点**

思考：在 Reduce 端处理过多的表，非常容易产生数据倾斜。怎么办？

在 Map 端缓存多张表，提前处理业务逻辑，这样增加 Map 端业务，减少 Reduce 端数据的压力，尽可能地减少数据倾斜

**3）具体办法**

（1）在 Mapper 的 setup 阶段，将文件读取到缓存集合中

（2）在 Driver 驱动类中加载缓存

```java
// 缓存普通文件到 Task 运行节点
job.addCacheFile(new URI("file://e:/cache/pd.txt"));
// 如果是集群运行，需要设置 HDFS 路径
job.addCacheFile(new URI("hdfs://hadoop102:8020/cache/pd.txt"));
```



### Map Join 案例实操

**1）需求**

<img src="https://lixianghong.oss-cn-beijing.aliyuncs.com/typore/image-20220612174846447.png" alt="image-20220612174846447" style="zoom:50%;" />

将商品信息表中数据根据商品 pid 合并到订单数据表中。

<img src="https://lixianghong.oss-cn-beijing.aliyuncs.com/typore/image-20220612174923951.png" alt="image-20220612174923951" style="zoom:50%;" />

**2）需求分析**

MapJoin 适用于关联表中有小表的情形。

<img src="https://lixianghong.oss-cn-beijing.aliyuncs.com/typore/image-20220612175045365.png" alt="image-20220612175045365" style="zoom:50%;" />







## 3.5 ETL (数据清洗)

ETL 是用来描述将数据从数据源经过抽取（Extract），转换（Transform），加载（Load）到目的端的过程。ETL 一词较常用在数据仓库，但其对象并不限于数据仓库

在运行核心业务 MapReduce 程序之前，往往要先对数据进行清晰，清理不掉不符合用户要求的数据。**==清理的过程往往只需要运行 Mapper 程序，不需要运行 Reduce 程序==**

**1）需求**

除去 日志中字段个数小于 11 的日志

（1）输入数据

（2）期望输出数据

​		每行字段长度都大于 11 

**2）需求分析**

需要在 Map 阶段对输入的数据根据规则进行过滤清晰











## 3.6 总结

**1、InputFormat**

1）默认的是 `TextInputFormat 	kv	key偏移量，	v: 一行内容`

2）处理小文件 `CombineTextInputFormat`	把多个文件合并到一起统一切片

**2、Mapper**

`setup()` 初始化；`map()` 用户的业务逻辑；	`clearup()` 关闭资源

**3）分区**

默认分区 HashPartitioner，默认按照 key 的hash 值 % numreducetask 个数自定义分区

**4）排序**

​	1）部分排序	每个输出的文件内部有序

​	2）全排序：一个reduce，对所有数据大排序。

​	3）二次排序：自定义排序范畴，实现 writableCompare 接口，重写 compareTo 方法；总流量倒叙排序，按照上行流量 正序

**5）Combiner**

前提：不影响最终的业务逻

**6）Reducer **

用户的业务逻辑：

setup() 初始化；reduce() 用户的业务逻辑；clearup()；关闭资源

**7）OutputFormat**

1）默认 TextOutputFormat 按行输出到文件

2）自定义

## 四、压缩



## 4.1 有哪些压缩算法



## 4.2 特点



## 4.3 在生产环境怎么用





## 五、常见的问题及解决方案