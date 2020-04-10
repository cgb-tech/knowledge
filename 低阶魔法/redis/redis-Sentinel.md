![1564739894414](C:\Users\888\AppData\Roaming\Typora\typora-user-images\1564739894414.png)

![1564739930391](C:\Users\888\AppData\Roaming\Typora\typora-user-images\1564739930391.png)

![1564751121261](C:\Users\888\AppData\Roaming\Typora\typora-user-images\1564751121261.png)

` sed "s/7000/7002/g" redis-7000.conf > redis-7002.conf `

文档复制，进行全局的替换

` cat sentinel.conf  |grep -v "#" |grep -v "^$" `去掉注释和多余的空格

`cat sentinel.conf  |grep -v "#" |grep -v "^$" > reids-sentinel-26379.conf`

使用的是redis-sentinel 来运行这个文件,可以使用redis-cli进行对这个端口的连接

可以使用info来查看各种信息

哨兵的配置:

```shell
port 26380
daemonize yes
pidfile "/var/run/redis-sentinel.pid"
logfile "26380.log"
dir "/opt/soft/redis/redis/data"
sentinel myid 9fed2fda188f9aefb9ef6136e1b265ff244bdd4d
sentinel deny-scripts-reconfig yes
sentinel monitor mymaster 127.0.0.1 7000 2
sentinel config-epoch mymaster 0

```





`./redis-sentinel ../reids-sentinel-26379.conf `哨兵启动



### 三个定时任务

![1564814535607](C:\Users\888\AppData\Roaming\Typora\typora-user-images\1564814535607.png)



![1564814653889](C:\Users\888\AppData\Roaming\Typora\typora-user-images\1564814653889.png)

- 主观下线 : 每个sentinel节点对Redis节点失败的'偏见'
- 客观下线 : 所有sentinel节点对Redis节点失败`达成共识`(超过quorum个统一)



### 领导者选举

sentinel is-master-down-by-addr

![1564815175045](C:\Users\888\AppData\Roaming\Typora\typora-user-images\1564815175045.png)

![1564815381635](C:\Users\888\AppData\Roaming\Typora\typora-user-images\1564815381635.png)

![1564815510851](C:\Users\888\AppData\Roaming\Typora\typora-user-images\1564815510851.png)

### 高可用读写分离

![1564816147573](C:\Users\888\AppData\Roaming\Typora\typora-user-images\1564816147573.png)

![1564816160692](C:\Users\888\AppData\Roaming\Typora\typora-user-images\1564816160692.png)