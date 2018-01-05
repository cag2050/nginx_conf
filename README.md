### nginx 配置文件，参数说明

* Nginx配置文件主要分为4部分：main（全局设置）、server（主机设置）、upstream（负载均衡服务器设置）和 location（URL匹配特定位置的设置）。  
main部分设置的指令将影响其他所有设置；server部分的指令主要用于指定主机和端口；upstream指令主要用于负载均衡，设置一系列的后端服务器；location部分用于匹配网页位置。  
这四者之间的关系如下：server继承main，location继承server，upstream既不会继承其他设置也不会被继承。

```
# 每个指令必须以分号结束

#配置用户或者组，默认为nobody nobody；windows下不指定
# 有时候会报错误：open() "/data/www/1.txt" failed (13: Permission denied)，一种简单解决办法是配置：user root;
#user nobody nobody;

#开启的线程数，一般跟逻辑CPU核数一致，默认为1
worker_processes  1;

#制定日志路径，级别。这个设置可以放入全局块，http块，server块，级别依次为：debug|info|notice|warn|error|crit|alert|emerg
#error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;

#指定进程id的存储文件位置
#pid        logs/nginx.pid;

events {
    #参考事件模型，use [ kqueue | rtsig | epoll | /dev/poll | select | poll ]; 
    #epoll模型是Linux 2.6以上版本内核中的高性能网络I/O模型，如果跑在FreeBSD上面，就用kqueue模型。
    use epoll;
    
    #每个进程的最大连接数,受系统进程的最大打开文件数量限制。
    #理论上每台nginx服务器的最大连接数为：worker_processes * worker_connections
    worker_connections  1024;
}

http {
    #文件扩展名与文件类型映射表
    include       mime.types;
    
    #默认文件类型，默认为text/plain
    default_type  application/octet-stream;
    
    #自定义日志格式
    #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
    #                  '$status $body_bytes_sent "$http_referer" '
    #                  '"$http_user_agent" "$http_x_forwarded_for"';
    
    #combined为日志格式的默认值
    #access_log  logs/access.log  main;
    
    #开启高效文件传输模式，默认为off，可以在http块，server块，location块。
    sendfile        on;
    
    #是否在FreeBSD上使用TCP_NOPUSH套接字选项，在Linux上使用TCP_CORK套接字选项。
    #选项仅在使用sendfile的时候才开启。
    #开启此选项允许：
    #1.在Linux和FreeBSD 4.*上将响应头和正文的开始部分一起发送；
    #2.一次性发送整个文件。
    #tcp_nopush     on;
    
    #仅在将连接转变为长连接、在upstream发送响应到客户端时才被启用。
    #tcp_nodelay    on;
    
    #客户端连接超时时间，默认为75s，可以在http，server，location块。
    #keepalive_timeout  0;
    keepalive_timeout  65;

    #开启gzip压缩
    #gzip  on;
    #gzip_disable "MSIE [1-6]\.(?!.*SV1)";

    server {
        #监听端口
        listen       8080;
        
        #主机域名，域名可以有多个，用空格隔开
        server_name  localhost;

        #默认编码
        #charset utf-8;

        #设定本虚拟主机的访问日志
        #access_log  logs/host.access.log;

        location / {
            #根目录
            root   /Users/cag/Documents/mp_query/dist;
            
            #默认页
            #index  index.html index.htm;
            
            #使用vue-router时，配置此项。详见：https://router.vuejs.org/zh-cn/essentials/history-mode.html
            try_files $uri $uri/ /index.html;
        }

        #error_page  404              /404.html;

        # redirect server error pages to the static page /50x.html
        #
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }

        # proxy the PHP scripts to Apache listening on 127.0.0.1:80
        #
        #location ~ \.php$ {
        #    proxy_pass   http://127.0.0.1;
        #}

        # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
        #
        #location ~ \.php$ {
        #    root           html;
        #    fastcgi_pass   127.0.0.1:9000;
        #    fastcgi_index  index.php;
        #    fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
        #    include        fastcgi_params;
        #}

        # deny access to .htaccess files, if Apache's document root
        # concurs with nginx's one
        #
        #location ~ /\.ht {
        #    deny  all;
        #}
    }


    # another virtual host using mix of IP-, name-, and port-based configuration
    #
    #server {
    #    listen       8000;
    #    listen       somename:8080;
    #    server_name  somename  alias  another.alias;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}


    # HTTPS server
    #
    #server {
    #    listen       443 ssl;
    #    server_name  localhost;

    #    ssl_certificate      cert.pem;
    #    ssl_certificate_key  cert.key;

    #    ssl_session_cache    shared:SSL:1m;
    #    ssl_session_timeout  5m;

    #    ssl_ciphers  HIGH:!aNULL:!MD5;
    #    ssl_prefer_server_ciphers  on;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}
    include servers/*;
}
```
