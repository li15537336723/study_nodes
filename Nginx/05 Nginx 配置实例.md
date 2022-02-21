# Nginx 配置实例



## Nginx 配置实例1——反向代理

#### 1、实现效果

（1）打开浏览器，在浏览器地址栏中输入 www.123.com , 跳转 linux 系统 tomcat 主页面中

#### 2、准备工作

##### （1）在linux系统安装tomcat，使用默认端口 8080 

- tomcat 安装文件放入到 linux 系统中，解压
- 进入tomcat的bin目录中，./startup.sh 启动 tomcat 服务器



##### （2）对外开放访问的端口

`firewall-cmd --add-port=8080/tcp --permanent`

`firewall-cmd --reload`

**开启防火墙**

`systemctl start firewalld`

**查看防火墙的状态**

`systemctl status firewalld`

**关闭防火墙**

`systemctl stop firewalld`

##### (3)在 window 系统下访问 tomcat 服务器

![image-20210312111350002](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210312111350002.png)



### 3、访问过程分析

![image-20210312111821576](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210312111821576.png)





### 4、具体配置

**第一步** 在 window 系统的host 文件进行域名和 ip 对应关系的配置

![](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210312112056855.png)

添加内容在 host 文件中

![image-20210312113141203](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210312113141203.png)

http://39.102.129.20/		www.123.com





**第二步** 在nginx 进行请求转发的配置（反向代理配置）

![image-20210312114158324](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210312114158324.png)

### 5、最终测试

![image-20210312145405847](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210312145405847.png)







## Nginx 配置实例2——反向代理

### 1、实现效果

使用 nginx 反向代理，根据访问的路径跳转到不同的端口的服务中

nginx 监听端口 9001

访问 http://127.0.0.1:9001/edu/ 直接跳转到 **127.0.0.1:8080**

访问 http://127.0.0.1:9001/vod/ 直接跳转到 **127.0.0.1:8081**



### 2、准备工作

(1)准备两个 tomcat 服务器，一个 8080 端口，一个 8081 端口

(2)创建文件夹和测试页面

### 3、具体配置

（1）找到 nginx 配置文件，进行反向代理配置

![image-20210312155803739](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210312155803739.png)

（2）开放对外开放的端口号











## Nginx 配置实例-负载均衡

### 1、实现效果

（1）浏览器地址栏输入地址 http://39.102.129.20:9001/edu/a.html ，负载均衡效果，平均 8080 和 8081 端口中

### 2、准备工作

（1）准备两台 tomcat 服务器，一台 8080，一台 8081

（2）在两台 tomcat 里面 webapps 目录中，创建名称是 edu 文件夹，在 edu 文件夹中创建页面 a.html ，用于测试

### 3、在 nginx 的配置文件中进行负载均衡的配置

![image-20210312163838641](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210312163838641.png)



### 4、nginx 分配服务器策略

**第一种 轮询（默认）**

每个请求按时间顺序逐一分配到不同的后端服务器，如果后端服务器 down 掉，能自动删除

**第二种 weight**

weight 代表权重默认为 1, 权重越高被分配的客户端越多





## Nginx 配置实例-动静分离

Nginx 动静分离简单来说就是把动态跟静态请求分开，不能理解成只是单纯的把动态页面和静态页面物理分离，严格意义上说应该是动态请求跟静态请求分开.

![image-20210312164837776](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210312164837776.png)





## Nginx 高可用的集群





















