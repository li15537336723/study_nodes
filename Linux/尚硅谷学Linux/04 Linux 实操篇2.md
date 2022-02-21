# Linux 实操篇



## 一、组管理

在 Linux 中的每个用户必须属于一个组，不能独立于组外。在Linux中每个文件有所有者，所在组，其他组的概念

1）所有者

2）所在组

3）其他组

4）改变用户所在的组



### 1.1 文件/目录 所有者

一般为文件的创建者，谁创建了该文件，就自然的成为该文件的所有者



### 1.2 查看文件的所有者

1）指令：`ls -ahl`

2）应用实例：创建一个组 police，再创建一个用户 tom，然后使用 tom 来创建一个文件，看看情况如何

![image-20210318084622775](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210318084622775.png)

![image-20210318084717626](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210318084717626.png)

目前 ok.txt 的所有者是 tom。tom 在组 police1



### 1.3 修改文件的所有者

- 指令：`chown 用户名 文件名`

- 应用案例
  - 要求：使用 root 创建一个文件 apple.txt ，然后将其所有者修改成 tom

![image-20210318085212533](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210318085212533.png)



### 1.4 组的创建

#### 1.4.1 基本指令

groupadd 组名

#### 1.4.2 应用实例

创建一个组，monster

创建一个用户 fox，并放到 monster 组中

![image-20210318085558676](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210318085558676.png)



### 1.5 文件/目录 所在组

当某个用户创建了一个文件后，默认这个文件的所在组就是该用户所在的组。

#### 1.5.1 修改文件所在组

- 基本指令

  chgrp 组名 文件名

- 应用实例

  使用 root 用户创建文件 orange.txt，看看当前这个文件属于那个组，然后将这个文件所在组，修改到 police 组

  ![image-20210318090120403](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210318090120403.png)



### 1.6 改变用户所在组

在添加用户时,可以指定将该用户添加到那个组中,同样的用 root 的管理权限可以改变某个用户所在的组

#### 1.6.1 改变用户所在组

1)`usermod -g 组名 用户名`

2)`usermod -g 目录名 用户名` 改变该用户登陆的初始目录

#### 1.6.2 应用实例

创建一个土匪组，将 tom 这个用户从原来所在的 police 组，修改到 bandit 土匪组

![image-20210318090747362](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210318090747362.png)





## 二、权限管理

### 2.1 权限的基本介绍

ls -l 中显示的内容如下：

`-rwxrw-r-- 1 root root 1213 Feb 2 09:39 abc`

0-9 位说明

1）第 0 位确定文件类型（d, -, 1, c, b）

2）第 1-3 位确定所有者（该文件的所有者）拥有该文件的权限。---User

3）第 4-6 位确定所属组（同用户组）拥有该文件的权限，---Group

4）第 7-9 位确定其他用户拥有该文件的权限 		---Other

![image-20210318091817322](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210318091817322.png)

### 2.2 rwx 权限详解

#### 2.2.1 rwx 作用到文件

1）[r] 代表可读：可以读取，查看 

2）[w] 代表可写：可以修改，但是不代表可以删除文件，删除一个文件的前提条件是对该文件所在的目录有写权限，才能删除该文件

3）[x] 代表可执行：可以被执行



#### 2.2.2 rwx 作用到目录

1）[r] 代表可读：可以读取，ls查看目录内容

2）[w] 代表可写：可以修改，目录内创建 + 删除 + 重命名目录

3）[x] 代表可执行：可以进入到该目录



### 2.3 文件及目录权限实际案例



ls -l 中显示的内容如下：

`-rwxrw-r-- 1 root root 1213 Feb 2 09:39 abc`

10 个字符确定不同的用户能对文件干什么

第一个字符代表文件类型，文件（-），目录（d），链接(l)

其余字符每3个一组(rwx) 读(r)，写(w)，执行(x)

第一组rwx：文件拥有者的权限是读，写和执行

第二组rw-：与文件拥有者同一组的用户权限是读，写，但不能执行

第三组r--：不与文件拥有者同组的其他用户的权限是都不能写和执行

可用数字表示位：r=4,w=2,x=1 因此 rwx=4+2+1=7

1 			文件：硬连接数或 目录：子目录数

root		用户

root		组

1213		文件大小(字节)，如果是文件夹，显示 4096 字节

Feb 2 09:30 最后修改日期

abc		文件名



### 2.4 修改权限 -chmod

#### 2.4.1 基本说明：

