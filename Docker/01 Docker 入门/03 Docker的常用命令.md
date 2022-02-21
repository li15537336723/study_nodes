## Docker 的常用命令

### 帮助命令

```bash
docker version		# 显示 docker 的版本信息
docker info			# 显示 docker 的系统信息，包括镜像和容器的数量
docker 命令 --help   # 帮助命令
```

帮助文档的地址：https://docs.docker.com/engine/install/centos/



### 镜像命令

**docker images** 查看所有本地的主机上的镜像

```bash
[root@xiaohong /]# docker images
REPOSITORY    TAG       IMAGE ID       CREATED         SIZE
hello-world   latest    bf756fb1ae65   14 months ago   13.3kB

# 解释
REPOSITORY	镜像的仓库源
TAG			镜像的标签
IMAGE ID	镜像的 id
CREATED		镜像的创建时间
SIZE		镜像的大小

# 可选项
-a, --all		# 列出所有镜像
-q, --quite		# 只显示镜像的 id
```

docker search 搜索镜像

```bash
[root@xiaohong /]# docker search mysql
NAME                              DESCRIPTION                                    STARS OFFICIAL   AUTOMATED
mysql                             MySQL is a widely used, open-source relation…  10563 [OK]       
mariadb                           MariaDB Server is a high performing open sou…   3947 [OK]       

# 可选项，通过搜藏来过滤
[root@xiaohong /]# docker search mysql --filter=STARS=3000
NAME      DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
mysql     MySQL is a widely used, open-source relation…   10563     [OK]       
mariadb   MariaDB Server is a high performing open sou…   3947      [OK]       
[root@xiaohong /]# docker search mysql --filter=STARS=5000
NAME      DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
mysql     MySQL is a widely used, open-source relation…   10563     [OK]       
[root@xiaohong /]#
```

**docker pull** 下载镜像

 ```bash
# 下载镜像 docker pull 镜像名[:tag]
[root@xiaohong /]# docker pull mysql
Using default tag: latest	# 如果不写 tag，默认就是 latest
latest: Pulling from library/mysql
a076a628af6f: Pull complete 	# 分层下载， docker image 的核心，联合文件系统
f6c208f3f991: Pull complete 
88a9455a9165: Pull complete 
406c9b8427c6: Pull complete 
7c88599c0b25: Pull complete 
25b5c6debdaf: Pull complete 
43a5816f1617: Pull complete 
1a8c919e89bf: Pull complete 
9f3cf4bd1a07: Pull complete 
80539cea118d: Pull complete 
201b3cad54ce: Pull complete 
944ba37e1c06: Pull complete 
Digest: sha256:feada149cb8ff54eade1336da7c1d080c4a1c7ed82b5e320efb5beebed85ae8c	# 签名
Status: Downloaded newer image for mysql:latest
docker.io/library/mysql:latest	# 真实地址

# 指定下载版本
[root@xiaohong /]# docker pull mysql:5.7
5.7: Pulling from library/mysql
a076a628af6f: Already exists 
f6c208f3f991: Already exists 
88a9455a9165: Already exists 
406c9b8427c6: Already exists 
7c88599c0b25: Already exists 
25b5c6debdaf: Already exists 
43a5816f1617: Already exists 
1831ac1245f4: Pull complete 
37677b8c1f79: Pull complete 
27e4ac3b0f6e: Pull complete 
7227baa8c445: Pull complete 
Digest: sha256:b3d1eff023f698cd433695c9506171f0d08a8f92a0c8063c1a4d9db9a55808df
Status: Downloaded newer image for mysql:5.7
docker.io/library/mysql:5.7
 ```

![image-20210301170729980](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210301170729980.png)



**docker rmi** 删除镜像！

```bash
[root@xiaohong /]# docker rmi -f 容器id				# 删除指定的容器
[root@xiaohong /]# docker rmi -f 容器id 容器id 容器id		# 删除多个容器
[root@xiaohong /]# docker rmi -f $(docker images -aq)	# 删除全部容器
```

### 容器命令

说明：我们有了镜像才可以创建容器，linux，下载一个 centos 镜像来测试

```bash
docker pull centos
```

新建容器并启动

```bash
docker run [可选参数] iamge

# 参数说明
--name="Name"	容器名字 tomcat01 tomcat02, 用来区分容器
-d				后台运行方式
-it				使用交互方式运行，进入容器查看内容
-p				指定容器的端口 -p 8080:8080
	-p ip:主机端口：容器端口
	-p 主机端口：容器端口（常用）
	-p 容器端口
	容器端口
-p			随机指定端口

# 测试	启动并进入容器
[root@xiaohong /]# docker run -it centos /bin/bash
[root@7ae60c41225e /]# ls	# 查看容器内的 centos，基础版本
bin  dev  etc  home  lib  lib64  lost+found  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var

#从容器中退回主机
[root@7ae60c41225e /]# exit
exit
[root@xiaohong /]# ls
bin  boot  dev  etc  home  lib  lib64  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
```

**列出所有的运行容器**

