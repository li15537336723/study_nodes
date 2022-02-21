## Jedis

我们要使用 Java 来操作 Redis

> 什么是 Jedis 是 Redis 官方推荐的 Java 连接开发工具！使用Java操作Redis中间件，如果你要使用 Java 操作Redis，那么一定要对 jedis 十分熟悉！



> 测试

1、导入对应的依赖

```xml
<!--导入相关的jar包的包 -->
<dependencies>
    <!--jedis-->
    <dependency>
        <groupId>redis.clients</groupId>
        <artifactId>jedis</artifactId>
        <version>3.2.0</version>
    </dependency>

    <!--fastjson-->
    <dependency>
        <groupId>com.alibaba</groupId>
        <artifactId>fastjson</artifactId>
        <version>1.2.6</version>
    </dependency>
</dependencies>
```

2、编码测试

- 链接数据库
- 操作命令
- 断开连接

```java
public class TestPing {
    public static void main(String[] args) {
        // 1、 new  Jedis 对象即可
        Jedis jedis = new Jedis("127.0.0.1", 6379);
        // jedis 所有的命令就是我们之前学习的所有指令！
        System.out.println(jedis.ping());
    }
}
```

输出：

![image-20210223082521873](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210223082521873.png)



## 常用的API

String

List

Set

Hash

Zset

> 所有的 api 命令，就是之前数据类型所学习的指令





## SpringBoot 整合

SpringBoot 操作数据：spring-data jps jdbc mongodb redis!

SpringData 也是和 SpringBoot 齐名的项目！

说明：在 SpringBoot 2.X 之后，原来使用的jedis 被替换为了 lettuce？

jedis：采用的直连，多个线程操作的话，是不安全的，如果想要避免不安全的，使用 jedis pool 连接池！更像 BIO 模式lettuce：采用netty，实例可以再多个线程进程中进行共享，不存在线程不安全的情况！可以减少线程数据了，更像 NIO 模式



> 整合测试

1、导入依赖

```xml
<!--操作redis-->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```

2、配置连接

```bash
# 配置 redis
spring.redis.host=127.0.0.1
spring.redis.port=6379
```

3、c测试

```java
@SpringBootTest
class Redis02SpringbootApplicationTests {

    @Autowired
    private RedisTemplate redisTemplate;

    @Test
    void contextLoads() {
        // redisTemplate    操作不同的数据类型，api 和 我们的指令是一样的
        // opsForValue  操作字符串   类似String
        // opsForList   操作 list 类似List
        // opsForSet
        // opsForZSet
        // opsForGeo
        // opsForHyperLogLog

        // 除了基本的操作，我们常用的方法都可以i直接通过 redisTemplate 操作，比如事务，和基本的CRUD
        redisTemplate.opsForValue().set("mykey","试试xiaohong");
        System.out.println(redisTemplate.opsForValue().get("mykey"));
    }
}
```