通过 chmod 指令，可以修改问价或目录的权限

#### 2.4.2 方式一：+，-，= 变更权限

==**u：所有者	g：所有组	o：其他人	a：所有人**==

1）chmod	u=rwx,	g=rx,	o=x	文件目录名

2）chmod	o+w	文件目录名

3）chmod	a-x	文件目录名

- 案例演示

1）给 abc 文件的所有者**读写执行的权限**，在所在组 **读执行**权限，给其他组读执行权限

![image-20210318101152069](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210318101152069.png)



2）给abc文件的所有者除去执行的权限，增加组写的权限

![image-20210318104112128](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210318104112128.png)

![image-20210318104120900](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210318104120900.png)

3）给abc文件的所有用户添加读的权限

![image-20210318104037691](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210318104037691.png)



#### 2.4.3 方式二：通过数字变更权限

规则：r=4	w=2	x=1		rwx=4+2+1=7

`chmod u=rwx,g=rx,o=x`	文件目录名

相当于 `chmod 751` 文件目录名

- 案例演示

要求：将 /home/abc.txt 文件的权限修改成 **rwx** **==r-w==** **r-w**

rwx = 4+2+1=7

r-w = 4+1=5

r-w = 4+1=5

指令：`chmod 744 /home/etc.txt`



### 2.5 修改文件所有者 -chown

#### 2.5.1 基本介绍

`chown newowner file` 改变文件的所有者

`chown newowner:newgroup file` 改变用户的所有者和所有组

-R 如果是目录 则时期下所有子文件或目录递归生效

#### 2.5.2 案例演示：

1）将 /home/abc.txt 文件的所有者修改成 tom

![image-20210318105551119](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210318105551119.png)

2）请将 /home/kkk 目录下所有的文件和目录的所有者都修改成 tom

首先我们应该使用 root 用户操作

![image-20210318110017972](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210318110017972.png)



### 2.6 修改文件所在组 -chgrp

#### 2.6.1 基本介绍

`chgrp new group file` 	改变文件的所有组

#### 2.6.2 案例演示：

1）请将 /home/abc.txt	文件的所在组修改成 bandit (土匪)

`chgrp bandit /home/abc.txt`

2）请将 /home/kkk	目录下所有的文件和目录的所在组都修改成 bandit (土匪)

`chgrp -R bandit /home/kkk`

![image-20210318110650749](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210318110650749.png)



### 2.7 实践-警察和土匪游戏

police，bandit

jack，jerry：警察

xh，xq土匪

（1）创建组

（2）创建用户

```bash
[root@xiaohong home]# useradd -g police jack
[root@xiaohong home]# useradd -g police jerry
[root@xiaohong home]# useradd -g bandit xhh
[root@xiaohong home]# useradd -g bandit xqq
```

![](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210318112145458.png)

（3）jack 创建一个文件，自己可以读写，本组人可以读，其他组没人任何权限

![image-20210318112520323](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210318112520323.png)

（4）jack 修改该文件，让其它组人可以读，本组人可读写

![image-20210318112713623](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210318112713623.png)

（5）xhh 投靠警察，看看是否可以读写

1. 先使用 root 修改 xh 的组 `usermod -g police xhh`
2. 使用 jack 给他的 家目录 /home/jack 的所在组一个 rx 的权限

![image-20210318113852945](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210318113852945.png)

xhh 需要重新注销再到 jack 目录就可以操作 jack 的文件



![image-20210318114027696](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210318114027696.png)



![image-20210318114114208](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210318114114208.png)



## 三、crond 任务调度

### 3.1 原理示意图

![image-20210319163420572](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210319163420572.png)

crontab 进行 定时任务的设置

### 3.2 概述

任务调度：是指系统在某个时间执行的特定命令或程序

任务调度分类：

1. 系统工作：有些重要的工作必须周而复始的执行，比如病毒扫描等
2. 个别用户工作：个别用户可能希望执行某些程序，比如对 mysql 数据库进行备份

### 3.3 基本语法

crontab [选项]

#### 3.3.1 常用选项

**-e 						编辑 crontab 定时任务**

**-l						  查询 crontab 任务**

**-r					     删除当前用户所有的 crontab 任务**



### 3.4 快速入门

#### 3.4.1 任务的要求

设置任务调度文件：/etc/crontab

设置个人任务调度。执行 crontab -e 命令

接着输入任务到调度文件

如：`*/1 * * * * ls -l /ect/>tmp/to.txt` 命令



