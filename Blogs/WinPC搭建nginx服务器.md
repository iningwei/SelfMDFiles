[官网](http://nginx.org/en/download.html)下载Windows稳定版包，解压后，双击nginx.exe启动nginx。

也可以命令行CD到nginx.exe所在目录后，执行：start nginx来启动服务器。


运行tasklist可以查看当前会话信息，看到如下信息说明启动正常。
```c
E:\Softwares\nginx-1.22.1\nginx-1.22.1>tasklist

映像名称                       PID 会话名              会话#       内存使用
========================= ======== ================ =========== ============
nginx.exe                    41136 Console                    4      7,100 K
nginx.exe                     6100 Console                    4      7,456 K
```
这两个进程中一个是主进程，一个是工作进程，如果nginx没有启动，可以到logs/error.log下面找原因。


此时在浏览器输入localhost可以看到nginx默认页面（默认启动页为 html/index.html）的展示。

## 修改端口
nginx默认的端口是80端口，可以在conf/nginx.conf中修改
```c
http {
    include       mime.types;
    default_type  application/octet-stream;

    #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
    #                  '$status $body_bytes_sent "$http_referer" '
    #                  '"$http_user_agent" "$http_x_forwarded_for"';

    #access_log  logs/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    #keepalive_timeout  0;
    keepalive_timeout  65;

    #gzip  on;

    server {
        listen       80;
        server_name  localhost;
```
修改端口后要使生效可以再次执行nginx.exe，或者使用命令：``nginx -s reload``,重启工程，使配置文件生效。
经常会遇到浏览器刷新不起作用，可能是缓存引起的问题。测试的的时候通过修改端口跳过该问题。

## 设置项目
项目所用资源放到html目录下，并调整好启动页面即可。
## 常用命令
```c
nginx -s stop 强制关闭
nginx -s quit 安全关闭
nginx -s reload 改变配置文件的时候，重启nginx工作进程，来时配置文件生效
nginx -s reopen 打开日志文件
```

