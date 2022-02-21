## Redis.config 详解

启动的时候，就通过配置文件来启动！

> 网络

```bash
bind 127.0.0.1		# 绑定的 ip
protected-mode yes	# 保护模式
prot 6379		# 端口设置
```

> 通用 GENERAL

```bash
daemonize yes		# 以守护进程的方式运行，默认是 no，我们需要自己开启为 yes！

pidfile /var/run/redis_6379.pid	# 如果以后台的方式运行，我们就需要指定一个 pid 文件

# 日志
loglevel notice
logfile "" 	# 日志文件位置名
databasees 16 # 数据库的数量，默认是 16 个数据库
always-show-logo  yes 	# 是否总是显示 LOGO
```



> 快照

持久化，在规定时间内，执行了多少次操作，则会持久化到文件 .rdb .aof

redis 是内存数据库，如果没有持久化，那么数据断电即失！

```bash
# 如果 900s 内，如果至少有一个 key 进行了修改，我们及进行持久化操作
save 900 1
# 如果300s内，如果至少 10 key 进行了修改，我们及进行持久化操作
save 300 10
# 如果 60 s内，如果至少 10000 key 进行了修改，我们及进行持久化操作
save 60 10000
# 我们之后学习持久化，会自己定义这个测试！

stop-writes-on-bgsave-error yes  # 持久化如果出错，是否还需要继续工作！

rdbcompression yes 	# 是否压缩 rdb 文件，需要消耗一些 cpu 资源

rdbchecksum yes 	# 保护 rdb 文件的时候，进行错误的检查校验！

dir ./ 		# rdb 文件保存的目录！
```

> REPLICATION 复制，我们后面讲解主从复制的



> security 安全

```bash
127.0.0.1:6379> config set requirepass "li12345..."		# 获取 redis 的密码
OK
127.0.0.1:6379> auth li12345...			# 使用密码进行登陆
OK
127.0.0.1:6379> config get requirepass	
1) "requirepass"
2) "li12345..."
```

> 限制 clients

```bash
maxclients 1000  # 设置能连接上 redis 的最大客户端的数量
maxmemory<bytes> # redis 配置最大的内存容量
maxmemory-policy noeviction 	# 内存达到上限之后的处理策略
```

![image-20210223114837443](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210223114837443.png)


