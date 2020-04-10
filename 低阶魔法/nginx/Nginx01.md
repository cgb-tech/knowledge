## Nginx

#### 1.[网址](http://nginx.org/en/linux_packages.html)

#### 2.安装

- 前提

> ```shell
> sudo yum install yum-utils
> ```
>
> 云主机重启之后需要执行
>
> nginx -c /etc/nginx/nginx.conf
>
> 才能nginx -s reload

https://www.xuliangwei.com/bgx/974.html

```shell
//确认系统网络
[root@xuliangwei ~]# ping baidu.com

//确认yum可用
[root@xuliangwei ~]# yum install -y gcc gcc-c++ autoconf pcre pcre-devel make automake wget httpd-tools vim tree
 
//关闭iptables
[root@xuliangwei ~]# /etc/init.d/iptables stop
[root@xuliangwei ~]# chkconfig iptables off

//临时关闭selinux
[root@xuliangwei ~]# setenforce 0

//初始化基本目录
mkdir /soft/{code,logs,package/src} -p

//配置Nginx官方Yum源
[root@xuliangwei ~]# vim /etc/yum.repos.d/nginx.repo
[nginx]
name=nginx repo
baseurl=http://nginx.org/packages/centos/7/$basearch/
gpgcheck=0
enabled=1

//安装Nginx
[root@xuliangwei ~]# yum install nginx -y

//查看Nginx当前版本
[root@xuliangwei ~]# nginx -v
nginx version: nginx/1.12.2
```

