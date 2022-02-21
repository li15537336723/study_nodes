# 五大数据类型

## Redis-Key：基本命令

```bash
127.0.0.1:6379> keys *		# 查看所有的 key
(empty list or set)
127.0.0.1:6379> set name xiaohong	# set key
OK
127.0.0.1:6379> keys *
1) "name"
127.0.0.1:6379> set age 1
OK
127.0.0.1:6379> keys *
1) "name"
2) "age"
127.0.0.1:6379> exists name		# 判断当前的 key 是否存在
(integer) 1
127.0.0.1:6379> exists name1
(integer) 0
127.0.0.1:6379> move name 1		# 移除当前的 key
(integer) 1
127.0.0.1:6379> keys *
1) "age"
127.0.0.1:6379> set name qinjiang
OK
127.0.0.1:6379> keys *
1) "name"
2) "age"
127.0.0.1:6379> get name
"qinjiang"
127.0.0.1:6379> expire name 10		# 设置 key 的过期时间  单位是 秒
(integer) 1
127.0.0.1:6379> ttl name		# 查看当前 key 的剩余时间
(integer) 4
127.0.0.1:6379> ttl name
(integer) 1
127.0.0.1:6379> ttl name
(integer) 0
127.0.0.1:6379> ttl name
(integer) -2
127.0.0.1:6379> ttl name
(integer) -2
127.0.0.1:6379> get name
(nil)
127.0.0.1:6379>  type name		# 查看当前 key 的配置类型
string
127.0.0.1:6379> type age
string
```



## String（字符串）

```bash
127.0.0.1:6379> set key1 v1		# 设置值
OK
127.0.0.1:6379> get key1		# 获取值
"v1"
127.0.0.1:6379> exists key1		# 判断某一个key是否存在
(integer) 1
127.0.0.1:6379> append key1 "hello"	# 追加字符串，如果当前key不存在，就相当于 setkey
(integer) 7
127.0.0.1:6379> get key1
"v1hello"	
127.0.0.1:6379> strlen key1		# 获取字符串的长度
(integer) 7
127.0.0.1:6379> append key1 ",kuangshen"
(integer) 17
127.0.0.1:6379> get key1
"v1hello,kuangshen"
#######################################################
# i++
# 步长 i+=
127.0.0.1:6379> set views 0	# 初始浏览量为 0
OK
127.0.0.1:6379> get views	
"0"
127.0.0.1:6379> incr views	# 自增1 浏览量变为 1
(integer) 1
127.0.0.1:6379> incr views
(integer) 2
127.0.0.1:6379> get views
"2"
127.0.0.1:6379> decr views	# 自减 1 浏览量 -1
(integer) 1
127.0.0.1:6379> decr views
(integer) 0
127.0.0.1:6379> get views
"0"
127.0.0.1:6379> incrby views 10 # 可以设置步长，指定增量！
(integer) 10
127.0.0.1:6379> decrby views 5
(integer) 5
127.0.0.1:6379> 
#######################################################
# 字符串范围 range
127.0.0.1:6379> set key1 "hello,xiaohong"	# 设置 key1 的值
OK
127.0.0.1:6379> get key1
"hello,xiaohong"
127.0.0.1:6379> getrange key1 0 3		# 截取字符串[0,3]
"hell"
127.0.0.1:6379> getrange key1 0 -1		# 获取全部的字符串 和 get key 是一样的
"hello,xiaohong"

# 替换！
127.0.0.1:6379> set key2 abcdefg
OK
127.0.0.1:6379> get key2
"abcdefg"
127.0.0.1:6379> setrange key2 1 xx	# 替换指定位置开始的字符串！
(integer) 7
127.0.0.1:6379> get key2
"axxdefg"
##############################################################
# setex	(set with expire)	设置过期时间
# setnx	(set if not exist)	不存在设置（在分布式锁中会常常使用！）
127.0.0.1:6379> setex key3 30 "hello"  # 设置 key3 的值为hello，30秒后过期
OK
127.0.0.1:6379> ttl key3
(integer) 25
127.0.0.1:6379>
127.0.0.1:6379> setnx mykey "redis"	# 如果 mykey 不纯在，创建 mykey
(integer) 1
127.0.0.1:6379> keys *
1) "mykey"
2) "key3"
127.0.0.1:6379> ttl key3
(integer) 8
127.0.0.1:6379> setnx mykey "MongoDB"	# 如果 mykey 存在，创建失败
(integer) 0
127.0.0.1:6379> get mykey
"redis"
################################################################
# mset 批量设置
127.0.0.1:6379> mset k1 v1 k2 v2 k3 v3	# 同时设置多个值
OK
127.0.0.1:6379> keys *
1) "k1"
2) "k3"
3) "k2"
127.0.0.1:6379> mget k1 k2 k3	# 同时获取多个值
1) "v1"
2) "v2"
3) "v3"
127.0.0.1:6379> msetnx k1 v1 k4 v4 	# msetnx 是一个原子性操作，要么一期成功，要么一期失败
(integer) 0
127.0.0.1:6379> get k4
(nil)


# 对象
set user:1{name:zhangsan, age:3} #设置一个user1:对象 值为接送字符换来保存一个对象！

# 这里的 key 是一个巧妙的设计：user:{id}，如此设计 在Redis 中是完全 OK 了！
127.0.0.1:6379> mset user:1:name zhangsan user:1:age 2
OK
127.0.0.1:6379> mget user:1:name user:1:age
1) "zhangsan"
2) "2"

######################################################################
getset # 先 get 然后再 set

127.0.0.1:6379> getset db redis	# 如果不存在值，则返回 nil
(nil)
127.0.0.1:6379> get db
"redis"
127.0.0.1:6379> getset db mongdb	# 如果存在值，获取原来的值，并设置新的值
"redis"
127.0.0.1:6379> get db
"mongdb"
```

