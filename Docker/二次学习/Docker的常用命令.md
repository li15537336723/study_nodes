# Docker 的常用命令



## 帮助命令

```bash
docker version			# 显示docker的版本信息
docker info				# 显示docker的系统信息，包括镜像和容器的数量
docker 命令 --help	   # 帮助命令
```

帮助文档地址：https://docs.docker.com/engine/reference/commandline/build/



## 镜像命令

**docker images** 

```bash
[root@lxh-serve ~]# docker images
REPOSITORY    TAG       IMAGE ID       CREATED        SIZE
hello-world   latest    d1165f221234   6 months ago   13.3kB

# 解释
REPOSITORY	镜像的仓库源
TAG			镜像的标签
IMAGE ID	镜像的id
CREATED		镜像的创建时间
SIZE		镜像的大小

# 可选项
-a, --all			# 列出所有镜像
-q, --quiet			# 只显示镜像的id
```



**docker search 搜索镜像**

```bash
[root@lxh-serve ~]# docker search mysql
NAME     DESCRIPTION         							 STARS   OFFICIAL   AUTOMATED
mysql    MySQL is a widely used, open-source relation…   11376   [OK]       
mariadb  MariaDB Server is a high performing open sou…   4316    [OK]       
# 可选项
--filter=stars=300		# 搜索出来的镜像就是 STARS 大于 3000 的
docker search mysql --filter=stars=3000
NAME     DESCRIPTION         							 STARS   OFFICIAL   AUTOMATED
mysql    MySQL is a widely used, open-source relation…   11376   [OK]       
mariadb  MariaDB Server is a high performing open sou…   4316    [OK] 
```



**docker pull 下载镜像**

```bash
[root@lxh-serve ~]# docker pull mysql
Using default tag: latest	# 如果不写 tag，默认就是 latest
latest: Pulling from library/mysql 
a330b6cecb98: Pull complete 	# 分层下载，docker image的核心 联合文件系统
9c8f656c32b8: Pull complete 
88e473c3f553: Pull complete 
062463ea5d2f: Pull complete 
daf7e3bdf4b6: Pull complete 
1839c0b7aac9: Pull complete 
cf0a0cfee6d0: Pull complete 
1b42041bb11e: Pull complete 
10459d86c7e6: Pull complete 
b7199599d5f9: Pull complete 
1d6f51e17d45: Pull complete 
50e0789bacad: Pull complete 
Digest: sha256:99e0989e7e3797cfbdb8d51a19d32c8d286dd8862794d01a547651a896bcf00c	# 签名
Status: Downloaded newer image for mysql:latest # 真实地址
docker.io/library/mysql:latest

# 下载指定版本
[root@lxh-serve ~]# docker pull mysql:5.7
5.7: Pulling from library/mysql
a330b6cecb98: Already exists 
9c8f656c32b8: Already exists 
88e473c3f553: Already exists 
062463ea5d2f: Already exists 
daf7e3bdf4b6: Already exists 
1839c0b7aac9: Already exists 
cf0a0cfee6d0: Already exists 
fae7a809788c: Pull complete 
dae5a82a61f0: Pull complete 
7063da9569eb: Pull complete 
51a9a9b4ef36: Pull complete 
Digest: sha256:d9b934cdf6826629f8d02ea01f28b2c4ddb1ae27c32664b14867324b3e5e1291
Status: Downloaded newer image for mysql:5.7
docker.io/library/mysql:5.7
```



**docker rmi 删除镜像**

```bash
[root@lxh-serve ~]# docker rmi -f 容器id				# 删除指定容器
[root@lxh-serve ~]# docker rmi -f 容器id 容器id 容器id 容器id	# 删除多个容器
[root@lxh-serve ~]# docker rmi -f $(docker images -aq)  	# 删除全部的容器
```



## 容器命令

说明：我们有了镜像才可以创建容器，linux，下载一个 centos 镜像来测试

```bash
docker pull centos
```

**新建容器并启动**

```bash
docker run [可选参数] image

# 参数说明
--name="Name"	容器名字 tomcat01 tomcat02，用来区分容器
-d				后台方式运行
-it				使用交互方式运行，进入容器查看内容
-p				指定容器的端口 -p 8080:8080
	-p ip:主机端口：容器端口
	-p 主机端口：容器端口（常用）
	-p 容器端口
	容器端口
-p				随机指定端口

# 测试，启动并进入容器
[root@lxh-serve ~]# docker run -it centos
[root@8b740233d5e2 /]# ls
bin  dev  etc  home  lib  lib64  lost+found  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
# 从容器中退回主机
exit
```



