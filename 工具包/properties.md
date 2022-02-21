```properties
server.port=8989

spring.datasource.type=com.alibaba.druid.pool.DruidDataSource
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
spring.datasource.url=jdbc:mysql://localhost:3306/music_test?serverTimezone=UTC&userSSL=true&userUnicode=true&characterEncoding=UTF-8
spring.datasource.username=root
spring.datasource.password=li12345

mybatis.type-aliases-package=com.javaclimb.music.domain
mybatis.mapper-locations=classpath:mapper/*.xml

# 打印出日志
logging.level.com.javaclimb.music.dao = debug
```



























