数据结构是相同！

String 类似的使用场景：value除了是我们的字符串还可以是我们的数字！

- 计数器
- 统计多单位的数量 uid:9164546:follow 0
- 粉丝数
- 对象缓存存储！



## List（列表）

在 redis 里面，我们可以把 list 玩成栈，队列，阻塞队列！

所有的 list 命令都是用 l 开头的

```bash
127.0.0.1:6379> lpush list one		# 将一个值或者多个值插入到列表的头部（相当于左边放入值）
(integer) 1
127.0.0.1:6379> lpush list two		
(integer) 2
127.0.0.1:6379> lpush list three
(integer) 3
127.0.0.1:6379> lrange list 0 -1	# 获取 list 中的值
1) "three"
2) "two"
3) "one"
127.0.0.1:6379> lrange list 0 1		# 通过区间获取具体的值
1) "three"
2) "two"

127.0.0.1:6379> rpush list right	# 将一个值或者多个值，插入到列表尾部（右边放入值）
(integer) 4
127.0.0.1:6379> lrange list 0 1
1) "three"
2) "two"
127.0.0.1:6379> lrange list 0 -1
1) "three"
2) "two"
3) "one"
4) "right"
#######################################################################
LPOP
RPOP
127.0.0.1:6379> lrange list 0 -1
1) "three"
2) "two"
3) "one"
4) "right"
127.0.0.1:6379> lpop list		# 移除list的第一个元素
"three"
127.0.0.1:6379> rpop list		# 移除list的最后一个元素
"right"
127.0.0.1:6379> lrange list 0 -1
1) "two"
2) "one"
######################################################################
lindex

127.0.0.1:6379> lrange list 0 -1
1) "two"
2) "one"
127.0.0.1:6379> lindex list 1
"one"
127.0.0.1:6379> lindex list 0
"two"

########################################################################
Llen 

127.0.0.1:6379> lpush list one
(integer) 3
127.0.0.1:6379> lpush list two
(integer) 4
127.0.0.1:6379> lpush list three
(integer) 5
127.0.0.1:6379> llen list		# 返回 列表的长度
(integer) 5

#######################################################################
移除指定的值
取关 uid

Lrem

127.0.0.1:6379> lrem list 1 one		# 移除 list 集合中指定的 value，精确匹配
(integer) 1
127.0.0.1:6379> lrange list 0 -1
1) "three"
2) "two"
3) "two"
4) "one"
127.0.0.1:6379> lrem list 2 two
(integer) 2
127.0.0.1:6379> lrange list 0 -1
1) "three"
2) "one"

#######################################################################
trim 修剪		list 截断

127.0.0.1:6379> rpush mylist "hello:
Invalid argument(s)
127.0.0.1:6379> rpush mylist "hello"
(integer) 1
127.0.0.1:6379> rpush mylist "hello1"
(integer) 2
127.0.0.1:6379> rpush mylist "hello2"
(integer) 3
127.0.0.1:6379> rpush mylist "hello3"
(integer) 4
127.0.0.1:6379> ltrim mylist 1 2		# 通过下标截取指定的长度
OK
127.0.0.1:6379> lrange mylist 0 -1
1) "hello1"
2) "hello2"

#####################################################################
rpoplpush   # 移除列表的最后一个元素，将他移动到新的列表中！

127.0.0.1:6379> rpush mylist "hello"
(integer) 1
127.0.0.1:6379> rpush mylist "hello1"
(integer) 2
127.0.0.1:6379> rpush mylist "hello2"
(integer) 3
127.0.0.1:6379> rpoplpush mylist myotherlist  # 移动列表的最后一额元素，将他移动至新列表中
"hello2"
127.0.0.1:6379> lrange mylist 0 -1		# 查看原来的列表
1) "hello"
2) "hello1"
127.0.0.1:6379> lrange myotherlist 0 -1		# 查看目标列表，确实存在该值
1) "hello2"

#####################################################################
lset	将列表中指定下标的值替换为另外一个值，更新操作
127.0.0.1:6379> lpush list value1
(integer) 1
127.0.0.1:6379> lrange list 0 0
1) "value1"
127.0.0.1:6379> lset list 0 item	# 更新当前下标的值，如果下标不存在则报错
OK
127.0.0.1:6379> lrange list 0 0
1) "item"

#####################################################################
linsert # 将某个具体的value插入到列表中某个元素的前面

127.0.0.1:6379> rpush mylist "hello"
(integer) 1
127.0.0.1:6379> rpush mylist "world"
(integer) 2
127.0.0.1:6379> linsert mylist before "world" "other"
(integer) 3
127.0.0.1:6379> lrange mylist 0 -1
1) "hello"
2) "other"
3) "world"
127.0.0.1:6379> linsert mylist after world new
(integer) 4
127.0.0.1:6379> lrange mylist 0 -1
1) "hello"
2) "other"
3) "world"
4) "new"
```