**列出所有运行的容器**

```bash
# docker ps 命令
	# 列出当前正在运行的容器
-a 	# 列出当前正在运行的容器 + 带出历史运行过的容器
-n=？#显示最近创建的容器
-q # 只显示容器的编号
[root@lxh-serve ~]# docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
[root@lxh-serve ~]# docker ps -a
CONTAINER ID   IMAGE          COMMAND       CREATED         STATUS                     PORTS     NAMES
8b740233d5e2   centos         "/bin/bash"   6 minutes ago   Exited (0) 6 minutes ago             silly_davinci
3f2be1f4a67a   d1165f221234   "/hello"      17 hours ago    Exited (0) 17 hours ago              condescending_moore
4556dc2b3b6d   d1165f221234   "/hello"      17 hours ago    Exited (0) 17 hours ago              hopeful_fermat
```



**退出容器**

```bash
exit	# 直接容器停止并退出
Ctrl + P + Q 	# 容器不停止退出
```

**删除容器**

```bash
docker rm 容器id					# 删除指定的容器，不能删除正在运行的容器，如果要强制删除 rm -f
docker rm -f $(docker ps -aq)	# 删除所有的容器
```



**启动和停止容器的操作**

```bash
docker start 容器id		# 启动容器
docker restart 容器id		# 重启容器
docker stop 容器id		# 停止当前正在运行的容器
docker kill 容器id		# 强制停止当前容器
```



## 其他常用命令

**后台启动容器**

```bash
# 命令 docker run -d 镜像名
[root@lxh-serve ~]# docker run -d centos
# 问题：docker ps，发现 centos 停止了

# 常见的坑，docker 容器使用后台运行，就必须要有一个前台的进程，docker发现没有应用，就会自动停止
# nginx，容器启动后，发现自己没有提供服务，就会立刻停止，就是没有程序了。
```

**查看日志**

```bash
docker logs -f -t --tail 容器，没有日志
```

**查看容器中进程信息 ps**

```bash
# top 命令 容器id
[root@lxh-serve ~]# docker top 0c0a2a7b31f9
UID             PID            PPID            C            STIME               TTY   
root            8984           8964            0            09:52               ?     
root            9160           8984            0            09:54               ?     
```

**查看镜像原数据**

```bash
# docker inspect 容器id
[root@lxh-serve ~]# docker inspect 0c0a2a7b31f9
[
    {
        "Id": "0c0a2a7b31f9ab111411076cf4ecae05911d8e289c424f44b0c825aea051ff5f",
        "Created": "2021-09-06T01:52:42.625035631Z",
        "Path": "/bin/sh",
        "Args": [

```

**进入当前正在运行的容器**

```bash
# 我们通常都是使用后台方式运行的，需要进入容器，修改一些配置

# 命令
docker exec -it 容器id /bin/bash

# 测试
[root@lxh-serve ~]# docker exec -it 0c0a2a7b31f9 /bin/bash
[root@0c0a2a7b31f9 /]# ps -ef
UID        PID  PPID  C STIME TTY          TIME CMD
root         1     0  0 01:52 ?        00:00:00 /bin/sh -c while true;do echo kuangshen; sleep 1;done
root       778     0  0 02:05 pts/0    00:00:00 /bin/bash
root       819     1  0 02:06 ?        00:00:00 /usr/bin/coreutils --coreutils-prog-shebang=sleep /usr/bin/sleep 1
root       820   778  0 02:06 pts/0    00:00:00 ps -ef
[root@0c0a2a7b31f9 /]# ls
bin  dev  etc  home  lib  lib64  lost+found  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var

# 方式二
docker attach 容器id
# 测试
[root@lxh-serve ~]#docker attach 0c0a2a7b31f9
正在执行当前的代码......
```

**从容器内拷贝文件主机上**

