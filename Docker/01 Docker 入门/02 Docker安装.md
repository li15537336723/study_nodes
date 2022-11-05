## Docker 安装

### Docker 的基本组成

![image-20210301145226266](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210301145226266.png)

**镜像（image）**

docker 镜像就好比是一个模板，可以通过这个模板来创建容器服务，tomcat镜像 ===>run---->tomcat01 容器（提供服务器）

**容器（container）**

Docker 利用容器技术，独立运行一个或者一个组应用，通过镜像来创建

启动，停止，删除，基本命令！

目前就可以把这个容器理解为就是一个简易的linux系统

**仓库（repository）**

仓库就是存放镜像的地方！

仓库分为共有仓库和私有仓库！



### 安装 Docker

> 环境准备

1、会 Linux 基础

2、CentOS 7

3、我们使用 Xshell 链接远程服务器进行操作！

> 环境查看

```bash
# 系统内核
[root@xiaohong /]# uname -r
4.18.0-147.5.1.el8_1.x86_64
```

```bash
# 系统版本
[root@xiaohong /]# cat /etc/os-release
NAME="CentOS Linux"
VERSION="8 (Core)"
ID="centos"
ID_LIKE="rhel fedora"
VERSION_ID="8"
PLATFORM_ID="platform:el8"
PRETTY_NAME="CentOS Linux 8 (Core)"
ANSI_COLOR="0;31"
CPE_NAME="cpe:/o:centos:centos:8"
HOME_URL="https://www.centos.org/"
BUG_REPORT_URL="https://bugs.centos.org/"

CENTOS_MANTISBT_PROJECT="CentOS-8"
CENTOS_MANTISBT_PROJECT_VERSION="8"
REDHAT_SUPPORT_PRODUCT="centos"
REDHAT_SUPPORT_PRODUCT_VERSION="8"
```



> 安装

帮助文档：

```bash
# 1、卸载旧的版本
yum remove docker \
            docker-client \
            docker-client-latest \
            docker-common \
            docker-latest \
            docker-latest-logrotate \
            docker-logrotate \
            docker-engine
            
# 2、需要的安装包
yum install -y yum-utils \
  device-mapper-persistent-data \
  lvm2

# 3、设置镜像仓库
yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo
    
国内的  (一般使用国内的)
yum-config-manager \
	--add-repo \
	http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo

# 更新 yum 软件包索引
[root@xiaohong /]# yum makecache fast

# 4、安装 docker 相关的引擎
yum install docker-ce docker-ce-cli containerd.io

# 5、启动 docker
systemctl start docker

# 6、使用 docker version 是否安装成功
```

![image-20210301154722773](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210301154722773.png)

```bash
# 7、hello-world
docker run hello-world
```

![image-20210301155037273](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210301155037273.png)



```bash
# 8、查看一下下载的这个 hello-world 镜像
[root@xiaohong /]# docker images
REPOSITORY    TAG       IMAGE ID       CREATED         SIZE
hello-world   latest    bf756fb1ae65   14 months ago   13.3kB
```

了解：卸载 docker

```shell
# 1、卸载依赖
yum remove docker-ce docker-ce-cli containerd.io
# 2、删除资源
rm -rf /var/lib/docker
# /var/lib/docker		docker 的默认工作路径！
```





### 阿里云镜像加速

![](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210301155831388.png)



配置使用

```bash
sudo mkdir -p /etc/docker

sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://nlm682f3.mirror.aliyuncs.com"]
}
EOF

sudo systemctl daemon-reload

sudo systemctl restart docker
```



### 回顾 HelloWorld 流程

![image-20210301160150716](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210301160150716.png)



### 第层原理

**Docker 是怎么工作的？**

Docker 是一个 Client - Server 结构的系统，Docker 的守护进程运行在主机上。通过 Socket从客户端访问！

DockerServer 接收到 Docker-Client 的指令，就会执行这个命令！

![image-20210301160917854](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210301160917854.png)

Docker 为什么比 VM 快？

1、Docker 有着比虚拟机更少的抽象层

2、docker 利用的是宿主机的内核，vm 需要是 Guess OS。

![image-20210301161112896](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210301161112896.png)

所以说，新建一个容器的时候，docker 不需要像虚拟机一样重新加载一个操作系统内核，避免引导。虚拟机是加载 Guest OS，分钟级别的，而 docker 是利用 宿主机的操作系统码，省略了这个复杂的过程，秒级！

![image-20210301161345624](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210301161345624.png)



