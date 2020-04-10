# rabbitMq的快速安装和使用在第二部分，第一部分就看个热闹，第二部分直接可以完成环境的搭建

## 第一部分

http://www.cnerlang.com/resource/182.html 下载 erlang

安装erlang

```shell
在解压erlang的时候出现问题的时候
#tar -vxf memcached-1.4.34.tar.gz

tar包压缩的时候用cvf参数，解压的时候用xvf参数
或压缩的时候用czvf参数，解压的时候用xzvf参数
```

cd otp_src_21.0

 ./configure 

```shell
安装erlang的时候，使用make命令一直报这个错 

Makefile:248: /usr/local/otp_src_18.1/make/x86_64-unknown-linux-gnu/otp_ded.mk: No such file or directory error: No curses library functions found

解决：
    # yum install gcc-c++  
    # ./configure --prefix=/home/erlang --without-javac  
    # make  
    # make install  
```

/home/erlang/bin/erl  

在path配置erl的bin

 可以在/etc/profile 当中添加
PATH=$PATH:/home/erlang/bin

export $PATH

保存退出，再用命令source /etc/profile 生效   

---

## 第二部分

rabbitmq:

安装erlang https://github.com/rabbitmq/erlang-rpm/releases

根据自己的系统和需要的版本下载完之后，使用`rpm -ivh`进行快速安装

安装完erlang之后运行下面的shell语句，安装erlang和rabbitmq的`中间人`

```shell
yum install -y socat　
```

- [下载](https://www.rabbitmq.com/install-rpm.html#downloads)rabbitmq

  根据自己的需要下载所需要的版本

  

然后运行下面的命令来安装客户端

```shell
[root@localhost soft]# rabbitmq-plugins enable rabbitmq_management
The following plugins have been configured:
  rabbitmq_management
  rabbitmq_management_agent
  rabbitmq_web_dispatch
Applying plugin configuration to rabbit@localhost...
The following plugins have been enabled:
  rabbitmq_management
  rabbitmq_management_agent
  rabbitmq_web_dispatch

started 3 plugins.

[root@localhost soft]# systemctl start rabbitmq-server
```

在另外的主机不能访问其guest用户，在终端运行以下命令，创建admin用户，然后重新进行登录经即可

```shell
#添加用户
#./rabbitmqctl add_user 账号 密码
./rabbitmqctl add_user admin admin
#分配用户标签(admin为要赋予administrator权限的刚创建的那个账号的名字)
./rabbitmqctl set_user_tags admin administrator
#设置权限<即开启远程访问>(如果需要远程连接,例如java项目中需要调用mq,则一定要配置,否则无法连接到mq,admin为要赋予远程访问权限的刚创建的那个账号的名字,必须运行着rabbitmq此命令才能执行)
./rabbitmqctl set_permissions -p "/" admin ".*" ".*" ".*" 

```

https://www.cnblogs.com/LUA123/p/8469574.html

**感谢这位博主，让我脱离了配置的苦海**



## 配置：

在`/usr/lib/rabbitmq/lib/rabbitmq_server-3.7.16/ebin/rabbit.app" 126L, 8517C written`下修改，为

```shell
{loopback_users, [guest]},
```

可以外网登录



重启rabbitmq服务通过两个命令来实现： 
 `rabbitmqctl stop` ：停止rabbitmq 
 `rabbitmq-server restart` : 重启rabbitmq （`systemctl start rabbitmq-server`**可以选择后台运行**）

因为rabbitmqctl是没有restart命令的，所以重启rabbitmq服务需要这么两步。