```bash
[root@lxh-serve ~]# docker run -it centos /bin/bash
[root@f10b60941411 /]# [root@lxh-serve ~]# docker ps
CONTAINER ID   IMAGE     COMMAND       CREATED         STATUS         PORTS     NAMES
f10b60941411   centos    "/bin/bash"   6 seconds ago   Up 4 seconds             competent_gauss
[root@lxh-serve ~]# cd /home
[root@lxh-serve home]# ls
admin
[root@lxh-serve home]# touch kuangshen.java

# 进入到容器内部
[root@lxh-serve home]# docker attach f10b60941411
[root@f10b60941411 /]# ls
bin  dev  etc  home  lib  lib64  lost+found  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
[root@f10b60941411 /]# cd /home
[root@f10b60941411 home]# ls

# 在容器内新建一个文件
[root@f10b60941411 home]# touch test.java
[root@f10b60941411 home]# exit
exit
[root@lxh-serve home]# docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
[root@lxh-serve home]# docker ps -a
CONTAINER ID   IMAGE     COMMAND       CREATED              STATUS                     PORTS     NAMES
f10b60941411   centos    "/bin/bash"   About a minute ago   Exited (0) 6 seconds ago             competent_gauss

# 将这个文件拷贝到 主机上
[root@lxh-serve home]# docker cp f10b60941411:/home/test.java /home
[root@lxh-serve home]# ls
admin  kuangshen.java  test.java
```



## 练习

> Docker 安装 Nginx

```bash
# 1. 搜索镜像 search 
# 2. 下载镜像 pull

[root@lxh-serve home]# docker images
REPOSITORY   TAG       IMAGE ID       CREATED        SIZE
nginx        latest    822b7ec2aaf2   2 days ago     133MB
centos       latest    300e315adb2f   9 months ago   209MB

# -d 后台运行
# --name 给容器命名
# -p 宿主机端口：容器内部端口
[root@lxh-serve home]# docker run -d --name nginx01 -p 3344:80 nginx
95f85908b26d3c7a7b19bba2f0f4d3051583256844ea28d4b5a1fc4006824a23
[root@lxh-serve home]# docker ps
CONTAINER ID   IMAGE     COMMAND                  CREATED         STATUS         PORTS                  NAMES
95f85908b26d   nginx     "/docker-entrypoint.…"   2 seconds ago   Up 2 seconds   0.0.0.0:3344->80/tcp   nginx01
[root@lxh-serve home]# curl localhost:3344


# 进入容器
[root@lxh-serve home]# docker exec -it nginx01 /bin/bash
root@95f85908b26d:/# whereis nginx
nginx: /usr/sbin/nginx /usr/lib/nginx /etc/nginx /usr/share/nginx
root@95f85908b26d:/# cd /etc/nginx
root@95f85908b26d:/etc/nginx# ls
conf.d	fastcgi_params	mime.types  modules  nginx.conf  scgi_params  uwsgi_params
root@95f85908b26d:/etc/nginx# 
```



> Docker 安装 Tomcat

```bash
# 先下载再启动
docker pull tomcat

# 启动运行
docker run -d -p 3355:8080 --name tomcat01 tomcat

# 测访问没有问题
```



> 部署 es + kibana

```bash
# es 暴露的端口很多！
# es 十分的耗内存
# es 的数据一般需要放置到安全目录！挂载
# --net somenetwork ？ 网络配置

# 启动 elasticsearch
docker run -d --name elasticsearch -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" elasticsearch:7.6.2

# 测试一下 es 是否成功
[root@lxh-serve ~]# curl localhost:9200
{
  "name" : "a57313bcb182",
  "cluster_name" : "docker-cluster",
  "cluster_uuid" : "QSVly8JKQ2m0mDplu2Sbmw",
  "version" : {
    "number" : "7.6.2",
    "build_flavor" : "default",
    "build_type" : "docker",
    "build_hash" : "ef48eb35cf30adf4db14086e8aabd07ef6fb113f",
    "build_date" : "2020-03-26T06:34:37.794943Z",
    "build_snapshot" : false,
    "lucene_version" : "8.4.0",
    "minimum_wire_compatibility_version" : "6.8.0",
    "minimum_index_compatibility_version" : "6.0.0-beta1"
  },
  "tagline" : "You Know, for Search"
}


```

```bash
# 内存占用过多，使用 -e 环境配置进行修改  将大小限制再 64 —— 512 之间
docker run -d --name elasticsearch02 -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" -e ES_JAVA_OPTS="-Xms64m -Xmx512m" elasticsearch:7.6.2
```

![image-20210909090503816](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210909090503816.png)



## docker 启动 Redis

```bash
docker run -d --name redis -p 6379:6379 --restart=always redis redis --appendonly yes --requirepass "li12345..."
```



## 可视化

- portainer

```bash
docker run -d -p 8088:9000\ --restart=always -v /var/run/docker.sock:/var/run/docker.sock --privileged=true portainer/ portainer
```

- Rancher (CI/CD 再用)

**什么是 portainer ?**

可视化面板，一般不会用





























