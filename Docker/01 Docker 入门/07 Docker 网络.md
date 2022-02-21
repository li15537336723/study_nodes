## Docker 网络

### 理解 Docekr0

首先清除所有的环境

清空

`docker rm -f $(docker ps -aq)`

清空所有的镜像 

`docker rmi -f $(docker images -aq)`

> 测试

![image-20210310191422214](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210310191422214.png)

三个网络

```bash
# 问题： docker 是如何处理容器网络访问的？
```

![image-20210310191510860](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210310191510860.png)



```bash
[root@xiaohong /]# docker run -d -P --name tomcat01 tomcat

# 查看容器内的内部网络地址 ip addr，容器启动的时候会得到一个 eth0@if73 ip地址，dockers分配的
[root@xiaohong /]# docker exec -it tomcat01 ip addr
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
72: eth0@if73: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
    link/ether 02:42:ac:11:00:02 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 172.17.0.2/16 brd 172.17.255.255 scope global eth0
       valid_lft forever preferred_lft forever
# 思考，linux 能不能 ping 通容器内部!
[root@xiaohong /]# ping 172.17.0.2
PING 172.17.0.2 (172.17.0.2) 56(84) bytes of data.
64 bytes from 172.17.0.2: icmp_seq=1 ttl=64 time=0.081 ms
64 bytes from 172.17.0.2: icmp_seq=2 ttl=64 time=0.063 ms

# linux 可以 ping 通 docker 容器内部
```



> 原理

1、我们安装一个docker 容器，docker 就会被 dockers 容器分配一个 ip，我们只要安装了 docker，就会有一个 docker0 桥接模式，使用的技术是 evth-pair 技术！

再次测试 ip addr

![image-20210310192322876](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210310192322876.png)

2、再启动一个容器测试，发现有多了一对网卡

![image-20210310192450790](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210310192450790.png)

```bash
# 我们发现这个容器带来网卡，都是一对对的
# evth-pair 就是一对的虚拟设备接口，他们都是成对出现的，一段连着协议，一段彼此相连
# 正因为有这个特性，evth-pair 充当一个桥梁，连接各种虚拟网络设备的
# OpenStac，Docker 容器之间的连接，OVS 的连接，都是使用 evth-pair 技术
```

3、我们来测试下 tomcat01 和 tomcat02 是否可以 ping 通！

```bash
[root@xiaohong /]# docker exec -it tomcat02 ping 172.17.0.2

# 结论：容器和容器之间是可以 ping 通的！
```

绘制一个网络模型图：

![image-20210310193305271](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210310193305271.png)

结论：tomcat01 和 tomcat02 是共用的一个路由器，docker0

所有的容器不指定网络的情况下，都是 docker0 路由的，docker 会给我们的容器分配一个默认的可用 ip

255.255.0.1/16 域  局域网

000000.000000.000000.000000

255.255.255.255



> 小结

Docker 使用的是 linux 的桥接，宿主机中是一个 Docker 容器的网桥 docker0

![image-20210310193818640](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210310193818640.png)

Docker 中所有的网络接口都是虚拟的，虚拟的转发效率高！（内网传递文件！）

只要容器删除，对应网桥一对就没了



![image-20210310195256855](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210310195256855.png)



### --Link

> 思考一个场景，我们编写了一个微服务，database url = ip 项目不重启，数据库 ip 换掉了，我们希望可以处理这个问题，可以名字来进行访问容器？

```shell
[root@xiaohong /]# docker exec -it tomcat02 ping tomcat01
ping: tomcat01: Name or service not known

# 如何解决呢？
# 通过 --link 既 可以解决了网络联通问题
[root@xiaohong /]# docker run -d -P --name tomcat03 --link tomcat02 tomcat
47d632b7f5258754e38f266fa487710c21c5319495989c953aa0c8d1d14a5988
[root@xiaohong /]# docker exec -it tomcat03 ping tomcat02
PING tomcat02 (172.17.0.3) 56(84) bytes of data.
64 bytes from tomcat02 (172.17.0.3): icmp_seq=1 ttl=64 time=0.107 ms
64 bytes from tomcat02 (172.17.0.3): icmp_seq=2 ttl=64 time=0.081 ms
64 bytes from tomcat02 (172.17.0.3): icmp_seq=3 ttl=64 time=0.074 ms

# 反向可以 ping 通嘛？
[root@xiaohong /]# docker exec -it tomcat02 ping tomcat03
ping: tomcat03: Name or service not known
```



探究：inspect

![image-20210310195350224](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210310195350224.png)

这个tomcat03 就是在本地配置了tomcat02的配置

```shell
# 查看 hosts 配置；在这里原理发现
[root@xiaohong /]# docker exec -it tomcat03 cat /etc/hosts
127.0.0.1	localhost
::1	localhost ip6-localhost ip6-loopback
fe00::0	ip6-localnet
ff00::0	ip6-mcastprefix
ff02::1	ip6-allnodes
ff02::2	ip6-allrouters
172.17.0.3	tomcat02 c735cb8a5295
172.17.0.4	47d632b7f525
```

本质探究：--link 就是我们在hosts 配置中增加了一个 `172.17.0.3	tomcat02 c735cb8a5295` 

我们现在玩Docker 已经不建议使用 --link 了！

自定义网络！不适用 docker0！

