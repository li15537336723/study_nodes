## 事务

Redis 单条命令保存原子性的，但是事务不保存原子性！一个事务中的所有命令都会被序列化，在事务执行的过程中，会按照顺序执行！

一次性、顺序性、排他性！执行一些列的命令！

```
------ 队列 set set set 执行-------
```

**Redis 事物没有隔离级别的概念！**

所有的命令在事务中，并没有直接被执行！只有发起执行命令的时候才会执行！Exec

**Redis 单条命令保存原子性的，但是事务不保证原子性！**

Redis 的事务：

- 开启事务（multi）
- 命令入队（......）
- 执行事务（exec）

> 正常执行事务！

```bash
127.0.0.1:6379> multi	# 开启事务
OK
# 命令入队
127.0.0.1:6379> set k1 v1
QUEUED
127.0.0.1:6379> set k2 v2
QUEUED
127.0.0.1:6379> get k2
QUEUED
127.0.0.1:6379> set k3 v3
QUEUED
127.0.0.1:6379> exec	# 执行事务
1) OK
2) OK
3) "v2"
4) OK
```

> 放弃事务

```bash
127.0.0.1:6379> set k1 v1	# 开启事务
QUEUED
127.0.0.1:6379> set k2 v2
QUEUED
127.0.0.1:6379> set k4 v4
QUEUED
127.0.0.1:6379> discard		# 取消事务
OK
127.0.0.1:6379> get k4	# 事务队列中命令都不会被执行！
(nil)
```

> 编译型异常（代码有问题！命令有错！），事务中所有的命令都不会被执行！

![image-20210222205038171](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210222205038171.png)



> 运行时异常（1/0），如果事务队列中存在语法性，那么执行命令的时候，其他命令是可以正常执行的，错误命令抛出异常！





> 监控！Watch

**悲观锁：**

- 很悲观，什么时候都会出问题，无论做什么都会加锁！

**乐观锁：**

- 很乐观，认为什么时候都不会出问题，所以不会上锁！更新数据的时候去判断一下，在此期间是否有人修改过这个数据。
- 更新的时候比较 version

> Redis 测监视测试

正常执行成功！

```bash
127.0.0.1:6379> set money 100
OK
127.0.0.1:6379> set out 0
OK
127.0.0.1:6379> watch money		# 监视 money 对象
OK
127.0.0.1:6379> multi			# 事务正常结束，数据期间没有发生变动，这个时候就正常执行成功！
OK
127.0.0.1:6379> decrby money 20
QUEUED
127.0.0.1:6379> incrby out 20
QUEUED
127.0.0.1:6379> exec
1) (integer) 80
2) (integer) 20
```

测试多线程修改值，使用 watch 可以当做 redis 的乐观锁操作



```bash
127.0.0.1:6379> watch money
OK
127.0.0.1:6379> multi
OK
127.0.0.1:6379> decrby money 10
QUEUED
127.0.0.1:6379> incrby out 10
QUEUED
127.0.0.1:6379> exec
(nil)
```





















