### nginx 配置文件，参数说明
```
# 每个指令必须以分号结束

#配置用户或者组，默认为nobody nobody
#user  nobody;

#启动进程,通常设置成和cpu的数量相等，默认为1
worker_processes  1;

#制定日志路径，级别。这个设置可以放入全局块，http块，server块，级别依次为：debug|info|notice|warn|error|crit|alert|emerg
#error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;

#pid        logs/nginx.pid;

events {
    #最大连接数，默认为512
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
    
    #允许sendfile方式传输文件，默认为off，可以在http块，server块，location块。
    sendfile        on;
    
    #tcp_nopush     on;
    
    #连接超时时间，默认为75s，可以在http，server，location块。
    #keepalive_timeout  0;
    keepalive_timeout  65;

    #开启gzip压缩
    #gzip  on;
    #gzip_disable "MSIE [1-6]\.(?!.*SV1)";

    server {
        #监听端口
        listen       8080;
        
        #监听地址
        server_name  localhost;

        #charset koi8-r;

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