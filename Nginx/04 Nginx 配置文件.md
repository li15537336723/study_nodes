### Nginx 配置文件

#### 第一部分：全局块

从配置文件开始到 events 之间的内容，主要会设置一些影响 nginx 服务整体运行的配置指令，主要包括配置运行 Nginx 服务器的用户组，允许生成的 worker process 数，进程 PID 存放路径、日志存放路径和类型以及配置文件的引入等。

`worker_processes 1;`

这是 Nginx 服务器并发处理服务的关键配置，worker_processes 值越大，可以支持的并发处理量也越多，但是会受到硬件软件设备的制约



#### 第二部分：events 块

events 块涉及的指令主要影响 Nginx 服务器与用户的网络连接

比如 **worker_connections 1024**;支持的最大连接数



#### 第三部分：http 块

Nginx 服务器配中最频繁的部分

http 块也可以包括 **http 全局块、server 块**







