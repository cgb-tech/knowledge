zookeeper的特点

- zookeeper集群的中的每一个zookeeper服务器中都有一个相同的副本，这样保证了客户端访问哪一个节点都可以保证数据是一致的

- 集群中只要有半数以上存活的节点，zookeeper集群就可以正常服务

- zookeeper集群中有一个领导者（leader）和多个跟随者（follower）
- 对每一个客户端（Client）发来的请求都`顺序执行`
- 数据更新原子性，一次数据要么更新成功，要么失败
- 实时性，在一定时间范围内，client能得到最新的数据 

zookeeper应用场景

- 统一命名服务：
  - 将域名与多个ip地址绑定
- 统一配置管理：
  - 配置文件同步 
  - ![1580017862038](C:\Users\888\AppData\Roaming\Typora\typora-user-images\1580017862038.png)
- 统一集群管理：
- 服务器动态上下线 （通知）
- 软负载均衡
  - 在zookeeper中记录每台服务器的访问数，让访问数最少的服务器去处理最新的客户端请求
- 