#### 3.4.2 步骤如下

1）cron -e

2）*/ 1 * * * ls -l /etc >>/tmp/to.txt

3）当保存退出后就程序

4）在每一分钟都会自动的调到 ls -l /etc >> /tmp/to.txt

参数说明

**5个占位符的说明**

![image-20210319170451610](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210319170451610.png)

![image-20210319170554884](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210319170554884.png)

![image-20210319170728847](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210319170728847.png)



### 3.5 任务调度的几个应用实例

案例1：每个1分钟，就将当前的日期信息，追加到 /tmp/mydate 文件中

1）先编写一个文件 mytask1.sh

​		`date >> /tmp/mydate`

2）给 mytask.sh  一个可以执行权限

​		`chmod 744 mytask1.sh`

3）crontab -e

4）* /1 * * * *  /home/mytask1.sh

案例2：每隔1分钟，将当前日期和日历都追加到 /home/mycal 文件中

1）先编写一个文件 mytask2.sh

​		`date >> /tmp/mycal`

​		`cal >> /tmp/mycal`

2）给 mytask2.sh  一个可以执行权限

​		`chmod 744 mytask1.sh`

3）crontab -e

4）* /1 * * * *  /home/mytask2.sh

案例3：每天凌晨 2:00 将mysql 数据库 testdb，备份到文件中



#### 3.6 cron 相关指令

1）crontab -r：指令任务调度

2）crontab -l：列出当前有那些任务调度

3）service crond restart	【重启任务调度】



## 四、磁盘分区

### 4.1 分区基础知识

#### 4.1.1 分区的方式

1）mbr 分区：

1. 最多支持四个主分区
2. 系统只能安装在分区
3. 扩展分区要占一个主分区
4. MBR 最大只支持 2TB，但拥有最好的兼容性

2）gtp 分区

1. 支持午先多个主分区（但操作系统可能限制，比如 windows 下最多 128 个分区）
2. 最大支持 18EB 的大容量（1EB=1024PB，1PB=1024TB）
3. windows7 64位以后支持 gtp



### 4.2 Iinux分区

#### 4.2.1 原理介绍

1）Linux 来说无论有几个分区，分给那一目录使用，他归根结底就只有一个根目录，也给独立且唯一的文件结构，Linux 中每个分区都是用来组成整个文件系统的一部分

2）Linux 采用了一种叫”载入“的处理方法，他的整个文件系统包含了一整套的文件和目录，且将一个分区和一个目录联系起来，这时要载入的一个分区将使它的存储空间在也给目录下获得

3）示意图

![image-20210320093126480](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210320093126480.png)





#### 4.2.2 使用 lsblk -f 指令查看当前系统的分区情况![image-20210320093602662](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210320093602662.png)



![image-20210320093901611](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210320093901611.png)



### 4.3 磁盘情况查询

#### 4.3.1 查询系统整体磁盘使用情况

基本语法

​	`df -h`

应用实例

查询系统整体磁盘使用情况

![image-20210320100115315](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210320100115315.png)



#### 4.3.2 查询指定目录的磁盘占用情况

- 基本语法

  du -h  /目录

  查询指定目录的磁盘占用情况，默认为当前目录

  -s 指定目录占用大小汇总

  -h 带计量单位

  -a 含文件

  -- max-depth = 1 子目录深度

  -c 列出明细的同时，增加汇总值

### 4.4 磁盘情况-工作实用指

1）统计 /home 文件夹下文件的个数

![image-20210320100958153](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210320100958153.png)

2）统计 /home 文件夹下目录的个数

![image-20210320101015548](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210320101015548.png)

3）统计 /home 文件夹下文件的个数，包括子文件夹里的

![image-20210320101102031](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210320101102031.png)

4）统计文件夹下目录的个数，包括子文件夹里的

![image-20210320101048835](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210320101048835.png)

5）以树状显示目录结构





## 五、进程管理

### 5.1 进程的基本介绍

1）在 Linux 中，每个执行的程序（代码）都称为一个进程。每一个进程都分配一个ID号。

2）每一个进程，都会对应一个父进程，而这个父进程可以复制多个子进程。例如 www 服务器。

3）每个进程都可能以两种方式存在的。前台与后台，所谓前台进程就是用户目前的屏幕上可以进行操作的。后台进程则是实际在操作，但由于屏幕上无法看到的进行，通常使用后台方式执行。