docker0 问题：不支持容器名连接访问！



### 自定义网络

> 查看所有的 docker 网络

![image-20210310200541607](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210310200541607.png)

**网络模式**

bridge：桥接 docker (默认)

none：不配置网络

host：和宿主机共享网络

container：容器内网络连通！（用的少！局限大）



**测试**

```shell
# 我们直接启动的命令 --net bridge ,而这个就是我们的 docker0
docker run -d -P --name tomcat01 tomcat
docker run -d -P --name tomcat01 --net bridge tomcat

# docker0 特点，域名不能访问 --link可以打通连接！

#我们可以自定义网络！
# --driver bridge
# --subnet 192.168.0.0/16
# --gateway 192.168.0.1 mynet
[root@xiaohong /]# docker network create --driver bridge --subnet 192.168.0.0/16 --gateway 192.168.0.1 mynet
c7d9cc726966c94f253a2c38c36b5fa95e6a29ec338db3643de8bcf192938dfa

[root@xiaohong /]# docker network ls
NETWORK ID     NAME      DRIVER    SCOPE
83b3b9a8653a   bridge    bridge    local
0ff08c84fd0a   help      bridge    local
58738b644430   host      host      local
c7d9cc726966   mynet     bridge    local
2d20173c9c20   none      null      local
```



我们自己的网络就创建好了

![image-20210310202147689](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210310202147689.png)



```shell
		"Internal": false,
        "Attachable": false,
        "Ingress": false,
        "ConfigFrom": {
            "Network": ""
        },
        "ConfigOnly": false,
        "Containers": {
            "0646e2ba52a3a41dfa75adcf8fdd284c0ebc6d3d95e0dfed2f874e367ca088e4": {
                "Name": "tomcat-net-02",
                "EndpointID": "35aab2bc9da0bb60c6af5164d91f9e4716b0d9d31a0185b703e562aed6e67602",
                "MacAddress": "02:42:c0:a8:00:03",
                "IPv4Address": "192.168.0.3/16",
                "IPv6Address": ""
            },
            "b50bab5750fc7b19945f173f1647a70086fb28a3e65e212fcdf139392a804bff": {
                "Name": "tomcat-net-01",
                "EndpointID": "0725ba300ca10fb8eacc011d7fac9d66fe0677b326e13b3f5d474f80392b8f19",
                "MacAddress": "02:42:c0:a8:00:02",
                "IPv4Address": "192.168.0.2/16",
                "IPv6Address": ""
            }
        },
        "Options": {},
        "Labels": {}
    }
    
    
# 再次测试 ping 连接
[root@xiaohong /]# docker exec -it tomcat-net-01 ping 192.168.0.3
PING 192.168.0.3 (192.168.0.3) 56(84) bytes of data.
64 bytes from 192.168.0.3: icmp_seq=1 ttl=64 time=0.134 ms
64 bytes from 192.168.0.3: icmp_seq=2 ttl=64 time=0.080 ms
64 bytes from 192.168.0.3: icmp_seq=3 ttl=64 time=0.081 ms
64 bytes from 192.168.0.3: icmp_seq=4 ttl=64 time=0.089 ms
^C
--- 192.168.0.3 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 80ms
rtt min/avg/max/mdev = 0.080/0.096/0.134/0.022 ms

# 现在不使用 --link 也可以 ping 名字了
[root@xiaohong /]# docker exec -it tomcat-net-01 ping tomcat-net-02
PING tomcat-net-02 (192.168.0.3) 56(84) bytes of data.
64 bytes from tomcat-net-02.mynet (192.168.0.3): icmp_seq=1 ttl=64 time=0.078 ms
64 bytes from tomcat-net-02.mynet (192.168.0.3): icmp_seq=2 ttl=64 time=0.083 ms
64 bytes from tomcat-net-02.mynet (192.168.0.3): icmp_seq=3 ttl=64 time=0.081 ms
64 bytes from tomcat-net-02.mynet (192.168.0.3): icmp_seq=4 ttl=64 time=0.079 ms
```

我们自定义的网络 dockers 都已经帮我们维护好了对应的关系，推荐我们平时这样使用网络！

好处：

redis - 不同的集群使用不同的网络，保证集群是安全和健康的

mysql - 不同的集群使用不同的网络，保证集群是安全和健康的



![image-20210310203404564](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210310203404564.png)



```shell
# 测试打通 tomcat01 - mynet
[root@xiaohong /]# docker network connect mynet tomcat01

# 连通之后就是将 tomcat01 放到了 mynet 网络下？

# 一个容器两个 IP 地址
# 阿里云服务器，公网 ip  私网 ip
```

![image-20210310203602436](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210310203602436.png)

```shell
# tomcat 01 连通2 ok
[root@xiaohong /]# docker exec -it tomcat01 ping tomcat-net-01
PING tomcat-net-01 (192.168.0.2) 56(84) bytes of data.
64 bytes from tomcat-net-01.mynet (192.168.0.2): icmp_seq=1 ttl=64 time=0.109 ms
64 bytes from tomcat-net-01.mynet (192.168.0.2): icmp_seq=2 ttl=64 time=0.085 ms

# tomcat02 依旧是打不通的
[root@xiaohong /]# docker exec -it tomcat02 ping tomcat-net-01
ping: tomcat-net-01: Name or service not known
```