> 小结

- 它实际上是一个链表，before Node after，left，right都可以插入值
- 如果 key 不存在，创建新的链表
- 如果 key 存在，新增内容
- 如果移除了所有值，空链表，也代表不存在！
- 在两边插入或者改动值，效率最高！中间元素，相对来说效率会低一点

消息排队！消息队列（ Lpush Rpop） 栈（Lpush Lpop）



## Set（集合）

set 中的值是不能重复读的 ！

```bash
127.0.0.1:6379> sadd myset "hello"		# set集合中添加元素
(integer) 1
127.0.0.1:6379> sadd myset "xiaohong"
(integer) 1
127.0.0.1:6379> sadd myset "helloxiaohong"
(integer) 1
127.0.0.1:6379> smembers myset		# 查看指定set的所有值
1) "xiaohong"
2) "helloxiaohong"
3) "hello"
127.0.0.1:6379> sismember myset hello	# 判断某一个值是不是在 set 集合中
(integer) 1

#####################################################################
127.0.0.1:6379> scard myset	# 获取set集合中的内容元素个数
(integer) 4

#####################################################################
srem

127.0.0.1:6379> srem myset hello	# 移除 set 集合中的指定元素
(integer) 1
127.0.0.1:6379> scard myset
(integer) 3
127.0.0.1:6379> smembers myset
1) "xiaohong"
2) "helloxiaohong"
3) "lovexiaohong"


#####################################################################
set 无序不重复集合，抽随机！

127.0.0.1:6379> srandmember  myset		# 随机抽选出一个元素
"helloxiaohong"
127.0.0.1:6379> srandmember  myset
"xiaohong"
127.0.0.1:6379> srandmember  myset
"xiaohong"
127.0.0.1:6379> srandmember  myset 2		# 随机抽选出指定个数的元素 2个
"lovexiaohong"
"xiaohong"
127.0.0.1:6379> srandmember  myset		# 随机抽选出一个元素
"helloxiaohong"

#####################################################################
删除指定的 key ，随机删除 key ！

127.0.0.1:6379> spop myset		# 随机删除一些 set 集合中的元素！
"xiaohong"
127.0.0.1:6379> spop myset
"lovexiaohong"
127.0.0.1:6379> smembers myset
1) "helloxiaohong"

#####################################################################
将一个指定的值，移动到另外一个集合中

127.0.0.1:6379> sadd myset "hello"
(integer) 1
127.0.0.1:6379> sadd myset "world"
(integer) 1
127.0.0.1:6379> sadd myset "xiaohong"
(integer) 1
127.0.0.1:6379> sadd myset2 "set2"
(integer) 1
127.0.0.1:6379> smove myset myset2 "xiaohong"	# 将一个指定的值，移动到另外一个set集合
(integer) 1
127.0.0.1:6379> smembers myset
1) "world"
2) "helloxiaohong"
3) "hello"
127.0.0.1:6379> smembers myset2
1) "xiaohong"
2) "set2"

#####################################################################
微博，B站，共同关注（并集）
数字集合：
- 差集	sdiff
- 并集	sinter
- 交集	sunion

127.0.0.1:6379> sdiff key1 key2	 	# 差集
1) "b"
2) "a"
127.0.0.1:6379> sdiff key2 key1
1) "d"
2) "e"
127.0.0.1:6379> sinter key1 key2	#交集
1) "c"
127.0.0.1:6379> sunion key1 key2	# 并集
1) "b"
2) "a"
3) "c"
4) "d"
5) "e"
```