4）一般系统的服务都是可以后台进程的方式存在，而且都会驻在系统中。直到关机才结束



### 5.2 显示系统执行的进程

说明：查看进程使用的指令 `ps ` 一般来说使用的参数时 ps -aux

![image-20210320142836175](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210320142836175.png)



![image-20210320143846448](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210320143846448.png)



#### 5.2.1 PS 指令详解

1）指令：`ps -aux | grep xxx`，比如我看有没有 sshd 服务

2）指令说明

- System V 展示风格
- USER：用户名称
- PID：进程号
- %CPU：进程占用 CPU 的百分比
- %MEM：进程占用物理内存的百分比
- VSZ：进程占用的虚拟内存大小（单位：KB）
- RSS：进程占用物理的内存大小（单位：KB）
- TT：终端名称，缩写
- STAT：进程状态，其中 s-睡眠，s-表示该进程是会话的先导进程，N-表示进程拥有比普通优先级更低的优先级，R-正在运行，D-短期等待，Z-僵死进程，T-被跟踪或者被停止等
- STARTED：进程的启动时间
- TIME：CPU时间，即进程使用 CPU 的总时间
- COMMAND：启动进程所用的命令和参数，如果过长会被截断





### 5.3 终止进程 kill 和 killall

#### 5.3.1 介绍

若是某个进程执行一把你需要停止时，或是已消了很大的系统资源时，此时可以考虑停止该进程。使用 kill 命令来完成此项任务

#### 5.3.2 基本语法

kill [选项] 进程号 （功能描述：通过进程号杀死进程）

killall 进程名称（功能描述：通过进程名杀死进程，也支持通配符，在这系统因负载过大而变得很慢时很有用）

#### 5.3.3 常用选项

-9 表示强制进程立即停止

#### 5.3.4 最佳实践

案例1：踢掉某个用户非法登陆

![image-20210320145433118](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210320145433118.png)

案例2：终止远程登陆服务 sshd，在适当时候再次重启 sshd 服务

![image-20210320150047554](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210320150047554.png)

案例3：终止多个 gedit 编辑器【killall】



案例4：强制杀掉一个终端



### 5.4 服务(Service)管理

#### 5.4.1 介绍

服务(service) 本质就是进程，但是是运行在后台的，通常都会监听某个端口，等待其他程序的请求，比如(mysql，sshd，防火墙等)。因此我们又称为守护进程，是 Linux 中非常重要的知识点【原理图】

![image-20210320151015359](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210320151015359.png)



#### 5.4.2 service 管理指令

service 服务名 [start | stop | restart | reload | status]

在 CentOS7.0后，不再使用 service，而是 systemctl

#### 5.4.3 使用案例

**开启防火墙**

`systemctl start firewalld`

**查看防火墙的状态**

`systemctl status firewalld`

**关闭防火墙**

`systemctl stop firewalld`



### 5.5 动态监控进程

#### 5.5.1 介绍 

top 与 ps 命令很相似。它们都用来显示正在执行的进程。Top 与 ps 最大的不同之处，在于 top 在执行一段时间可以更新正在运行的进程

#### 5.5.2 基本语法

top[选项]

#### 5.5.3 选项说明

选项									功能

-d 秒数					指定 top 命令每个几秒更新，默认是 3 秒在 top 命令的交互模式当中可以执行的命令

-i							  使 top 不显示任何闲置或者僵死进程

-p							 通过指定监控进程 ID 来仅仅监控某个进程的状态



==交互操作说明==

操作						功能

p						以 CPU 使用率排序，默认就是此项

M					   以内存的使用率排序

N					   以 PID 排序

q					   退出 top



## 六、RPM和YUM包

### 6.1 介绍

一种用于互联网下载包的打包及安装工具



卸载 rpm 包：

- 基本语法

  rpm -e RPM 包的名称

- 应用案例

  1）删除 firefox 软件包

`rpm -r firefox `





### Yum 包管理

#### 介绍

Yum 是一个 shell 前端软件包管理，基于 RPM 包管理，能够从指定的服务器自动下载 RPM 包并且安装，可以自动处理依赖性关系，并且一次安装所有依赖的软件包，需要联网

yum 的基本指令

- 查询 yum 服务器是否需要安装的软件

  `yum list | grep xx` 软件列表

- 安装指定的 yum 包

  `yum install xxx` 下载安装



#### yum 应用实例

案例：请使用 yum 的方式安装 firefox 

`yum install firefox`











