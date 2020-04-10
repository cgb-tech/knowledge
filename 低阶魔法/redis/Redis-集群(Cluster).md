[一个博主的步骤](https://blog.csdn.net/weixin_37882382/article/details/83538367)

## 怎么关闭redis

如果是用`apt-get`或者`yum install`安装的redis，可以直接通过下面的命令停止/启动/重启redis



```
/etc/init.d/redis-server stop
/etc/init.d/redis-server start
/etc/init.d/redis-server restart
```

如果是通过源码安装的redis，则可以通过redis的客户端程序`redis-cli``shutdown`



```
redis-cli -h 127.0.0.1 -p 6379 shutdown
```

如果上述方式都没有成功停止redis，则可以使用终极武器 `kill -9`

怎么查看redis

```shell
ps -ef|grep redis
```

39.96.11.225:6379





分别修改端口为7001..7006

port 7001  

修改绑定ip地址为本机实际网路ip地址

#bind 127.0.0.1
bind  192.168.1.204



## 启用集群模式
```shell
cluster-enabled yes  

nodes-7001.conf...nodes-7006.conf

cluster-config-file nodes-7001.conf
cluster-node-timeout 5000 

#超时时间appendonly yes
daemonize yes 

#后台运行
protected-mode no 

#非保护模式pidfile,分别修改为redis_7001.pid...7006.pid  
/var/run/redis_7001.pid
```

```shell
mkdir  cluster  # 用于存放 redis.conf .rdb .aof nodes-xxxx.conf
cd cluster  
mkdir 7000  #端口为 7000的配置文件目录 后续还会有 7001等
cp  redis.conf 7000  #伪命令 将 redis根目录下的配置文件 拷贝一份到 新建的 7000目录下
nano  redis.conf  #开始修改配置文件
```

需要修改的配置文件

```shell
daemonize    yes  # redis后台运行
pidfile  /var/run/redis_7000.pid  #需要修改为 reids_{port}.pid 的形式
port  7000  #端口
cluster-enabled  yes #开启集群
cluster-config-file  nodes_7000.conf #集群的配置文件 nodes_{port}.conf的形式
cluster-node-timeout  5000 #超时时间 5s够了
appendonly  yes #开启AOF日志
```



==需要把相对应的端口+10000也打开==``否则一直waiting``





```nginx

#启动redis服务节点
[root@centos-cluster-s19423 redis5]# /home/app/redis5/src/redis-server   /home/app/redis5/redis-cluster-conf/7001/redis.conf 
[root@centos-cluster-s19423 redis5]# /home/app/redis5/src/redis-server   /home/app/redis5/redis-cluster-conf/7002/redis.conf 
[root@centos-cluster-s19423 redis5]# /home/app/redis5/src/redis-server   /home/app/redis5/redis-cluster-conf/7003/redis.conf 
[root@centos-cluster-s19423 redis5]# /home/app/redis5/src/redis-server   /home/app/redis5/redis-cluster-conf/7004/redis.conf 
[root@centos-cluster-s19423 redis5]# /home/app/redis5/src/redis-server   /home/app/redis5/redis-cluster-conf/7005/redis.conf 
[root@centos-cluster-s19423 redis5]# /home/app/redis5/src/redis-server   /home/app/redis5/redis-cluster-conf/7006/redis.conf
```

```shell

[root@centos-cluster-s19423 redis-cluster-conf]#  ./redis-cli   --cluster   create   39.96.11.225:6379   39.96.11.225:6380    39.96.11.225:6381   39.96.11.225:6382   39.96.11.225:6383   39.96.11.225:6384  --cluster-replicas 1
>>> Performing hash slots allocation on 6 nodes...
Master[0] -> Slots 0 - 5460
Master[1] -> Slots 5461 - 10922
Master[2] -> Slots 10923 - 16383
Adding replica 192.168.1.204:7004 to 192.168.1.204:7001
Adding replica 192.168.1.204:7005 to 192.168.1.204:7002
Adding replica 192.168.1.204:7006 to 192.168.1.204:7003
>>> Trying to optimize slaves allocation for anti-affinity
[WARNING] Some slaves are in the same host as their master
M: 075a4724233a382182d369ddd9c5cfff1936f8cb 192.168.1.204:7001
   slots:[0-5460] (5461 slots) master
M: 70fadf578fe98d5a2a1194e2eecd9fc96e238bd2 192.168.1.204:7002
   slots:[5461-10922] (5462 slots) master
M: 739076a173ecd494e03e8dc0a04b6fc9a3691b86 192.168.1.204:7003
   slots:[10923-16383] (5461 slots) master
S: e4e4cebd9f830c3b524cb1e6a8dea8850245c9c1 192.168.1.204:7004
   replicates 739076a173ecd494e03e8dc0a04b6fc9a3691b86
S: 7b1844c6f5f187a95b7b3e3950b62461f36b091e 192.168.1.204:7005
   replicates 075a4724233a382182d369ddd9c5cfff1936f8cb
S: 127b7434c2f2707bce4185866fac4bd896f28a6b 192.168.1.204:7006
   replicates 70fadf578fe98d5a2a1194e2eecd9fc96e238bd2
Can I set the above configuration? (type 'yes' to accept): yes
>>> Nodes configuration updated
>>> Assign a different config epoch to each node
>>> Sending CLUSTER MEET messages to join the cluster
Waiting for the cluster to join
...
>>> Performing Cluster Check (using node 192.168.1.204:7001)
M: 075a4724233a382182d369ddd9c5cfff1936f8cb 192.168.1.204:7001
   slots:[0-5460] (5461 slots) master
   1 additional replica(s)
S: e4e4cebd9f830c3b524cb1e6a8dea8850245c9c1 192.168.1.204:7004
   slots: (0 slots) slave
   replicates 739076a173ecd494e03e8dc0a04b6fc9a3691b86
S: 127b7434c2f2707bce4185866fac4bd896f28a6b 192.168.1.204:7006
   slots: (0 slots) slave
   replicates 70fadf578fe98d5a2a1194e2eecd9fc96e238bd2
S: 7b1844c6f5f187a95b7b3e3950b62461f36b091e 192.168.1.204:7005
   slots: (0 slots) slave
   replicates 075a4724233a382182d369ddd9c5cfff1936f8cb
M: 70fadf578fe98d5a2a1194e2eecd9fc96e238bd2 192.168.1.204:7002
   slots:[5461-10922] (5462 slots) master
   1 additional replica(s)
M: 739076a173ecd494e03e8dc0a04b6fc9a3691b86 192.168.1.204:7003
   slots:[10923-16383] (5461 slots) master
   1 additional replica(s)
[OK] All nodes agree about slots configuration.
>>> Check for open slots...
>>> Check slots coverage...
[OK] All 16384 slots covered.
```



## hash分区

- 节点取余分区
  - 客户端分片:哈希 + 取余
  - 节点伸缩 : 数据节点关系变化,导致数据迁移
  - 迁移数量和添加节点数量有关 : 建议翻倍扩容
- 一致性哈希分区
  - 客户端分片: 哈希 + 顺时针(优化取余)
  - 节点伸缩 : 只影响临近节点,但是还是有数据迁移
  - 翻倍伸缩 : 保证最小迁移数据和负载均衡
- 虚拟槽分区



## 集群手动搭建



![1564819312353](C:\Users\888\AppData\Roaming\Typora\typora-user-images\1564819312353.png)

![1564819340870](C:\Users\888\AppData\Roaming\Typora\typora-user-images\1564819340870.png)

![1564819379009](C:\Users\888\AppData\Roaming\Typora\typora-user-images\1564819379009.png)



![1564819528938](C:\Users\888\AppData\Roaming\Typora\typora-user-images\1564819528938.png)

![1564819626645](C:\Users\888\AppData\Roaming\Typora\typora-user-images\1564819626645.png)

http://www.xwood.net/_site_domain_/_root/5870/5874/t_c279826.html

https://blog.csdn.net/truong/article/details/52531103

https://blog.csdn.net/wudalang_gd/article/details/52121204