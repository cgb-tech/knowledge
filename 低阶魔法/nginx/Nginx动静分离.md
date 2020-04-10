## Nginx动静分离

动静分离,通过中间件将动态请求和静态请求进行分离, 分离资源, 减少不必要的请求消耗, 减少请求延时。
 好处: 动静分离后, 即使动态服务不可用, 但静态资源不会受到影响

通过中间件将动态请求和静态请求分离

```nginx
[root@Nginx conf.d]# cat access.conf 
server{
        listen 80;
        root /soft/code;
        index index.html;

        location ~ .*\.(png|jpg|gif)$ {
                gzip on;
                root /soft/code/images;
        }
}

//准备目录, 以及静态相关图片
[root@Nginx ~]# wget -O /soft/code/images/nginx.png http://nginx.org/nginx.png
```

```nginx
[root@Nginx ~]# wget -O /soft/package/tomcat9.tar.gz \
http://mirror.bit.edu.cn/apache/tomcat/tomcat-9/v9.0.7/bin/apache-tomcat-9.0.7.tar.gz
[root@Nginx ~]# mkdir /soft/app
[root@Nginx ~]# tar xf /soft/package/tomcat9.tar.gz -C /soft/app/

[root@Nginx ~]# vim /soft/app/apache-tomcat-9.0.7/webapps/ROOT/java_test.jsp
<%@ page language="java" import="java.util.*" pageEncoding="utf-8"%>
<HTML>
    <HEAD>
        <TITLE>JSP Test Page</TITLE>
    </HEAD>
    <BODY>
      <%
        Random rand = new Random();
        out.println("<h1>Random number:</h1>");
        out.println(rand.nextInt(99)+100);
      %>
    </BODY>
</HTML>
```

```nginx
upstream static {
        server 192.168.69.113:80;
}

upstream java {
        server 192.168.69.113:8080;
}

server {
        listen 80;
        server_name 192.168.69.112;

        location / {
                root /soft/code;
                index index.html;
        }

        location ~ .*\.(png|jpg|gif)$ {
                proxy_pass http://static;
                include proxy_params;
        }

        location  ~ .*\.jsp$ {
                proxy_pass  http://java;
                include proxy_params;
        }

}
```

```nginx
[root@Nginx ~]# cat /soft/code/mysite.html 
<html lang="en">
<head>
        <meta charset="UTF-8" />
        <title>测试ajax和跨域访问</title>
        <script src="http://libs.baidu.com/jquery/2.1.4/jquery.min.js"></script>
</head>
<script type="text/javascript">
$(document).ready(function(){
        $.ajax({
        type: "GET",
        url: "http://192.168.69.112/java_test.jsp",
        success: function(data) {
                $("#get_data").html(data)
        },
        error: function() {
                alert("fail!!,请刷新再试!");
        }
        });
});
</script>
        <body>
                <h1>测试动静分离</h1>
                <img src="http://192.168.69.112/nginx.png">
                <div id="get_data"></div>
        </body>
</html>
```

当停止`Nginx`后, 强制刷新页面会发现静态内容无法访问, 动态内容依旧运行正常

当停止`tomcat`后, 静态内容依旧能正常访问, 动态内容将不会被请求到

## Nginx手机电脑应用案例

```nginx
http {
...
    upstream firefox {
        server 172.31.57.133:80;
    }
    upstream chrome {
        server 172.31.57.133:8080;
    }
    upstream iphone {
        server 172.31.57.134:8080;
    }
    upstream android {
        server 172.31.57.134:8081;
    }
    upstream default {
        server 172.31.57.134:80;
    }
...
}

//server根据判断来访问不同的页面
server {
    listen       80;
    server_name  www.xuliangwei.com;

    #safari浏览器访问的效果
    location / {
        if ($http_user_agent ~* "Safari"){
        proxy_pass http://dynamic_pools;
        }     
    #firefox浏览器访问效果
        if ($http_user_agent ~* "Firefox"){
        proxy_pass http://static_pools;
        }
    #chrome浏览器访问效果
        if ($http_user_agent ~* "Chrome"){
        proxy_pass http://chrome;
        } 
        
    #iphone手机访问效果
        if ($http_user_agent ~* "iphone"){
        proxy_pass http://iphone;
        }
    
    #android手机访问效果
        if ($http_user_agent ~* "android"){
        proxy_pass http://and;
        }
    
    #其他浏览器访问默认规则
        proxy_pass http://dynamic_pools;
        include proxy.conf;
        }
    }
}
```

根据访问不同目录, 代理不同的服务器

```nginx
//默认动态，静态直接找设置的static，上传找upload
    upstream static_pools {
        server 10.0.0.9:80  weight=1;
    }
   upstream upload_pools {
         server 10.0.0.10:80  weight=1;
    }
   upstream default_pools {
         server 10.0.0.9:8080  weight=1;
   }
 
    server {
        listen       80;
        server_name  www.xuliangwei.com;
 
#url: https://www.xuliangwei.com/
    location / { 
        proxy_pass http://default_pools;
        include proxy.conf;
    }
 
#url: https://www.xuliangwei.com/static/
    location /static/ {
        proxy_pass http://static_pools;
        include proxy.conf;
    }
 
#url: https://www.xuliangwei.com/upload/
    location /upload/ {
        proxy_pass http://upload_pools;
        include proxy.conf;
    }
}

//方案2：以if语句实现。
if ($request_uri   ~*   "^/static/(.*)$")
{
        proxy_pass http://static_pools/$1;
}
if ($request_uri   ~*   "^/upload/(.*)$")
{
        proxy_pass http://upload_pools/$1;
}
location / { 
    proxy_pass http://default_pools;
    include proxy.conf;
}
```