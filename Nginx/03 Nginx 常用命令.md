### Nginx 操作的常用命令

#### 1、使用 nginx 操作命令前提条件：必须进入 nginx 的目录

`/usr/sbin`

#### 2、查看 nginx 版本号

`./ngxin -v`

![image-20210312094449837](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210312094449837.png)

#### 3、启动 nginx

`./nginx`

![image-20210312094832068](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210312094832068.png)

#### 4、关闭 nginx

`./nginx -s stop`

![image-20210312094718797](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210312094718797.png)



#### 5、重加载 nginx

`./nginx -s reload`

![image-20210312095355319](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210312095355319.png)











































```shell
#user  nobody;
worker_processes  1;
events {
    worker_connections  1024;
}
http {
    include       mime.types;
    default_type  application/octet-stream;
    sendfile        on;
    keepalive_timeout  65;
    server {
        listen       80;
        server_name  localhost;

        location / {
            root   /usr/local/nginx/html;
            try_files $uri $uri/ /index.html last;
            index  index.html index.htm;
        }
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }
    }
}

```













