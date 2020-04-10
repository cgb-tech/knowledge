## 0 查看日志

`tail -f /var/log/nginx/access.log`

## 1 Nginx代理配置语法

1.`Nginx`代理配置语法

```nginx
Syntax: proxy_pass URL;
Default:    —
Context:    location, if in location, limit_except

http://localhost:8000/uri/
http://192.168.56.11:8000/uri/
http://unix:/tmp/backend.socket:/uri/
```

2.类似于`nopush`缓冲区

```nginx
//尽可能收集所有头请求, 
Syntax: proxy_buffering on | off;
Default:    
proxy_buffering on;
Context:    http, server, location

//扩展:
proxy_buffer_size 
proxy_buffers 
proxy_busy_buffer_size
```

3.跳转重定向 

```nginx
Syntax: proxy_redirect default;
proxy_redirect off;proxy_redirect redirect replacement;
Default:    proxy_redirect default;
Context:    http, server, location
```

4.头信息

```nginx
Syntax: proxy_set_header field value;
Default:    proxy_set_header Host $proxy_host;
            proxy_set_header Connection close;
Context:    http, server, location

//扩展: 
proxy_hide_header
proxy_set_body
```

5.代理到后端的`TCP`连接超时

```nginx
Syntax: proxy_connect_timeout time;
Default: proxy_connect_timeout 60s;
Context: http, server, location

//扩展
proxy_read_timeout  //以及建立
proxy_send_timeout  //服务端请求完, 发送给客户端时间
```

6.`Proxy`常见配置项具体配置如下

```nginx
[root@Nginx ~]# vim /etc/nginx/proxy_params
proxy_redirect default;
proxy_set_header Host $http_host;
proxy_set_header X-Real-IP $remote_addr;
proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;

proxy_connect_timeout 30;
proxy_send_timeout 60;
proxy_read_timeout 60;

proxy_buffer_size 32k;
proxy_buffering on;
proxy_buffers 4 128k;
proxy_busy_buffers_size 256k;
proxy_max_temp_file_size 256k;

//具体location实现
location / {
    proxy_pass http://127.0.0.1:8080;
    include proxy_params;
}
```

## 2 Nginx正向代理示例

`Nginx`正向代理配置实例

```nginx
//配置69.113访问限制,仅允许同网段访问
location ~ .*\.(jpg|gif|png)$ {
    allow 192.168.69.0/24;
    deny all;
    root /soft/code/images;
}

//配置正向代理
[root@Nginx ~]# cat /etc/nginx/conf.d/zy_proxy.conf 
server {
    listen       80;

    resolver 233.5.5.5;
    location / {
        proxy_pass http://$http_host$request_uri;
        proxy_set_header Host $http_host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}

//客户端使用SwitchySharp浏览器插件配置正向代理
```

## 3 Nginx反向代理示例

```nginx
/proxy代理
[root@proxy ~]# cat /etc/nginx/conf.d/proxy.conf
server {
    listen 80;
    server_name nginx.bjstack.com;
    index index.html;

    location / {
    proxy_pass http:/36.93.11.255:8001;
    include proxy_params;
    }
}

//WEB站点
[root@Nginx ~]# cat /etc/nginx/conf.d/images.conf
server {
    listen 80;
    server_name nginx.bjstack.com;
    root /soft/code;

    location / {
        root /soft/code;
        index index.html;
    }

    location ~ .*\.(png|jpg|gif)$ {
    gzip on;
    root /soft/code/images;
    }
}
```

 