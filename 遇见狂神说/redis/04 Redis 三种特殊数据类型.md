# 三种特殊数据类型

## Geospatial 地理位置

朋友的定位，附近的人，打车距离计算？

Redis 的 Geo 在 Redis3.2 版本就推出了！这个功能可以推算地理位置的信息，两地之间的距离，方圆几里的人！

**六个命令**

![image-20210222164943404](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210222164943404.png)



> getadd

```bash
127.0.0.1:6379> geoadd china:city 116.40 39.90 beijing	# 添加地理位置
(integer) 1
127.0.0.1:6379> geoadd china:city 121.47 31.23 shanghai
(integer) 1
127.0.0.1:6379> geoadd china:city 106.50 29.53 chongqing 114.05 22.52 shenzhen
(integer) 2
127.0.0.1:6379> geoadd china:city 120.16 30.24 hangzhou 108.96 34.26 xian
(integer) 2
```



> getpos

获得当前定位：一定是一个坐标值！

```bash
127.0.0.1:6379> geopos china:city beijing
1) 1) "116.39999896287918091"
   2) "39.90000009167092543"
127.0.0.1:6379> geopos china:city beijing chongqing
1) 1) "116.39999896287918091"
   2) "39.90000009167092543"
2) 1) "106.49999767541885376"
   2) "29.52999957900659211"
```



> geodist

两人之间的距离

单位：

- **m** 表示为米
- **km** 表示为千米
- **mi** 表示为英里
- **ft** 表示为英尺

```bash
127.0.0.1:6379> geodist china:city beijing shanghai km	# 查看上海到北京的直线距离
"1067.3788"
127.0.0.1:6379> geodist china:city beijing chongqing km	# 查看重庆到北京的直线距离
"1464.0708"
```



> 

我附近的人？（获得所有附近的人的地址，定位！）通过半径来查询



## Hyperloglog

> 什么是基数？

A{1,3,5,4,8,5}

B{1,3,5,7,8}

基数（不重复的元素）= 5，可以接收误差！

> 简介

Redis 2.8.9 版本就更新了 Hyperloglog！

Redis Hyperloglog 基数统计的算法！

优点：占用的内存是固定，2^64 不同的元素的技术，只需要废12KB内存！如果要从内存角度比较的话 Hyperlog log 首选

**网页的 UV （一个人访问一个网站多次，但是还是算作一个人！）**

传统的方式，set 保存用户的 id，然后就可以统计 set 中的元素数量作为标准判断！

这个方式如果保存大量的用户 id ，就会比较麻烦！我们的目的是为了计数，而不是保存用户 id



> 测试使用

```bash
127.0.0.1:6379> PFadd mykey2 i l d d s s a e	# 创建第一组元素 mykey
(integer) 1
127.0.0.1:6379> pfcount mykey		# 统计  mykey 元素的基数数量
(integer) 10
127.0.0.1:6379> pfcount mykey2		# 创建第二组元素 mykey2
(integer) 6
127.0.0.1:6379> pfmerge mykey3 mykey mykey2	# 合并两组 mykey mykey2 =》 mykey3 并集
OK
127.0.0.1:6379> pfcount mykey3		# 看并集的数量！
(integer) 12
```

如果允许容错，那么一定可以使用 Hyperloglog！

如果不允许容错，就是用 set 或者自己的数据类型即可！



## bitmaps

> 位存储

统计用户信息，活跃，不活跃！登陆，未登录！打卡，365打卡



使用 bitmap 来记录 周一到周日的打卡

周一：1 周二：0 周三 3 周四：1 ............

```bash
127.0.0.1:6379> setbit sign 0 1
(integer) 0
127.0.0.1:6379> setbit sign 1 0
(integer) 0
127.0.0.1:6379> setbit sign 2 0
(integer) 0
127.0.0.1:6379> setbit sign 3 1
(integer) 0
127.0.0.1:6379> setbit sign 4 1
(integer) 0
127.0.0.1:6379> setbit sign 5 0
(integer) 0
127.0.0.1:6379> setbit sign 6 0
(integer) 0
```

查看某一天是否有打卡！

```bash
127.0.0.1:6379> getbit sign 3
(integer) 1
127.0.0.1:6379> getbit sign 6
(integer) 0
```

统计操作，统计打卡的天数！

```bash
127.0.0.1:6379> bitcount sign	# 统计这周的打卡天数就可以是否有全勤！
(integer) 3
```

