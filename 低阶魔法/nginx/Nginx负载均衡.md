## Nginx负载均衡配置场景

`Nginx`实现负载均衡用到了`proxy_pass`代理模块核心配置, 将客户端请求代理转发至一组`upstream`虚拟服务池

`Nginx upstream`虚拟配置语法

```nginx
Syntax: upstream name { ... }
Default: -
Context: http

//upstream例子
upstream backend {
    server backend1.example.com       weight=5;
    server backend2.example.com:8080;
    server unix:/tmp/backend3;
    server backup1.example.com:8080   backup;
}
server {
    location / {
        proxy_pass http://backend;
    }
}
```

1.创建对应`html`文件

```nginx
[root@Nginx ~]# mkdir /soft/{code1,code2,code3} -p
[root@Nginx ~]# cat /soft/code1/index.html
<html>
        <title> Code1</title>
        <body bgcolor="red">
                <h1> Code1-8081 </h1>
        </body>
</html>

[root@Nginx ~]# cat /soft/code2/index.html
<html>
        <title> Coder2</title>
        <body bgcolor="blue">
                <h1> Code1-8082</h1>
        </body>
</html>

[root@Nginx ~]# cat /soft/code3/index.html
<html>
        <title> Coder3</title>
        <body bgcolor="green">
                <h1> Code1-8083</h1>
        </body>
</html>
```

2.建立对应的`releserver.conf`配置文件

```nginx
[root@Nginx ~]# cat /etc/nginx/conf.d/releserver.conf 
server {
    listen 8081;
    root /soft/code1;
    index index.html;
}

server {
    listen 8082;
    root /soft/code2;
    index index.html;
}

server {
    listen 8083;
    root /soft/code3;
    index index.html;
}
```

3.配置`Nginx`反向代理

```nginx
[root@Nginx ~]# cat /etc/nginx/conf.d/proxy.conf 
upstream node {
    server 192.168.69.113:8081;
    server 192.168.69.113:8082;
    server 192.168.69.113:8083;
}

server {
    server_name 192.168.69.113;
    listen 80;
    location / {
        proxy_pass http://node;
        include proxy_params;
    }
}
```

## Nginx负载均衡状态配置

后端服务器在负载均衡调度中的状态

| 状态         | 概述                              |
| ------------ | --------------------------------- |
| down         | 当前的server暂时不参与负载均衡    |
| backup       | 预留的备份服务器                  |
| max_fails    | 允许请求失败的次数                |
| fail_timeout | 经过max_fails失败后, 服务暂停时间 |
| max_conns    | 限制最大的接收连接数              |

测试`backup`以及`down`状态

```nginx
upstream load_pass {
    server 192.168.56.11:8001 down;
    server 192.168.56.12:8002 backup;
    server 192.168.56.13:8003 max_fails=1 fail_timeout=10s;
}

location  / {
    proxy_pass http://load_pass;
    include proxy_params;
}

//关闭8003测试
```

## Nginx负载均衡调度策略

| 调度算法     | 概述                                                         |
| ------------ | ------------------------------------------------------------ |
| 轮询         | 按时间顺序逐一分配到不同的后端服务器(默认)                   |
| weight       | 加权轮询,weight值越大,分配到的访问几率越高                   |
| ip_hash      | 每个请求按访问IP的hash结果分配,这样来自同一IP的固定访问一个后端服务器 |
| url_hash     | 按照访问URL的hash结果来分配请求,是每个URL定向到同一个后端服务器 |
| least_conn   | 最少链接数,那个机器链接数少就分发                            |
| hash关键数值 | hash自定义的key                                              |

Nginx负载均衡权重轮询具体配置

```nginx
upstream load_pass {
    server 192.168.56.11:8001;
    server 192.168.56.12:8002 weight=5;
    server 192.168.56.13:8003;
}
```

Nginx负载均衡`ip_hash`具体配置

```nginx
//如果客户端都走相同代理, 会导致某一台服务器连接过多
upstream load_pass {
    ip_hash;
    server 192.168.56.11:8001;
    server 192.168.56.12:8002;
    server 192.168.56.13:8003;
}
//如果出现通过代理访问会影响后端节点接收状态均衡
```

Nginx负载均衡url_hash具体配置

```nginx
upstream load_pass {
    hash $request_uri;
    server 192.168.56.11:8001;
    server 192.168.56.12:8002;
    server 192.168.56.13:8003;
}

//针对三台服务器添加相同文件
/soft/code1/url1.html url2.html url3.html
/soft/code2/url1.html url2.html url3.html
/soft/code3/url1.html url2.html url3.html
```

## Nginx负载均衡TCP配置

`Nginx`四层代理仅能存在于`main`段

```nginx
stream {
        upstream ssh_proxy {
                hash $remote_addr consistent;
                server 192.168.56.103:22;
        }
        upstream mysql_proxy {
                hash $remote_addr consistent;
                server 192.168.56.103:3306;
        }
    server {
        listen 6666;
        proxy_connect_timeout 1s;
        proxy_timeout 300s;
        proxy_pass ssh_proxy;
    }
    server {
        listen 5555;
        proxy_connect_timeout 1s;
        proxy_timeout 300s;
        proxy_pass mysql_proxy;
    }
}
```