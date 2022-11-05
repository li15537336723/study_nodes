# 一、概述

## 1、HDFS 的产生背景和定义

**背景**：HDFS只是分布式文件管理系统中的一种。

**定义**：HDFS 是要给文件系统，其次，它是分布式的。HDFS 的使用场景：适合一次写入，多次读出的场景





## 2、优缺点

### 优点

**1. 高容错性**

- 数据自动保存多个副本。通过增加副本的形式，提高容错性
- 丢失一个副本之后，可以自动恢复

**2. 适合处理大数据**

- 数据规模：能够处理数据规模达到 GB、TB、甚至 ==PB 级别的数据==
- 文件规模：能够处理 ==百万== 规模以上的==文件数量==，数量相当之大

**3. 可==构建在廉价机器上==**，通过多副本机制，提高可靠性。

### 缺点

- 不适合低延迟数据访问，比如毫秒级的数据存储数据，是做不到的

- ==无法高效的对大量小文件进行存储==
- 不支持并发写入、文件随即修改



## 3、组成

<img src="https://lixianghong.oss-cn-beijing.aliyuncs.com/typore/image-20220609203643478.png" alt="image-20220609203643478" style="zoom:50%;" />

**NameNode：**就是 Master，它是要给主管、管理者

（1）管理 HDFS 的命名空间

（2）配置副本策略

（3）管理数据块（Block）映射信息

（4）处理客户端读写请求。

**DataNode：**就是 Slave。NameNode下达命令，DataNode 执行实际的操作

（1）存储实际的数据块

（2）执行数据块的读 / 写操作。

**Client：就是客户端**

（1）文件切分。文件上传 HDFS 的时候，Client 将文件切分成一个一个的 Block，然后进行上传

（2）与 NameNode 交互，获取文件的位置信息；

（3）与 DataNode 交互，读取或者写入数据

（4）Client 提供一些命令来管理 HDFS，比如 NameNode 格式化

（5）Client 可以通过一些命令来访问 HDFS，比如对 HDFS 增删改查操作

## 4、文件块大小

HDFS 文件快大小（面试重点）







# 二、HDFS 的 Shell 相关操作

**开发的重点**

## 1、上传

**1）-moveFromLocal：从本地剪切==粘贴==到 HDFS**

```bash
vim shuguo.txt
hadoop fs -moveFromLocal ./shuguo.txt /sanguo 
```

**2）-copyFromLocal：从本地系统中 ==拷贝== 文件到 HDFS 路径去**

```bash
vim weiguo.txt
hadoop fs -copyFromLocal weiguo.txt /sanguo
```

**3）-put：等同于 copyFromLocal，生产环境更习惯用 put**

```bash
vim wuguo.txt
hadoop fs -put wuguo.txt /sanguo
```

**4）-appendToFile：追加一个文件到已经存在的文件末尾**

```bash
vim liubei.txt
hadoop fs -addendToFile liubei.txt /sanguo/shuguo.txt
```



## 2、HDFS 下载

**1）-copyToLocal：从 HDFS 拷贝到本地**

```bash
hadoop fs -copyToLocal sanguo/shuguo.txt ./
```

**2）-get：等同于 copyToLocal，生产环境更喜欢用 get**

```bash
hadoop fs -get sanguo/shuguo.txt ./sanguo2.txt
```



## 3、HDFS 直接操作

**1）-ls：显示目录信息**

```bash
hadoop fs -ls /sanguo
```

**2）-cat：显示文件内容**

```bash
hadoop fs -cat /sanguo/sanguo.txt
```

**3）-chgrp、-chmod、chown：Linux 文件系统的用法一样，修改文件所属权限**

```bash
hadoop fs -chmod 666 /sanguo/shuguo.txt
hadoop fs -chown lixianghong:lixianghong /sanguo/shuguo.txt
```

**4）-mkdir：创建路径**

```bash
hadoop fs -mkdir /jinguo
```

**5）-cp：从HDFS 的一个路径拷贝到 HDFS 的另一个路径**

```bash
hadoop fs -cp /sanguo/shuguo.txt /jinguo
```

**6）-mv：在 HDFS 目录中移动文件**

```bash
hadoop fs -mv /sanguo/wuguo.txt /jinguo
hadoop fs -mv /sanguo/weiguo.txt /jinguo
```

**7）-tail：显示一个文件的末尾 1kb 的数据**

```bash
hadoop fs -tail /jinguo/shuguo.txt
```

**8）-rm：删除文件或文件夹**

```bash
hadoop fs -rm /sanguo/shuguo.txt
```

**9）-rm -r：递归删除目录里面的内容**

```bash
hadoop fs -rm - r /sanguo
```

**10）-du：统计文件夹的大小信息**

```bash
hadoop fs -du -s -h /jinguo

hadoop fs -du -h /jinguo
```

