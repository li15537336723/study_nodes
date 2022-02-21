## 部署 Redis 集群

![image-20210310210208826](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210310210208826.png)



shell脚本

```shell
# 创建网卡
docker network create redis --subnet 172.38.0.0/16

# 通过脚本创建六个 redis 配置
for port in $(seq 1 6);\
do \
mkdir -p /mydata/redis/node-${port}/conf
touch /mydata/redis/node-${port}/conf/redis.conf
cat << EOF >/mydata/redis/node-${port}/conf/redis.conf
port 6379
bind 0.0.0.0
cluster-enabled yes 
cluster-config-file nodes. conf
cluster-node-timeout 5000
cluster announce-ip 172.38 0.1${port}
cluster-announce-port 6379
cluster-announce-bus-port 16379
appendonly yes
EOF
done

docker run -p 6371:6379 -p 16371:16379 --name redis-1 \
-v /mydata/redis/node-1/data:/data \
-v /mydata/redis/node-1/conf/redis. conf :/etc/redis/redis. conf \
-d --net redis --ip 172.38.0.11 redis:5.0. 9-alpine3.11 redis-server /etc/redis/redis. conf

```



移除正在运行的所有容器

`docker rm -f $(docker ps -aq)`