微博，A 用户将所有关注的人放在一个 set 集合中！将它的粉丝也放在一个集合中！

共同关注，共同爱好，二度好友，推荐好友（六度分割理论）

## Hash（哈希）

Map 集合，key-map！这个时候这个值是一个map集合！本质和String类型没有太大的区别，还是一个简单的key-value！

set myhash field xiaohong

```bash
127.0.0.1:6379> hset myhash field1 xiaohong		# 设置一个具体的 key -value
(integer) 1
127.0.0.1:6379> hget myhash field1		# 获取一个字段值
"xiaohong"
127.0.0.1:6379> hmset myhash field1 hello field2 world	# set 多个 key-value
OK
127.0.0.1:6379> hmget myhash field1 field2		# 获取多个字段值
1) "hello"
2) "world"
127.0.0.1:6379> hgetall myhash		# 获取全部的数据
1) "field1"
2) "hello"
3) "field2"
4) "world"

127.0.0.1:6379> hdel myhash field1	# 删除 hash 指定key字段！对应的value值也就消失了！
(integer) 1
127.0.0.1:6379> hgetall myhash
1) "field2"
2) "world"
#######################################################################
hlen

127.0.0.1:6379> hmset myhash field1 hello field2 world
OK
127.0.0.1:6379> hgetall myhash
1) "field2"
2) "world"
3) "field1"
4) "hello"
127.0.0.1:6379> hlen myhash		# 获取 hash 表的字段数量
(integer) 2

########################################################################
# 只获取所有的 field
# 只获取所有的 value
127.0.0.1:6379> hkeys myhash	# 只获得所有的 field
1) "field2"
2) "field1"
127.0.0.1:6379> hvals myhash	# 只获得所有的 value
1) "world"
2) "hello"

#####################################################################
incr decr 

127.0.0.1:6379> hset myhash field3 5	# 指定增量
(integer) 1
127.0.0.1:6379> hincrby myhash field3 1
(integer) 6
127.0.0.1:6379> hincrby myhash field3 -1
(integer) 5
127.0.0.1:6379> hsetnx myhash field4 hello	# 如果不存在则可以设置
(integer) 1
127.0.0.1:6379> hsetnx myhash field4 world	# 如果存在则不能设置
(integer) 0
```

