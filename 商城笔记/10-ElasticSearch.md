![image.png](https://fynotefile.oss-cn-zhangjiakou.aliyuncs.com/fynote/1462/1644651801000/ee1f9860a2a34b0e94796dfe61d88904.png)

# ElasticSearch

# 一、ElasticSearch概述

## 1.ElasticSearch介绍

&emsp;&emsp;ES 是一个开源的**高扩展的分布式全文搜索引擎**，是整个Elastic Stack技术栈的核心。它可以近乎实时的存储，检索数据；本身扩展性很好，可以扩展到上百台服务器，处理PB级别的数据。

&emsp;&emsp;ElasticSearch的底层是开源库Lucene，但是你没办法直接用Lucene，必须自己写代码去调用它的接口，Elastic是Lucene的封装，提供了REST API的操作接口，开箱即用。天然的跨平台。

&emsp;&emsp;全文检索是我们在实际项目开发中最常见的需求了，而ElasticSearch是目前全文检索引擎的首选，它可以快速的存储，搜索和分析海量的数据，维基百科，GitHub，Stack Overflow都采用了ElasticSearch。

官方网站：https://www.elastic.co/cn/elasticsearch/

中文社区：https://elasticsearch.cn/explore/

## 2.ElasticSearch用途

1. 搜索的数据对象是大量的非结构化的文本数据。
2. 文件记录达到数十万或数百万个甚至更多。
3. 支持大量基于交互式文本的查询。
4. 需求非常灵活的全文搜索查询。
5. 对高度相关的搜索结果的有特殊需求，但是没有可用的关系数据库可以满足。
6. 对不同记录类型，非文本数据操作或安全事务处理的需求相对较少的情况。

## 3. ElasticSearch基本概念

![image.png](https://fynotefile.oss-cn-zhangjiakou.aliyuncs.com/fynote/1462/1644651801000/94fe255dec514382954823229fb5deb3.png)

### 3.1 索引

&emsp;&emsp;索引（indices）在这儿很容易和MySQL数据库中的索引产生混淆，其实是和MySQL数据库中的Databases数据库的概念是一致的。

### 3.2 类型

&emsp;&emsp;类型(Type),对应的其实就是数据库中的 Table(数据表)，类型是模拟mysql中的table概念，一个索引库下可以有不同类型的索引，比如商品索引，订单索引，其数据格式不同。

### 3.3 文档

&emsp;&emsp;文档(Document),对应的就是具体数据行(Row)

### 3.4 字段

&emsp;&emsp;字段(field)相对于数据表中的列，也就是文档中的属性。

## 4. 倒排索引

&emsp;&emsp;Elasticsearch是通过Lucene的倒排索引技术实现比关系型数据库更快的过滤。特别是它对多条件的过滤支持非常好.

&emsp;&emsp;倒排索引是搜索引擎的核心。搜索引擎的主要目标是在查找发生搜索条件的文档时提供快速搜索。ES中的倒排索引其实就是 lucene 的倒排索引，区别于传统的正向索引，倒排索引会再存储数据时将关键词和数据进行关联，保存到倒排表中，然后查询时，将查询内容进行分词后在倒排表中进行查询，最后匹配数据即可。

![image.png](https://fynotefile.oss-cn-zhangjiakou.aliyuncs.com/fynote/1462/1644651801000/dad53ed6f080419593dc6d5e090b7118.png)

![image.png](https://fynotefile.oss-cn-zhangjiakou.aliyuncs.com/fynote/1462/1644651801000/34620b0154a64f03a970a4c360bad35d.png)

![image.png](https://fynotefile.oss-cn-zhangjiakou.aliyuncs.com/fynote/1462/1644651801000/692c1c7773ac4c40a1688ac1b13bb961.png)

具体拆解的案例

| 词     | 记录          |
| ------ | ------------- |
| 红海   | 1，2，3，4，5 |
| 行动   | 1，2，3       |
| 探索   | 2，5          |
| 特别   | 3，5          |
| 记录篇 | 4             |
| 特工   | 5             |

保存的对应的记录为

> 1-红海行动
>
> 2-探索红海行动
>
> 3-红海特别行动
>
> 4-红海记录篇
>
> 5-特工红海特别探索

分词：将整句分拆为单词

检索信息：

1. 红海特工行动?
2. 红海行动？

# 二、ElasticSearch相关安装

![image.png](https://fynotefile.oss-cn-zhangjiakou.aliyuncs.com/fynote/1462/1644651801000/eae1bec6ad04431b9f15327bd75ef343.png)

## 1.Elasticsearch安装

&emsp;&emsp;ElasticSearch安装就相当于安装MySQL数据库。

下载对应的镜像文件

```shell
docker pull elasticsearch:7.4.2
```

创建需要挂载的目录

> mkdir -p /mydata/elasticsearch/config
>
> mkdir -p /mydata/elasticsearch/data
>
> echo "http.host : 0.0.0.0" >> /mydata/elasticsearch/config/elasticsearch.yml

安装ElasticSearch容器

> docker run --name elasticsearch -p 9200:9200 -p 9300:9300 \-e "discovery.type=single-node" -e ES_JAVA_OPTS="-Xms64m -Xmx128m" -v /mydata/elasticsearch/config/elasticsearch.yml:/usr/share/elasticsearch/config/elasticsearch.yml -v /mydata/elasticsearch/data:/usr/share/elasticsearch/data -v /mydata/elasticsearch/plugins:/usr/share/elasticsearch/plugins -d elasticsearch:7.4.2

启动异常：

elasticsearch.yml配置文件的 `:` 两边需要添加空格

还有就是访问的文件权限问题：

![image.png](https://fynotefile.oss-cn-zhangjiakou.aliyuncs.com/fynote/1462/1644651801000/c63747a2cfe04eeca7cdfeeafe4d9f04.png)

没有权限我们就添加权限就可以了

chmod -R 777 /mydata/elasticsearch/

然后我们就可以启动容器了

docker start 容器编号

![image.png](https://fynotefile.oss-cn-zhangjiakou.aliyuncs.com/fynote/1462/1644651801000/6de25a054a41450fb5992082bc0d2d1f.png)

然后测试访问：http://192.168.56.100:9200

![image.png](https://fynotefile.oss-cn-zhangjiakou.aliyuncs.com/fynote/1462/1644651801000/55a524f911f44c2c8c5f119148e21250.png)

看到这个效果表示安装成功！

## 2.Kibanan安装

&emsp;&emsp;Kibanan的安装就相当于安装MySQL的客户端SQLYog。

下载镜像文件

```shell
docker pull kibana:7.4.2
```

启动容器的命令

> docker run --name kibana -e ELASTICSEARCH_HOSTS=http://192.168.56.100:9200 -p 5601:5601 -d kibana:7.4.2

测试访问：http://192.168.56.100:5601

![image.png](https://fynotefile.oss-cn-zhangjiakou.aliyuncs.com/fynote/1462/1644651801000/5e8ff6b4b4b145379d56c400649d6cd2.png)

如果查看日志：docker logs 容器编号

![image.png](https://fynotefile.oss-cn-zhangjiakou.aliyuncs.com/fynote/1462/1644651801000/aac3a786ae8f445298059efad7f19e95.png)

那么我们就手动的进入容器中修改ElasticSearch的服务地址

> docker exec -it 容器编号  /bin/bash
>
> 进入config目录
>
> cd config
>
> 修改kibana.yml文件中的ElasticSearch的服务地址

![image.png](https://fynotefile.oss-cn-zhangjiakou.aliyuncs.com/fynote/1462/1644651801000/960552b8aa7048ba9f6250066b9d1d49.png)

然后我们重启Kibana服务

![image.png](https://fynotefile.oss-cn-zhangjiakou.aliyuncs.com/fynote/1462/1644651801000/98164201a56b4f328082a8841ebf08b4.png)

看到如下界面表示安装启动成功

![image.png](https://fynotefile.oss-cn-zhangjiakou.aliyuncs.com/fynote/1462/1644651801000/ac5d69d8620a463da77e24eb6a4a2bf3.png)

# 三、ElasticSearch入门

## 1._cat

| _cat接口          | 说明             |
| ----------------- | ---------------- |
| GET /_cat/nodes   | 查看所有节点     |
| GET /_cat/health  | 查看ES健康状况   |
| GET /_cat/master  | 查看主节点       |
| GET /_cat/indices | 查看所有索引信息 |

/_cat/indices?v 查看所有的索引信息

![image.png](https://fynotefile.oss-cn-zhangjiakou.aliyuncs.com/fynote/1462/1644651801000/287330730323401a80593c1e683bdc4b.png)

es 中会默认提供上面的几个索引，表头的含义为：

| 字段名         | 含义说明                                                     |
| -------------- | ------------------------------------------------------------ |
| health         | green(集群完整) yellow(单点正常、集群不完整) red(单点不正常) |
| status         | 是否能使用                                                   |
| index          | 索引名                                                       |
| uuid           | 索引统一编号                                                 |
| pri            | 主节点几个                                                   |
| rep            | 从节点几个                                                   |
| docs.count     | 文档数                                                       |
| docs.deleted   | 文档被删了多少                                               |
| store.size     | 整体占空间大小                                               |
| pri.store.size | 主节点占                                                     |

## 2.索引操作

索引就相当于我们讲的关系型数据库MySQL中的 database

### 2.1 创建索引

> PUT /索引名

参数可选：指定分片及副本，默认分片为3，副本为2。

```json
{
    "settings": {
        "number_of_shards": 3,
        "number_of_replicas": 2
      }
}
```

![image.png](https://fynotefile.oss-cn-zhangjiakou.aliyuncs.com/fynote/1462/1644651801000/e457ed262f504188a8fafc3f1c93829a.png)

### 2.2 查看索引信息

> GET /索引名

![image.png](https://fynotefile.oss-cn-zhangjiakou.aliyuncs.com/fynote/1462/1644651801000/952c0e8ed5d04cc1a7387f5a2938ea9a.png)

或者，我们可以使用*来查询所有索引具体信息

![image.png](https://fynotefile.oss-cn-zhangjiakou.aliyuncs.com/fynote/1462/1644651801000/f5b0d09041dc48ea9d44b1116d1b9fc7.png)

### 2.3 删除索引

> DELETE /索引名称

![image.png](https://fynotefile.oss-cn-zhangjiakou.aliyuncs.com/fynote/1462/1644651801000/bd348d3bf1134db29c52e55bb37afc22.png)

## 3.文档操作

文档相当于数据库中的表结构中的Row记录

### 3.1 创建文档

> PUT /索引名称/类型名/编号

数据

```json
{
   "name":"bobo"
}
```

![image.png](https://fynotefile.oss-cn-zhangjiakou.aliyuncs.com/fynote/1462/1644651801000/e59a0a7faaa8485787ca4619cbd13e51.png)

| 提交方式 | 描述                                                              |
| -------- | ----------------------------------------------------------------- |
| PUT      | 提交的id如果不存在就是新增操作，如果存在就是更新操作，id不能为空  |
| POST     | 如果不提供id会自动生成一个id,如果id存在就更新，如果id不存在就新增 |

> POST /索引名称/类型名/编号

![image.png](https://fynotefile.oss-cn-zhangjiakou.aliyuncs.com/fynote/1462/1644651801000/5769eb60c8b941cbaa62f3db287323be.png)

![image.png](https://fynotefile.oss-cn-zhangjiakou.aliyuncs.com/fynote/1462/1644651801000/a15bbbb6493f486a8944243c308e161b.png)

### 3.2 查询文档

> GET /索引/类型/id

![image.png](https://fynotefile.oss-cn-zhangjiakou.aliyuncs.com/fynote/1462/1644651801000/438793e1ad5645ebb9d55b67fbb598f8.png)

返回字段的含义

| 字段          | 含义                                         |
| ------------- | -------------------------------------------- |
| _index        | 索引名称                                     |
| _type         | 类型名称                                     |
| _id           | 记录id                                       |
| _version      | 版本号                                       |
| _seq_no       | 并发控制字段，每次更新都会+1，用来实现乐观锁 |
| _primary_term | 同上，主分片重新分配，如重启，就会发生变化   |
| found         | 找到结果                                     |
| _source       | 真正的数据内容                               |

乐观锁： ?if_seq_no=0&if_primary_term=1

![image.png](https://fynotefile.oss-cn-zhangjiakou.aliyuncs.com/fynote/1462/1644651801000/4be0c9d1fc85418aa3925f1253ee87d9.png)

![image.png](https://fynotefile.oss-cn-zhangjiakou.aliyuncs.com/fynote/1462/1644651801000/63fe97b8b4074c0b94d60a3d9e798ce3.png)

### 3.3 更新文档

&emsp;&emsp;前面的POST和PUT添加数据的时候，如果id存在就会执行更新文档的操作，当然我们也可以通过POST方式提交，然后显示的跟上_update来实现更新

> POST /索引/类型/id/_update

```json
{
   "doc":{
       "name":"bobo666"
   }
}
```

这种方式来更新，只是这种方式的更新如果数据没有变化则不会操作。

![image.png](https://fynotefile.oss-cn-zhangjiakou.aliyuncs.com/fynote/1462/1644651801000/a5b62baeb3ba4d4397e648e46796fba0.png)

如果更新的数据和文档中的数据是一样的，那么POST方式提交是不会有任何操作的

![image.png](https://fynotefile.oss-cn-zhangjiakou.aliyuncs.com/fynote/1462/1644651801000/8d758ea68a0048c094b8b59a2d50258e.png)

### 3.4 删除文档

> DELETE /索引/类型/id
>
> DELETE /索引

![image.png](https://fynotefile.oss-cn-zhangjiakou.aliyuncs.com/fynote/1462/1644651801000/23c70d873d0f4cfca2b47448145a9393.png)

### 3.5 测试数据

_bulk批量操作，语法格式

```json
{action:{metadata}}\n
{request body }\n
{action:{metadata}}\n
{request body }\n
```

案例

```json
POST /bobo/system/_bulk
{"index":{"_id":"1"}}
{"name":"dpb"}
{"index":{"_id":"2"}}
{"name":"dpb2"}
```

![image.png](https://fynotefile.oss-cn-zhangjiakou.aliyuncs.com/fynote/1462/1644651801000/f7f89ca577224ba19a58aaf4ac82a003.png)

复杂点的案例：

```json
POST /_bulk
{"delete":{"_index":"website","_type":"blog","_id":"123"}}
{"create":{"_index":"website","_type":"blog","_id":"123"}}
{"title":"My first bolg post ..."}
{"index":{"_index":"website","_type":"blog"}}
{"title":"My second blog post ..."}
{"update":{"_index":"website","_type":"blog","_id":"123"}}
{"doc":{"title":"My updated blog post ..."}}

```

官方测试数据：[https://github.com/elastic/elasticsearch/blob/master/docs/src/test/resources/accounts.json](https://github.com/elastic/elasticsearch/blob/master/docs/src/test/resources/accounts.json)

# 四、ElasticSearch进阶

https://www.elastic.co/guide/en/elasticsearch/reference/7.4/getting-started-search.html

## 1.ES中的检索方式

在ElasticSearch中支持两种检索方式

1. 通过使用REST request URL 发送检索参数(uri+检索参数)
2. 通过使用 REST request body 来发送检索参数 (uri+请求体)

### 第一种方式

> GET bank/_search # 检索bank下的所有信息，包括 type 和 docs
>
> GET bank/_search?q=*&sort=account_number:asc

响应结果信息

| 信息             | 描述                                          |
| ---------------- | --------------------------------------------- |
| took             | ElasticSearch执行搜索的时间(毫秒)             |
| time_out         | 搜索是否超时                                  |
| _shards          | 有多少个分片被搜索了，统计成功/失败的搜索分片 |
| hits             | 搜索结果                                      |
| hits.total       | 搜索结果统计                                  |
| hits.hits        | 实际的搜索结果数组(默认为前10条文档)          |
| sort             | 结果的排序key，没有就按照score排序            |
| score和max_score | 相关性得分和最高分(全文检索使用)              |

![image.png](https://fynotefile.oss-cn-zhangjiakou.aliyuncs.com/fynote/1462/1644651801000/d82de29b2de64da888eb7350ebedac87.png)

### 第二种方式

通过使用 REST request body 来反射检索参数 (uri+请求体)

> GET bank/_search

```json
{
   "query":{
       "match_all":{}
    },
    "sort":[
       {
           "account_number":"desc"  
       }
   ]
}
```

![image.png](https://fynotefile.oss-cn-zhangjiakou.aliyuncs.com/fynote/1462/1644651801000/8d088d187da04706b6398661e6b3beda.png)

## 2.Query DSL

### 2.1 基本语法

&emsp;&emsp;ElasticSearch提供了一个可以执行的JSON风格的DSL(domain-specific language 领域特定语言)，这个被称为Query DSL，该查询语言非常全面，并且刚开始的时候感觉有点复杂，真正学好它的方法就是从一些基础案例开始的。

完整的语法结构

```json
{
   QUERY_NAME:{
      ARGUMENT:VALUE,
      ARGUMENT:VALUE,...
   }
}
```

如果是针对某个字段，那么它的结构为

```json
{
    QUERY_NAME:{
        FIELD_NAME:{
            ARGUMENT:VALUE,
            ARGUMENT:VALUE,...
        }
    }
}
```

![image.png](https://fynotefile.oss-cn-zhangjiakou.aliyuncs.com/fynote/1462/1644651801000/c21123a5634e47b5bae27808e74b24ee.png)

### 2.2 match

&emsp;&emsp;上面我们用到来的match_all是匹配所有的数据，而我们现在要讲的match是条件匹配

如果对应的字段是基本类型(非字符串类型)，则是精确匹配。

```json
GET bank/_search
{
   "query":{
       "match":{
          "account_number":20
      }
   }
}
```

match返回的是 account_number:20的记录

![image.png](https://fynotefile.oss-cn-zhangjiakou.aliyuncs.com/fynote/1462/1644651801000/592c0054f95046bebb54b98f1c69d189.png)

如果对应的字段是字符串类型，则是全文检索

```json
GET bank/_search
{
   "query":{
       "match":{
          "address":"mill"
      }
   }
}
```

match返回的就是address中包含mill字符串的记录

![image.png](https://fynotefile.oss-cn-zhangjiakou.aliyuncs.com/fynote/1462/1644651801000/843931773e3749bfb5f63d1d498df460.png)

### 2.3 match_phrase

将需要匹配的值当成一个整体单词(不分词)进行检索，短语匹配

```json
GET bank/_search
{
   "query":{
       "match_phrase":{
          "address":"mill road"
      }
   }
}
```

查询出address中包含 mill road的所有记录，并给出相关性得分

![image.png](https://fynotefile.oss-cn-zhangjiakou.aliyuncs.com/fynote/1462/1644651801000/395a62e11c0e4597a790ea948ebe0966.png)

### 2.4 multi_match[多字段匹配]

```json
GET bank/_search
{
   "query":{
       "multi_match":{
          "query":"mill road",
          "fields":["address","state"]
      }
   }
}
```

查询出state或者address中包含 mill road的记录

![image.png](https://fynotefile.oss-cn-zhangjiakou.aliyuncs.com/fynote/1462/1644651801000/a3eb88a2965f4587a5e5a90514aec89f.png)

### 2.5 bool[复合查询]

布尔查询又叫**组合查询**，bool用来实现复合查询，

`bool`把各种其它查询通过 `must`（与）、`must_not`（非）、`should`（或）的方式进行组合

复合语句可以合并任何其他查询语句，包括复合语句也可以合并，了解这一点很重要，这意味着，复合语句之间可以相互嵌套，可以表达非常复杂的逻辑。

```json
GET /bank/_search
{
  "query": {
    "bool": {
      "must": [
        { "match": { "age": "40" } }
      ],
      "must_not": [
        { "match": { "state": "ID" } }
      ]
    }
  }
}
```

![image.png](https://fynotefile.oss-cn-zhangjiakou.aliyuncs.com/fynote/1462/1644651801000/7e870b14a46246e4a8294db3eb79a7fc.png)

### 2.6 filter[结果过滤]

&emsp;&emsp;并不是所有的查询都需要产生分数，特别是那些仅用于"filtering"的文档，为了不计算分数，ElasticSearch会自动检查场景并且优化查询的执行。

```json
GET /bank/_search
{
  "query": {
    "bool": {
      "must": { "match_all": {} },
      "filter": {
        "range": {
          "balance": {
            "gte": 20000,
            "lte": 30000
          }
        }
      }
    }
  }
}
```

![image.png](https://fynotefile.oss-cn-zhangjiakou.aliyuncs.com/fynote/1462/1644651801000/939aae34fb9849c482fb6547f2a68dba.png)

### 2.7 term

&emsp;&emsp;和match一样，匹配某个属性的值，全文检索字段用match，其他非text字段匹配用term

```json
GET bank/_search
{
   "query":{
       "term":{
          "account_number":20
      }
   }
}
```

![image.png](https://fynotefile.oss-cn-zhangjiakou.aliyuncs.com/fynote/1462/1644651801000/0890abbae7044f0cabb85e6271b62555.png)

| 检索关键字    | 描述                                       |
| ------------- | ------------------------------------------ |
| term          | 非text使用                                 |
| match         | 在text中我们实现全文检索-分词              |
| match keyword | 在属性字段后加.keyword 实现精确查询-不分词 |
| match_phrase  | 短语查询，不分词，模糊查询                 |

## 3.聚合(aggregations)

聚合可以让我们极其方便的实现对数据的统计、分析。例如：

* 什么品牌的手机最受欢迎？
* 这些手机的平均价格、最高价格、最低价格？
* 这些手机每月的销售情况如何？

实现这些统计功能的比数据库的sql要方便的多，而且查询速度非常快，可以实现实时搜索效果。

语法规则

```json
"aggregations" : {
    "<aggregation_name>" : {
        "<aggregation_type>" : {
            <aggregation_body>
        }
        [,"meta" : {  [<meta_data_body>] } ]?
        [,"aggregations" : { [<sub_aggregation>]+ } ]?
    }
    [,"<aggregation_name_2>" : { ... } ]*
}
```

https://www.elastic.co/guide/en/elasticsearch/reference/7.4/search-aggregations.html

### 3.1 基本概念

Elasticsearch中的聚合，包含多种类型，最常用的两种，一个叫 `桶`，一个叫 `度量`：

> **桶（bucket）**

桶的作用，是按照某种方式对数据进行分组，每一组数据在ES中称为一个 `桶`，例如我们根据国籍对人划分，可以得到 `中国桶`、`英国桶`，`日本桶`……或者我们按照年龄段对人进行划分：0~10,10~20,20~30,30~40等。

Elasticsearch中提供的划分桶的方式有很多：

* Date Histogram Aggregation：根据日期阶梯分组，例如给定阶梯为周，会自动每周分为一组
* Histogram Aggregation：根据数值阶梯分组，与日期类似
* Terms Aggregation：根据词条内容分组，词条内容完全匹配的为一组
* Range Aggregation：数值和日期的范围分组，指定开始和结束，然后按段分组
* ……

bucket aggregations 只负责对数据进行分组，并不进行计算，因此往往bucket中往往会嵌套另一种聚合：metrics aggregations即度量

> **度量（metrics）**

分组完成以后，我们一般会对组中的数据进行聚合运算，例如求平均值、最大、最小、求和等，这些在ES中称为 `度量`

比较常用的一些度量聚合方式：

* Avg Aggregation：求平均值
* Max Aggregation：求最大值
* Min Aggregation：求最小值
* Percentiles Aggregation：求百分比
* Stats Aggregation：同时返回avg、max、min、sum、count等
* Sum Aggregation：求和
* Top hits Aggregation：求前几
* Value Count Aggregation：求总数
* ……

### 3.2 案例讲解

案例1:搜索address中包含mill的所有人的年龄分布以及平均年龄

```json
GET /bank/_search
{
  "query": {
    "match": {
      "address": "mill"
    }
  },
  "aggs": {
    "ageAgg": {
      "terms": {
        "field": "age",
        "size": 10
      }
    },
    "ageAvg":{
      "avg": {
        "field": "age"
      }
    }
  },"size": 0 
}
```

![image.png](https://fynotefile.oss-cn-zhangjiakou.aliyuncs.com/fynote/1462/1644651801000/2bd4f9524ca646849e0ecbf51488327c.png)

案例2：按照年龄聚合，并且请求这些年龄段的这些人的平均薪资

```json
GET /bank/_search
{
  "query": {"match_all": {}},
  "aggs": {
    "ageAgg": {
      "terms": {
        "field": "age",
        "size": 50
      },"aggs": {
        "balanceAvg": {
          "avg": {
            "field": "balance"
          }
        }
      }
    }
  },"size": 0
}
```

![image.png](https://fynotefile.oss-cn-zhangjiakou.aliyuncs.com/fynote/1462/1644651801000/7b572c7d55e04d0eb2a8bebd05f8e8a4.png)

案例3：查出所有年龄分布，并且这些年龄段中M的平均薪资和F的平均薪资以及这个年龄段的总体平均薪资。

```json
GET /bank/_search
{
  "query": {"match_all": {}}
  ,"aggs": {
    "ageAgg": {
      "terms": {
        "field": "age",
        "size": 50
      },
      "aggs": {
        "genderAgg": {
          "terms": {
            "field": "gender.keyword",
            "size": 10
          },"aggs": {
            "balanceAvg": {
              "avg": {
                "field": "balance"
              }
            }
          }
        }
        ,"ageBalanceAvg":{
          "avg": {
            "field": "balance"
          }
        }
      }
    }
  }
  ,"size": 0
}
```

![image.png](https://fynotefile.oss-cn-zhangjiakou.aliyuncs.com/fynote/1462/1644651801000/4f225939afa14ba48995c82ac26ddc93.png)

## 4.映射配置(_mapping)

查看索引库中所有的属性的_mapping

![image.png](https://fynotefile.oss-cn-zhangjiakou.aliyuncs.com/fynote/1462/1644651801000/a4dc54534e944bc485928965d92a5cb5.png)

### 4.1 ElasticSearch7-去掉type概念：

&emsp;&emsp;关系型数据库中两个数据表示是独立的，即使他们里面有相同名称的列也不影响使用，但ES中不是这样的。elasticsearch是基于Lucene开发的搜索引擎，而ES中不同type下名称相同的filed最终在Lucene中的处理方式是一样的。

&emsp;&emsp;两个不同type下的两个user_name，在ES同一个索引下其实被认为是同一个filed，你必须在两个不同的type中定义相同的filed映射。否则，不同type中的相同字段名称就会在处理中出现冲突的情况，导致Lucene处理效率下降。

&emsp;&emsp;去掉type就是为了提高ES处理数据的效率。

**Elasticsearch 7.x**

URL中的type参数为可选。比如，索引一个文档不再要求提供文档类型。

**Elasticsearch 8.x**

不再支持URL中的type参数。

解决：将索引从多类型迁移到单类型，每种类型文档一个独立索引

### 4.2 什么是映射？

&emsp;&emsp;映射是定义文档的过程，文档包含哪些字段，这些字段是否保存，是否索引，是否分词等

### 4.3 创建映射字段

```json
PUT /索引库名/_mapping/类型名称
{
  "properties": {
    "字段名": {
      "type": "类型",
      "index": true，
      "store": true，
      "analyzer": "分词器"
    }
  }
}
```

类型名称：就是前面将的type的概念，类似于数据库中的不同表

字段名：类似于列名，properties下可以指定许多字段。

每个字段可以有很多属性。例如：

* type：类型，可以是text、long、short、date、integer、object等
* index：是否索引，默认为true
* store：是否存储，默认为false
* analyzer：分词器，这里使用ik分词器：`ik_max_word`或者`ik_smart`

![image.png](https://fynotefile.oss-cn-zhangjiakou.aliyuncs.com/fynote/1462/1644651801000/a97d3520eac9461ba927699c896bf9b1.png)

### 4.4 新增映射字段

&emsp;&emsp;如果我们创建完成索引的映射关系后，又要添加新的字段的映射，这时怎么办？第一个就是先删除索引，然后调整后再新建索引映射，还有一个方式就在已有的基础上新增。

```json
PUT /my_index/_mapping
{
  "properties":{
    "employee-id":{
      "type":"keyword"
      ,"index":false
    }
  }
}
```

![image.png](https://fynotefile.oss-cn-zhangjiakou.aliyuncs.com/fynote/1462/1644651801000/69042d9316fd428fa51aecd59da8c907.png)

### 4.5 更新映射

&emsp;&emsp;对于存在的映射字段，我们不能更新，更新必须创建新的索引进行数据迁移

### 4.6 数据迁移

先创建出正确的索引，然后使用如下的方式来进行数据的迁移

| POST_reindex [固定写法]<br />{<br />   "source":{<br />   "index":"twitter"<br />   },<br />   "dest":{<br />   "index":"new_twitter"<br />   }<br />} |
| :----------------------------------------------------------------------------------------------------------------------------------------------------- |

老的数据有type的情况

| POST_reindex [固定写法]<br />{<br />   "source":{<br />   "index":"twitter"，<br />   "type":"account"<br />   },<br />   "dest":{<br />   "index":"new_twitter"<br />   }<br />} |
| :-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |

案例：新创建了索引，并指定了映射属性![image.png](https://fynotefile.oss-cn-zhangjiakou.aliyuncs.com/fynote/1462/1644651801000/cfdd911c558c4bb4be4a942738852fd5.png)

![image.png](https://fynotefile.oss-cn-zhangjiakou.aliyuncs.com/fynote/1462/1644651801000/3e74ba5c2aa944628efad3214ca8a5fd.png)

## 5.分词

&emsp;&emsp;所谓的分词就是通过tokenizer(分词器)将一个字符串拆分为多个独立的tokens(词元-独立的单词)，然后输出为tokens流的过程。

例如"my name is HanMeiMei"这样一个字符串就会被默认的分词器拆分为[my,name,is HanMeiMei].ElasticSearch中提供了很多默认的分词器，我们可以来演示看看效果

![image.png](https://fynotefile.oss-cn-zhangjiakou.aliyuncs.com/fynote/1462/1644651801000/a99066ed062d483a9d7745ae45dd85c3.png)

![image.png](https://fynotefile.oss-cn-zhangjiakou.aliyuncs.com/fynote/1462/1644651801000/43d4f8b805c644a3a46084186eb43669.png)

但是在ElasticSearch中提供的分词器对中文的分词效果都不好。

![image.png](https://fynotefile.oss-cn-zhangjiakou.aliyuncs.com/fynote/1462/1644651801000/63597b55887c400c87e9eb80e4da9a38.png)

所以这时我们就需要安装特定的分词器 IK

### 1) 安装ik分词器

https://github.com/medcl/elasticsearch-analysis-ik 下载对应的版本，然后解压缩到plugins目录中

![image.png](https://fynotefile.oss-cn-zhangjiakou.aliyuncs.com/fynote/1462/1644651801000/fe105eb5f9e34d7495c018ef97181f2e.png)

然后检查是否安装成功：进入容器 通过如下命令来检测

![image.png](https://fynotefile.oss-cn-zhangjiakou.aliyuncs.com/fynote/1462/1644651801000/d4b2dce2eb1b4d8ca66320767b9e0715.png)

检查下载的文件是否完整，如果不完整就重新下载。

![image.png](https://fynotefile.oss-cn-zhangjiakou.aliyuncs.com/fynote/1462/1644651801000/c0f3bb238f5b479ea2207463652731ff.png)

插件安装OK后我们重新启动ElasticSearch服务

### 2) ik分词演示

ik_smart分词

```json
# 通过ik分词器来分词
POST /_analyze
{
  "analyzer": "ik_smart"
  ,"text": "我是中国人，我热爱我的祖国"
}

```

![image.png](https://fynotefile.oss-cn-zhangjiakou.aliyuncs.com/fynote/1462/1644651801000/a1495d4ee40c4b28a76b84dde29f01b1.png)

ik_max_word

```json
POST /_analyze
{
  "analyzer": "ik_max_word"
  ,"text": "我是中国人，我热爱我的祖国"
}
```

![image.png](https://fynotefile.oss-cn-zhangjiakou.aliyuncs.com/fynote/1462/1644651801000/5098631414324215bb8096deb1521579.png)

通过ik分词器的使用我们发现：如果使用ElasticSearch中默认提供的分词器是不支持中文分词的，也就是我们在定义一个索引的使用不能使用默认的mapping，而是要手动的来建立对应的mapping，在mapping我们需要选择对应的分词器。

### 3) 自定义词库

#### 虚拟机扩容

安装的软件越来越多，虚拟机的空间有限，这时我们可以关闭虚拟机后扩容

![image.png](https://fynotefile.oss-cn-zhangjiakou.aliyuncs.com/fynote/1462/1644651801000/20bc29b9cae1452994ff9d2f9899ad87.png)

ElasticSearch中原来分配的空间比较小，虚拟机空间增大后我们可以调整ElasticSearch的空间。

调整ElasticSearch的虚拟机内存，我们没办法直接修改，需要先删除原来的容器，然后创建新的容器。

![image.png](https://fynotefile.oss-cn-zhangjiakou.aliyuncs.com/fynote/1462/1644651801000/431873f0174045188535d3018189e99e.png)

调整JVM参数后重新启动容器：

```xshell
docker run --name elasticsearch -p 9200:9200 -p 9300:9300 \-e "discovery.type=single-node" -e ES_JAVA_OPTS="-Xms64m -Xmx512m" -v /mydata/elasticsearch/config/elasticsearch.yml:/usr/share/elasticsearch/config/elasticsearch.yml -v /mydata/elasticsearch/data:/usr/share/elasticsearch/data -v /mydata/elasticsearch/plugins:/usr/share/elasticsearch/plugins -d elasticsearch:7.4.2
```

![image.png](https://fynotefile.oss-cn-zhangjiakou.aliyuncs.com/fynote/1462/1644651801000/6f46b505efcc419cace683ac67301ccf.png)

#### Nginx安装

先安装一个简单的Nginx实例，来获取对应的配置信息

拉取Nginx的镜像

![image.png](https://fynotefile.oss-cn-zhangjiakou.aliyuncs.com/fynote/1462/1644651801000/9ed2095adc3c4c6bba8d9d048c038070.png)

启动Nginx服务

> docker run -d -p 80:80 --name nginx nginx:1.10

![image.png](https://fynotefile.oss-cn-zhangjiakou.aliyuncs.com/fynote/1462/1644651801000/1ff357fd9d0044c685479ba0e109acb1.png)

把容器中的配置文件拷贝到/mydata/nginx目录中

> docker container cp nginx:/etc/nginx .

![image.png](https://fynotefile.oss-cn-zhangjiakou.aliyuncs.com/fynote/1462/1644651801000/3f888ff367f04cb2a2677f74a4c16edb.png)

有了这个对应的配置文件夹后我们就可以删除掉之前的Nginx服务了

![image.png](https://fynotefile.oss-cn-zhangjiakou.aliyuncs.com/fynote/1462/1644651801000/2dd6671fabf9454ba3862b4a8b6f9462.png)

然后创建新的Nginx服务

```docker
docker run -d -p 80:80 --name nginx \
-v /mydata/nginx/html:/usr/share/nginx/html \
-v /mydata/nginx/logs:/var/log/nginx \
-v /mydata/nginx/conf:/etc/nginx \
nginx:1.10
```

![image.png](https://fynotefile.oss-cn-zhangjiakou.aliyuncs.com/fynote/1462/1644651801000/9013a32095b2446eb1d5cdea13346bf4.png)

测试访问：

![image.png](https://fynotefile.oss-cn-zhangjiakou.aliyuncs.com/fynote/1462/1644651801000/c4ee83648d93439aa08f2dd8fc533dec.png)

#### 实现自定义词库

我们需要在Nginx中创建对应的词库文件

![image.png](https://fynotefile.oss-cn-zhangjiakou.aliyuncs.com/fynote/1462/1644651801000/1b326e1573f049ada260dbb157e5b21c.png)

然后我们在ik分词器的插件的配置文件中修改远程词库的地址

/mydata/elasticsearch/plugins/ik/config

![image.png](https://fynotefile.oss-cn-zhangjiakou.aliyuncs.com/fynote/1462/1644651801000/ec8f201567ff4b27bbe761ad47041064.png)

![image.png](https://fynotefile.oss-cn-zhangjiakou.aliyuncs.com/fynote/1462/1644651801000/c64f13244a4b4f1f816a21cd33a4e70a.png)

然后保存文件重启ElasticSearch服务即可

![image.png](https://fynotefile.oss-cn-zhangjiakou.aliyuncs.com/fynote/1462/1644651801000/faf7e49333cd467e8fdef2f540b37911.png)

然后在Kibana中检索测试即可

![image.png](https://fynotefile.oss-cn-zhangjiakou.aliyuncs.com/fynote/1462/1644651801000/e682d71b59094a9aa209febd56815a29.png)

# 五、ElasticSearch应用

## 1.ES 的Java API两种方式

&emsp;&emsp;Elasticsearch 的API 分为 REST Client API（http请求形式）以及 transportClient
API两种。相比来说transportClient API效率更高，transportClient
是通过Elasticsearch内部RPC的形式进行请求的，连接可以是一个长连接，相当于是把客户端的请求当成

&emsp;&emsp;Elasticsearch 集群的一个节点，当然 REST Client API 也支持http
keepAlive形式的长连接，只是非内部RPC形式。但是从Elasticsearch 7 后就会移除transportClient
。主要原因是transportClient 难以向下兼容版本。

### 1.1 9300[TCP]

&emsp;&emsp;利用9300端口的是spring-data-elasticsearch:transport-api.jar,但是这种方式因为对应的SpringBoot版本不一致，造成对应的transport-api.jar也不同，不能适配es的版本，而且ElasticSearch7.x中已经不推荐使用了，ElasticSearch 8之后更是废弃了，所以我们不做过多的介绍

### 1.2 9200[HTTP]

&emsp;&emsp;基于9200端口的方式也有多种

* JsetClient:非官方，更新缓慢
* RestTemplate:模拟发送Http请求，ES很多的操作需要我们自己来封装，效率低
* HttpClient：和上面的情况一样
* ElasticSearch-Rest-Client:官方的RestClient，封装了ES的操作，API层次分明，易于上手。
* JavaAPIClient 7.15版本后推荐

## 2.ElasticSearch-Rest-Client整合

### 2.1 创建检索的服务

&emsp;&emsp;我们在商城服务中创建一个检索的SpringBoot服务

![image.png](https://fynotefile.oss-cn-zhangjiakou.aliyuncs.com/fynote/1462/1644651801000/4e68f197b4e84096a84da1fc222fca1e.png)

添加对应的依赖：官方地址：https://www.elastic.co/guide/en/elasticsearch/client/java-rest/current/java-rest-high-getting-started-maven.html#java-rest-high-getting-started-maven-maven

![image.png](https://fynotefile.oss-cn-zhangjiakou.aliyuncs.com/fynote/1462/1644651801000/2a5f9b13da9e4d9a8d53f4ff009efdb1.png)

公共依赖不要忘了，同时我们在公共依赖中依赖了MyBatisPlus所以我们需要在search服务中排除数据源，不然启动报错

![image.png](https://fynotefile.oss-cn-zhangjiakou.aliyuncs.com/fynote/1462/1644651801000/64127649661e4d0585382ff4d3ad8d5a.png)

![image.png](https://fynotefile.oss-cn-zhangjiakou.aliyuncs.com/fynote/1462/1644651801000/c9b69ef6ab4640b59a1b124311d69fcb.png)

然后我们需要把这个服务注册到Nacos注册中心中，这块操作了很多遍，不重复

添加对应的ElasticSearch的配置类

```java
/**
 * ElasticSearch的配置类
 */
@Configuration
public class MallElasticSearchConfiguration {

    @Bean
    public RestHighLevelClient restHighLevelClient(){
        RestClientBuilder builder = RestClient.builder(new HttpHost("192.168.56.100", 9200, "http"));
        RestHighLevelClient client = new RestHighLevelClient(builder);
        return client;
    }
}
```

![image.png](https://fynotefile.oss-cn-zhangjiakou.aliyuncs.com/fynote/1462/1644651801000/66d01685d40d4c588d5b79e33754e465.png)

测试：

![image.png](https://fynotefile.oss-cn-zhangjiakou.aliyuncs.com/fynote/1462/1644651801000/5af72d45e3b04deda4f77c98d3696ff8.png)

### 2.2 测试保存文档

#### 设置RequestOptions

![image.png](https://fynotefile.oss-cn-zhangjiakou.aliyuncs.com/fynote/1462/1644651801000/056add3cad7847a783451cc48d8f4f3a.png)

我们就在ElasticSearch的配置文件中设置

![image.png](https://fynotefile.oss-cn-zhangjiakou.aliyuncs.com/fynote/1462/1644651801000/a70828ae270b418686f7e410e6d7c731.png)

#### 保存数据

然后就可以结合官方文档来实现文档数据的存储

```java
package com.msb.mall.mallsearch;

import com.fasterxml.jackson.core.JsonProcessingException;
import com.fasterxml.jackson.databind.ObjectMapper;
import com.fasterxml.jackson.databind.util.JSONPObject;
import com.msb.mall.mallsearch.config.MallElasticSearchConfiguration;
import lombok.Data;
import org.elasticsearch.action.index.IndexRequest;
import org.elasticsearch.action.index.IndexResponse;
import org.elasticsearch.client.RestHighLevelClient;
import org.elasticsearch.common.xcontent.XContentType;
import org.json.JSONObject;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;

@SpringBootTest
class MallSearchApplicationTests {

    @Autowired
    private RestHighLevelClient client;

    @Test
    void contextLoads() {
        System.out.println("--->"+client);
    }

    /**
     * 测试保存文档
     */
    @Test
    void saveIndex() throws Exception {
        IndexRequest indexRequest = new IndexRequest("system");
        indexRequest.id("1");
        // indexRequest.source("name","bobokaoya","age",18,"gender","男");
        User user = new User();
        user.setName("bobo");
        user.setAge(22);
        user.setGender("男");
        // 用Jackson中的对象转json数据
        ObjectMapper objectMapper = new ObjectMapper();
        String json = objectMapper.writeValueAsString(user);
        indexRequest.source(json, XContentType.JSON);
        // 执行操作
        IndexResponse index = client.index(indexRequest, MallElasticSearchConfiguration.COMMON_OPTIONS);
        // 提取有用的返回信息
        System.out.println(index);
    }
    @Data
    class User{
        private String name;
        private Integer age;
        private String gender;
    }

}

```

之后成功

![image.png](https://fynotefile.oss-cn-zhangjiakou.aliyuncs.com/fynote/1462/1644651801000/6d369a2d69f342c6bf6bd22469f3b39d.png)

![image.png](https://fynotefile.oss-cn-zhangjiakou.aliyuncs.com/fynote/1462/1644651801000/6c144fafdfc04750885f2fe824518918.png)

### 2.3 检索操作

参考官方文档可以获取到处理各种检索情况的API

案例1：检索出所有的bank索引的所有文档

```java
    @Test
    void searchIndexAll() throws IOException {
        // 1.创建一个 SearchRequest 对象
        SearchRequest searchRequest = new SearchRequest();
        searchRequest.indices("bank"); // 设置我们要检索的数据对应的索引库
        SearchSourceBuilder sourceBuilder = new SearchSourceBuilder();
        /*sourceBuilder.query();
        sourceBuilder.from();
        sourceBuilder.size();
        sourceBuilder.aggregation();*/
        searchRequest.source(sourceBuilder);

        // 2.如何执行检索操作
        SearchResponse response = client.search(searchRequest, MallElasticSearchConfiguration.COMMON_OPTIONS);
        // 3.获取检索后的响应对象，我们需要解析出我们关心的数据
        System.out.println("ElasticSearch检索的信息："+response);
    }
```

![image.png](https://fynotefile.oss-cn-zhangjiakou.aliyuncs.com/fynote/1462/1644651801000/88ae6860c1324fecbd1505a2a9b8ca6e.png)

案例2：根据address全文检索

```java
    @Test
    void searchIndexByAddress() throws IOException {
        // 1.创建一个 SearchRequest 对象
        SearchRequest searchRequest = new SearchRequest();
        searchRequest.indices("bank"); // 设置我们要检索的数据对应的索引库
        SearchSourceBuilder sourceBuilder = new SearchSourceBuilder();
        // 查询出bank下 address 中包含 mill的记录
        sourceBuilder.query(QueryBuilders.matchQuery("address","mill"));
        searchRequest.source(sourceBuilder);
        // System.out.println(searchRequest);

        // 2.如何执行检索操作
        SearchResponse response = client.search(searchRequest, MallElasticSearchConfiguration.COMMON_OPTIONS);
        // 3.获取检索后的响应对象，我们需要解析出我们关心的数据
        System.out.println("ElasticSearch检索的信息："+response);
    }
```

案例3：嵌套的聚合操作：检索出bank下的年龄分布和每个年龄段的平均薪资

```java
/**
     * 聚合：嵌套聚合
     * @throws IOException
     */
    @Test
    void searchIndexAggregation() throws IOException {
        // 1.创建一个 SearchRequest 对象
        SearchRequest searchRequest = new SearchRequest();
        searchRequest.indices("bank"); // 设置我们要检索的数据对应的索引库
        SearchSourceBuilder sourceBuilder = new SearchSourceBuilder();
        // 查询出bank下 所有的文档
        sourceBuilder.query(QueryBuilders.matchAllQuery());
        // 聚合 aggregation
        // 聚合bank下年龄的分布和每个年龄段的平均薪资
        AggregationBuilder aggregationBuiler = AggregationBuilders.terms("ageAgg")
                                                .field("age")
                                                .size(10);
        // 嵌套聚合
        aggregationBuiler.subAggregation(AggregationBuilders.avg("balanceAvg").field("balance"));

        sourceBuilder.aggregation(aggregationBuiler);
        sourceBuilder.size(0); // 聚合的时候就不用显示满足条件的文档内容了
        searchRequest.source(sourceBuilder);
        System.out.println(sourceBuilder);

        // 2.如何执行检索操作
        SearchResponse response = client.search(searchRequest, MallElasticSearchConfiguration.COMMON_OPTIONS);
        // 3.获取检索后的响应对象，我们需要解析出我们关心的数据
        System.out.println(response);
    }
```

案例4：并行的聚合操作：查询出bank下年龄段的分布和总的平均薪资

```java
/**
     * 聚合
     * @throws IOException
     */
    @Test
    void searchIndexAggregation1() throws IOException {
        // 1.创建一个 SearchRequest 对象
        SearchRequest searchRequest = new SearchRequest();
        searchRequest.indices("bank"); // 设置我们要检索的数据对应的索引库
        SearchSourceBuilder sourceBuilder = new SearchSourceBuilder();
        // 查询出bank下 所有的文档
        sourceBuilder.query(QueryBuilders.matchAllQuery());
        // 聚合 aggregation
        // 聚合bank下年龄的分布和平均薪资
        AggregationBuilder aggregationBuiler = AggregationBuilders.terms("ageAgg")
                .field("age")
                .size(10);

        sourceBuilder.aggregation(aggregationBuiler);
        // 聚合平均年龄
        AvgAggregationBuilder balanceAggBuilder = AggregationBuilders.avg("balanceAgg").field("age");
        sourceBuilder.aggregation(balanceAggBuilder);

        sourceBuilder.size(0); // 聚合的时候就不用显示满足条件的文档内容了
        searchRequest.source(sourceBuilder);
        System.out.println(sourceBuilder);

        // 2.如何执行检索操作
        SearchResponse response = client.search(searchRequest, MallElasticSearchConfiguration.COMMON_OPTIONS);
        // 3.获取检索后的响应对象，我们需要解析出我们关心的数据
        System.out.println(response);
    }
```

案例5：处理检索后的结果

```java
 @Test
    void searchIndexResponse() throws IOException {
        // 1.创建一个 SearchRequest 对象
        SearchRequest searchRequest = new SearchRequest();
        searchRequest.indices("bank"); // 设置我们要检索的数据对应的索引库
        SearchSourceBuilder sourceBuilder = new SearchSourceBuilder();
        // 查询出bank下 address 中包含 mill的记录
        sourceBuilder.query(QueryBuilders.matchQuery("address","mill"));
        searchRequest.source(sourceBuilder);
        // System.out.println(searchRequest);

        // 2.如何执行检索操作
        SearchResponse response = client.search(searchRequest, MallElasticSearchConfiguration.COMMON_OPTIONS);
        // 3.获取检索后的响应对象，我们需要解析出我们关心的数据
       // System.out.println("ElasticSearch检索的信息："+response);
        RestStatus status = response.status();
        TimeValue took = response.getTook();
        SearchHits hits = response.getHits();
        TotalHits totalHits = hits.getTotalHits();
        TotalHits.Relation relation = totalHits.relation;
        long value = totalHits.value;
        float maxScore = hits.getMaxScore(); // 相关性的最高分
        SearchHit[] hits1 = hits.getHits();
        for (SearchHit documentFields : hits1) {
            /*"_index" : "bank",
                    "_type" : "account",
                    "_id" : "970",
                    "_score" : 5.4032025*/
            //documentFields.getIndex(),documentFields.getType(),documentFields.getId(),documentFields.getScore();
            String json = documentFields.getSourceAsString();
            //System.out.println(json);
            // JSON字符串转换为 Object对象
            ObjectMapper mapper = new ObjectMapper();
            Account account = mapper.readValue(json, Account.class);
            System.out.println("account = " + account);
        }
        //System.out.println(relation.toString()+"--->" + value + "--->" + status);
    }

    @ToString
    @Data
    static class Account {

        private int account_number;
        private int balance;
        private String firstname;
        private String lastname;
        private int age;
        private String gender;
        private String address;
        private String employer;
        private String email;
        private String city;
        private String state;

    }
```

数据的结果：

![image.png](https://fynotefile.oss-cn-zhangjiakou.aliyuncs.com/fynote/1462/1644651801000/52c0097617db4d03bf9ccab010865e9b.png)