```bash
# docker ps 命令
-a # 列出当前正在运行的容器+带出里斯运行过的容器
-n=?  # 显示最近创建的容器
-q  # 只显示容器的编号

[root@xiaohong /]# docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
[root@xiaohong /]# docker ps -a
CONTAINER ID   IMAGE         COMMAND       CREATED          STATUS                          PORTS     NAMES
7ae60c41225e   centos        "/bin/bash"   20 minutes ago   Exited (0) About a minute ago             sweet_hoover
dbb75415a18d   hello-world   "/hello"      4 hours ago      Exited (0) 4 hours ago                    inspiring_snyder
61b3a43ec833   hello-world   "/hello"      23 hours ago     Exited (0) 23 hours ago                   ecstatic_albattani
```

**退出容器**

```bash
exit	# 直接容器停止并退出
ctrl + p + q  # 容器不停止退出
```

**删除容器**

```bash
docker rm 容器 id			# 删除指定的容器，不能删除正在运行的容器，如果要强制删除 rm -f
docker rm -f$(docker ps -aq)	# 删除所有的容器
docker ps -a -q|xargs docker rm	# 删除所有的容器
```

**启动和停止容器的操作**

```bash
docker start 容器id		# 启动容器
docker restart 容器id		# 重启容器
docker stop 容器id		# 停止当前正在运行的容器
docker kill 容器id		# 强制停止当前容器
```



### 常用的其他命令

后台启动容器

```bash
# 命令 docker run -d 镜像名

```



**进入当前正在运行的容器**

```bash
 # 我们常用容器都是使用后台方式运行的，需要进入容器，修改一些配置
 
 # 命令
 docker exec -it 容器id bashShell
 
 # 测试
[root@xiaohong /]# docker ps
CONTAINER ID   IMAGE     COMMAND       CREATED         STATUS         PORTS     NAMES
ae791079b1fe   centos    "/bin/bash"   7 minutes ago   Up 7 minutes             nifty_pike
204c9053952d   centos    "/bin/bash"   2 days ago      Up 2 days                vigilant_kilby
[root@xiaohong /]# docker exec -it ae791079b1fe /bin/bash
[root@ae791079b1fe /]# ls
bin  dev  etc  home  lib  lib64  lost+found  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
[root@ae791079b1fe /]# ps -ef
UID        PID  PPID  C STIME TTY          TIME CMD
root         1     0  0 01:20 pts/0    00:00:00 /bin/bash
root        16     0  0 01:29 pts/1    00:00:00 /bin/bash
root        31    16  0 01:29 pts/1    00:00:00 ps -ef

# 方式二
docker attach 容器id
# 测试
[root@xiaohong ~]# docker attach  ae791079b1fe
正在执行当前的代码.......

# docker exec		# 进入容器后开启一个新的终端，可以在里面操作（常用）
# docker attach		# 进入容器正在执行的终端，不会启动新的进程！
```



**从容器内拷贝文件到主机上**

```shell
docker cp 容器id：容器内路径	目的的主机路径

# 查看当前目录下
[root@xiaohong home]# ls
xiaohong  xiaohong.java
[root@xiaohong home]# docker ps
CONTAINER ID   IMAGE     COMMAND       CREATED              STATUS              PORTS     NAMES
ed3a4d30725f   centos    "/bin/bash"   About a minute ago   Up About a minute             unruffled_keldysh
204c9053952d   centos    "/bin/bash"   2 days ago           Up 2 days                     vigilant_kilby
# 进入docker容器
[root@xiaohong home]# docker attach 204c9053952d
[root@204c9053952d /]# cd /home
[root@204c9053952d home]# ls
# 在容器内新建一个文件
[root@204c9053952d home]# touch test.java
[root@204c9053952d home]# exit
exit
[root@xiaohong home]#  docker ps
CONTAINER ID   IMAGE     COMMAND       CREATED          STATUS          PORTS     NAMES
ed3a4d30725f   centos    "/bin/bash"   13 minutes ago   Up 13 minutes             unruffled_keldysh
[root@xiaohong home]# docker ps -a
CONTAINER ID   IMAGE     COMMAND       CREATED          STATUS                        PORTS     NAMES
ed3a4d30725f   centos    "/bin/bash"   13 minutes ago   Up 13 minutes                           unruffled_keldysh
ae791079b1fe   centos    "/bin/bash"   29 minutes ago   Exited (127) 13 minutes ago             nifty_pike
a41db811d505   centos    "/bin/bash"   2 days ago       Exited (0) 2 days ago                   clever_wright
204c9053952d   centos    "/bin/bash"   2 days ago       Exited (0) 11 seconds ago               vigilant_kilby
[root@xiaohong home]# ls
xiaohong  xiaohong.java
# 将这个文件拷贝到主机上
[root@xiaohong home]# docker cp 204c9053952d:/home/test.java /home
[root@xiaohong home]# ls
test.java  xiaohong  xiaohong.java

# 拷贝是一个手动过程，未来我们使用 -v 卷的技术，可以实现，自动同步
```