hash 变更的数据 user	name	age，尤其是用户信息之类的，经常变动的信息！**hash 更适合于对象的存储**，**String更适合字符串的存储**



## Zset（有序集合）

在 set 的基础上，增加了一个值，`set k1 v1, Zset k1 score1 v1`

```bash
127.0.0.1:6379> zadd myset 1 one	# 添加一个值
(integer) 1
127.0.0.1:6379> zadd myset 2 two 3 three	# 添加多个值
(integer) 2
127.0.0.1:6379> zrange myset 0 -1
1) "one"
2) "two"
3) "three"

#######################################################################
排序如何实现

127.0.0.1:6379> zadd salary 2500 xiaohong	# 添加三个用户
(integer) 1
127.0.0.1:6379> zadd salary 5000 zhangsan
(integer) 1
127.0.0.1:6379> zadd salary 500 kuangshen
(integer) 1
# zrangebyscore key min max
127.0.0.1:6379> zrangebyscore salary -inf +inf	# 显示全部的用户  从小到大排序
1) "kuangshen"
2) "xiaohong"
3) "zhangsan"
127.0.0.1:6379> zrevrange salary 0 -1	# 从大到小排序
1) "zhangsan"
2) "kuangshen"
127.0.0.1:6379> zrangebyscore salary -inf +inf withscores	# 显示全部的用户并且附带成绩
1) "kuangshen"
2) "500"
3) "xiaohong"
4) "2500"
5) "zhangsan"
6) "5000"
127.0.0.1:6379> zrangebyscore salary -inf 2500 withscores # 显示工资<=2500的并升序排
1) "kuangshen"
2) "500"
3) "xiaohong"
4) "2500"

########################################################################
# 移除 rem 中的元素

127.0.0.1:6379> zrange salary 0 -1
1) "kuangshen"
2) "xiaohong"
3) "zhangsan"
127.0.0.1:6379> zrem salary xiaohong  # 移除有序集合中的指定元素
(integer) 1
127.0.0.1:6379> zrange salary 0 -1
1) "kuangshen"
2) "zhangsan"

127.0.0.1:6379> zcard salary	# 获取有序集合中的个数
(integer) 2

############################################################################

127.0.0.1:6379> zadd myset 1  hello
(integer) 1
127.0.0.1:6379> zadd myset 2 world 3 xiaohong
(integer) 2
127.0.0.1:6379> zcount myset 1 3	# 获取指定区间的成员数量！
(integer) 3
127.0.0.1:6379> zcount myset 1 2
(integer) 2
```

其余的一些 API，通过我们的学习，剩下的如果工作中有需要，查询官方文档

案例思路：set 排序 	存储班级成绩表，工资表排序！

普通消息，1. 重要消息 	2. 带权重进行判断！

排行榜应用实现，取 Top N 测试！