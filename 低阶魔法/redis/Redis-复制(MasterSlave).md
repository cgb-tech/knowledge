## Redis的复制(Master/Slave)

### 是什么：

- 行话：也就是我们所说的主从复制，主机数据更新后根据配置和策略，
  自动同步到备机的master/slaver机制，Master以写为主，Slave以读为主

- 官网：

![1564124939567](C:\Users\888\AppData\Roaming\Typora\typora-user-images\1564124939567.png)

### 干什么：

- `读写分离`
- `容灾恢复`

### 怎么玩

1. ##### 配从(库)不配主(库)

2. 从库配置：slaveof 主库IP 主库端口

   1. 每次与master断开之后，都需要重新连接，除非你配置进redis.conf文件
   2. Info replication

3. 修改配置文件细节操作

4. 常用3招

   1. `一主二仆`
      - slaveof ip:端口
      - 可以设置只读 : slave-read-only yes
   2. `薪火相传`
      - 上一个Slave可以是下一个slave的Master，Slave同样可以接收其他
        slaves的连接和同步请求，那么该slave作为了链条中下一个的master,
        可以有效减轻master的写压力
      - 中途变更转向:会清除之前的数据，重新建立拷贝最新的
      - Slaveof 新主库IP 新主库端口
   3. `反客为主`
      - sloveof no one



## 复制原理

- Slave启动成功连接到master后会发送一个sync命令
- Master接到命令启动后台的存盘进程，同时收集所有接收到的用于修改数据集命令，
  在后台进程执行完毕之后，master将传送整个数据文件到slave,以完成一次完全同步
- 全量复制：而slave服务在接收到数据库文件数据后，将其存盘并加载到内存中。
- 增量复制：Master继续将新的所有收集到的修改命令依次传给slave,完成同步
- 但是只要是重新连接master,一次完全同步（全量复制)将被自动执行

## ==哨兵模式(sentinel)==

##### 是什么：

- 反客为主的自动版，能够后台监控主机是否故障，如果故障了根据投票数自动将从库转换为主库

##### 使用步骤：

- 调整结构，6379带着80、81
- `自定义的/myredis目录下新建sentinel.conf文件，名字绝不能错`
- 配置哨兵,填写内容
  -  sentinel monitor 被监控数据库名字(自己起名字) 127.0.0.1 6379 1
  - 上面最后一个数字1，表示主机挂掉后salve投票看让谁接替成为主机，得票数多少后成为主机
- 启动哨兵
  - `Redis-sentinel /myredis/sentinel.conf` 
  - 上述目录依照各自的实际情况配置，可能目录不同
- 正常主从演示
- 原有的master挂了
- 投票新选
- 重新主从继续开工,info replication查查看
- 问题：如果之前的master重启回来，会不会双master冲突？

##### 一组sentinel能同时监控多个Master

## 复制的缺点

延迟复制：

- 由于所有的写操作都是先在Master上操作，然后同步更新到Slave上，所以从Master同步到Slave机器有一定的延迟，当系统很繁忙的时候，延迟问题会更加严重，Slave机器数量的增加也会使这个问题更加严重。



## 主从复制的故障转移

#### 开发和运维的问题

- 读写分离
  - 复制数据延迟
  - 读到过期数据
  - 从节点故障
- 主从配置不一致
  - maxmemory不一致 : 丢失数据
  - 数据结构优化参数不一致(例如hash-max-ziplist-entries): 内存不一致
- 规避全量复制
  - 第一次全量复制
    - 第一次不可避免
    - 小主节点、低峰
  - 节点运行ID不匹配
    - 主节点重启(运行ID变化)
    - 故障转移,例如哨兵和集群模式
  - 复制积压缓冲区不足
    - 网络中断,部分复制无法满足
    - 增大复制缓冲区配置rel_backlog_size,网络"增强".

- 规避复制风暴

  ​	![1564739282447](C:\Users\888\AppData\Roaming\Typora\typora-user-images\1564739282447.png)

