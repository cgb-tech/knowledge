# nginx常用操作



## 1.第一次启动nginx需要指定配置文件位置

~~~
nginx -c /etc/nginx/nginx.conf
~~~



## 2.打印访问日志

~~~
tail -f /var/log/nginx/access.log
~~~



## 3.查找主配置文件

~~~
ps -ef | grep nginx
~~~



## 4.配置文件分析（注意分号结尾）

~~~yml
user  nginx;
#工作进程，配置与cpu个数保持一致
worker_processes  1;

#日志打印级别，现在是warn
error_log  /var/log/nginx/error.log warn;
#服务器启动后的pid
pid        /var/run/nginx.pid;

#事件模块
events {
	#每个worker支持的请求数量
    worker_connections  1024;
    #内核模型(select,poll,epoll)
    use epool;
}

#非虚拟主机的配置
http {
    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;
	
	#配置用户访问日志需要记录那些信息
    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /var/log/nginx/access.log  main;
		
		#虚拟主机的配置，每个虚拟主机配置一个server
        server{
        		#监听端口，默认80
        		listen 80；
        		#提供服务的域名或者主机名
                server_name www.xiajibagao.top;
                #'/'后面可以匹配规则，比如location /1.html{}
                location / {
                	#网站的存放路径
                	root /soft/code/www;
                	#默认访问的首页 
                	index index.html;
                }
                #错误页面统一定位
                error_page 500 502 503 504 /50x.html
                #错误代码重新定向到新的location
                location = /50x.html{
                	root html
                }
                
                #include可以在当前标签内引用外部配置文件，等同于直接写在include所在位置
                include /usr/local/nginx/conf/vhost/*;
        }

    sendfile        on;
    #tcp_nopush     on;

    keepalive_timeout  65;

    #gzip  on;
    

"/etc/nginx/nginx.conf" 38L, 735C   
~~~



## 5.测试运行nginx的配置文件

~~~yml
#一次启动nginx需要指定配置文件位置
nginx -c /etc/nginx/nginx.conf

#若在默认目录则不需要指定位置
nginx -t

#重新加载配置文件
nginx -s reload/restart
~~~



## 6.查看对部署网站的请求和连接过程的数据

~~~
[root@iZwz9ev02los7q1d71e7muZ nginx]# curl -v xiajibagao.top
~~~



## 7.开启访问日志页面

### 7.1 配置页面

~~~yml
#在server下配置，然后在地址栏访问网址/mystatus即可
location /mystatus{
        stub_status on;
        access_log off;
}

~~~

### 7.2 页面信息

以下为访问的http://xiajibagao.top/mystatus页面的展示信息

~~~yml
#当前nginx活跃的连接数
Active connections: 2 

#server表示启动到现在处理的总连接数
#accepts表示启动到现在一共建立了几次握手
#另外，总连接数减去握手数则为请求丢失数
#handled requests表示总共处理的请求数
server accepts handled requests
 18 18 17 

#reading表示读取到客户端header信息数
#writing表示返回给客户端的header信息数
#waiting表示开启keep-alive长连接情况下，没有读写而建立长链接的情况
Reading: 0 Writing: 1 Waiting: 1 
~~~



## 8.配置简单下载界面

~~~yml
location /down{
	#配置文件夹位置
	#注意：这里实际访问的是/soft/package/src/down这个文件夹
    root /soft/package/src;
    #显示目录
    autoindex on;
    #显示上传时间
    autoindex_localtime on;
    #显示文件大概大小
    autoindex_exact_size off;
}
~~~



## 9.配置访问请求限制

参考：参考：https://www.cnblogs.com/shouke/p/10157552.html

注：多个请求可能建立在一个TCP连接上，因此限制请求比限制连接有效

### 9.1 声明配置

~~~yml
#在http模块中全局定义请求限制，然后再在location中引用配置
limit_req_zone $binary_remote_addr zone=req_zone:1m rate=1r/s;
~~~

### 9.2 配置页面

~~~yml
location / {
    root /soft/code/www;
    index index.html;
	#在location中引用配置
	#只接受一个请求，多余的直接处理
    #limit_req zone=req_zone;
	#只接受一个请求，多余的延迟处理，当请求数多余burst，则多余的返回503
    limit_req zone=req_zone burst=3 nodelay;

}

~~~



## 10.访问限制

~~~yml
#在location中配置
#deny表示拒绝访问，可以为具体ip或all
#allow表示允许访问
deny 222.173.61.94;
deny 39.108.129.179;
allow all;
~~~

注意：拒绝跟允许有逻辑顺序，两者上下位置在运行的时候会产生影响

例如：deny all;
			allow 222.173.61.94;

​			会拒绝所有访问



## 11.登录认证

### 11.1 安装了httpd-tools

~~~
[root@iZwz9ev02los7q1d71e7muZ nginx]# yum install httpd-tools
~~~

### 11.2 htpasswd说明

~~~yml
[root@iZwz9ev02los7q1d71e7muZ nginx]# htpasswd --help
Usage:
	htpasswd [-cimBdpsDv] [-C cost] passwordfile username
	htpasswd -b[cmBdpsDv] [-C cost] passwordfile username password

	htpasswd -n[imBdps] [-C cost] username
	htpasswd -nb[mBdps] [-C cost] username password
	#直接创建新文件会导致旧文件被覆盖
 -c  Create a new file.
 -n  Don't update file; display results on stdout.
 -b  Use the password from the command line rather than prompting for it.
 -i  Read password from stdin without verification (for script usage).
 -m  Force MD5 encryption of the password (default).
 -B  Force bcrypt encryption of the password (very secure).
 -C  Set the computing time used for the bcrypt algorithm
     (higher is more secure but slower, default: 5, valid: 4 to 31).
 -d  Force CRYPT encryption of the password (8 chars max, insecure).
 -s  Force SHA encryption of the password (insecure).
 	#使用明文密码
 -p  Do not encrypt the password (plaintext, insecure).
 	#删除指定用户
 -D  Delete the specified user.
 -v  Verify password for the specified user.
On other systems than Windows and NetWare the '-p' flag will probably not work.
The SHA algorithm does not use a salt and is less secure than the MD5 algorithm.
~~~

### 11.3 htpasswd使用

注意：不指明目录则默认在当前目录生成.htpasswd文件

#### 11.3.1 设置配置文件，添加用户

~~~yml
[root@iZwz9ev02los7q1d71e7muZ /]# htpasswd -c /etc/nginx/auth_conf huang
#若已有配置文件，想要不要覆盖旧的并且添加新用户
htpasswd -b /etc/nginx/auth_conf 用户名 密码
~~~

~~~yml
#输入密码回车显示添加了新用户
New password: xxxxxxx
Re-type new password: xxxxxxx
~~~

#### 11.3.2 确认配置文件

~~~yml
[root@iZwz9ev02los7q1d71e7muZ /]# cat /etc/nginx/auth_conf
~~~

~~~yml
#显示账号和密码
huang:$apr1$u6cnrOVn$tlA7/I8CMyng.zEYrMr1W.
~~~

#### 11.3.3 在loaction中启用模块

~~~yml
#当访问时提示消息
auth_basic "please login!";
#指明配置文件位置
auth_basic_user_file /etc/nginx/auth_conf;
~~~



## 12.存储静态资源

### 12.1 在location里配置拦截特定后缀

~~~yml
#根据后缀拦截，这里拦截的是图片
location ~ .*\.(gif|GIF|jpg|JPG|jpeg|JEPG|png|PNG)$ {
                gzip on;
                gzip_http_version 1.1;
                gzip_comp_level 2;
                gzip_types text/plain application/json application/x-javascript application/css application/xml application/xml+rss text/javascript     application/x-httpd-php image/jpeg image/gif image/png;
				#配置静态资源库
                root /soft/package/img;
 }

~~~

### 12.2 对图片传输配置

~~~yml
#配置gzip

#开启压缩
gzip on;
#http协议版本
gzip_http_version 1.1;
#压缩等级
gzip_comp_level 2;
#压缩的文件类型
gzip_types image/gif image/jpeg image/png image/svg+xml image/tiff image/vnd.wap.wbmp image/webp image/x-icon image/x-jng image/x-ms-bmp
~~~

注意：gzip_type这一栏可以在nginx安装目录里找到所有类型

~~~
[root@iZwz9ev02los7q1d71e7muZ package]# vim /etc/nginx/mime.types


types {
    text/html                                        html htm shtml;
    text/css                                         css;
    text/xml                                         xml;
    image/gif                                        gif;
    image/jpeg                                       jpeg jpg;
    application/javascript                           js;
    application/atom+xml                             atom;
    application/rss+xml                              rss;

    text/mathml                                      mml;
    text/plain                                       txt;
    text/vnd.sun.j2me.app-descriptor                 jad;
    text/vnd.wap.wml                                 wml;
    text/x-component                                 htc;

    image/png                                        png;
    image/svg+xml                                    svg svgz;
    image/tiff                                       tif tiff;
    image/vnd.wap.wbmp                               wbmp;
    image/webp                                       webp;
    image/x-icon                                     ico;
    image/x-jng                                      jng;
    image/x-ms-bmp                                   bmp;
							... ...
~~~

### 12.3 设置防盗链

~~~yml
#在对应location中判断是否为允许访问的ip/域名
valid_referers none blocked 111.17.194.89;
#如果不是，就返回一个403
#注意：if和（）之间需要空格
if ($invalid_referer){
    return 403;
}
~~~

另外：资源服务器可以通过网易或七牛cdn之类的服务商白嫖，不一定用自己的



## 13.反向代理

### 13.1 配置页面

~~~yml
#在location中添加
location / {
    proxy_pass http://www.qiuyeye.cn/index.html;
    include /etc/nginx/conf.d/proxy_params.conf;
}
~~~

### 13.2 配置参数

~~~yml
#添加代理参数
proxy_redirect  default;
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
~~~