**11）-setrep：设置 HDFS 中文件的副本数量**

```bash
hadoop fs -setrep 10 /jinguo/shuguo.txt
```

这里设置的副本数只是记录在 NameNode 的元数据中，是否真的会有这么多副本，还得看 DataNode 的数量。因为目前只有 3 台设备，最多也就3个副本，只有节点数的增加到 10 台时，副本数才能达到 10



# 三、HDFS 的客户端 API

```java
public class HDFSClient {

  public static FileSystem fs;
  @Before
  public void init() throws URISyntaxException, IOException, InterruptedException {
    // 链接的集群 nn 地址
    URI uri = new URI("hdfs://hadoop102:8020");
    // 创建一个配置文件
    Configuration configuration = new Configuration();
    configuration.set("dfs.replication", "2");
    // 用户
    String user = "lixianghong";
    // 1. 获取到客户端对象
    fs = FileSystem.get(uri, configuration, user);
  }

  @After
  public void close() throws IOException {
    fs.close();
  }

  @Test
  public void testMkdir() throws IOException {
    // 2. 创建一个文件夹
//    fs.mkdirs(new Path("/xiyou/huaguoshan1"));
    fs.mkdirs(new Path("/xiyou1/huaguoshan1"));
  }

  // 上传
  /*
    参数优先级
      hdfs-default.cml => hdfs-site.xml => 在项目资源目录下的配置文件 => 代码里面的配置  最高
   */
  @Test
  public void testPut() throws IOException {
    // 参数解读：参数一：表示删除原数据，参数二：是否允许覆盖，参数三：原数据路径，参数四：目的地路径
    fs.copyFromLocalFile(false, true,
            new Path("D:\\qitiandasheng.txt"), new Path("/xiyou1"));
  }

  // 文件下载
  @Test
  public void testGet() throws IOException {
    // 参数的解读；参数一：原文件是否删除，参数二：原文件路径 HDFS；参数三：目标地址路径 win；
    fs.copyToLocalFile(true, new Path("hdfs://hadoop102/xiyou/huaguoshan"),
            new Path("D:\\"), false);
  }

  // 文件删除
  @Test
  public void testRm() throws IOException {
    // 参数解读：参数一：要删除的路径；参数二：是否递归删除
//    fs.delete(new Path("/xiyou1/qitiandasheng.txt"), false);
    // 删除空目录
//    fs.delete(new Path("/xiyou1/huaguoshan1"), false);
    // 删除非空目录
    fs.delete(new Path("/xiyou1"), true);
  }

  // 文件的更名和移动
  @Test
  public void testMv() throws IOException {
    // 参数解读：参数1：原文件路径，参数2：目标文件
    // 对文件的名称的修改
//    fs.rename(new Path("/input/word.txt"), new Path("/input/ss.txt"));
    // 文件的移动和更改
//    fs.rename(new Path("/xiyou/erlangshen.txt"), new Path("/diaochan.txt"));

    // 目录更名
    fs.rename(new Path("/output1"), new Path("/input"));

  }

  // 获取文件详情
  @Test
  public void fileDetail() throws IOException {
    RemoteIterator<LocatedFileStatus> listFiles = fs.listFiles(new Path("/"), true);
    while (listFiles.hasNext()) {
      LocatedFileStatus fileStatus = listFiles.next();
      System.out.println("======" + fileStatus.getPath() + "=========");
      System.out.println(fileStatus.getPermission());
      System.out.println(fileStatus.getOwner());
      System.out.println(fileStatus.getGroup());
      System.out.println(fileStatus.getLen());
      System.out.println(fileStatus.getModificationTime());
      System.out.println(fileStatus.getReplication());
      System.out.println(fileStatus.getBlockSize());
      System.out.println(fileStatus.getPath().getName());

      // 获取快信息
      BlockLocation[] blockLocations = fileStatus.getBlockLocations();
      System.out.println(Arrays.toString(blockLocations));
    }
  }

  // 判断时文件还是文件夹
  @Test
  public void testFile() throws IOException {
    FileStatus[] fileStatuses = fs.listStatus(new Path("/"));
    for (FileStatus fileStatus : fileStatuses) {
      if (fileStatus.isFile()) {
        System.out.println("文件：" + fileStatus.getPath().getName());
      } else {
        System.out.println("目录：" + fileStatus.getPath().getName());
      }
    }
  }

}
```



# 四、HDFS 的读写流程

面试重点







# 五、NN 和 2NN (了解)

NameNode 存储位置 

![image-20220610143143703](https://lixianghong.oss-cn-beijing.aliyuncs.com/typore/image-20220610143143703.png)





# 六、Datanode 工作机制

![image-20220610151156957](https://lixianghong.oss-cn-beijing.aliyuncs.com/typore/image-20220610151156957.png)





















