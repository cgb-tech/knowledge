## 命令行和管控台

```shell
rabbitmqctl stop_app 关闭应用

rabbitmqctl start_app  打开应用

rabbitmqctl status  节点状态

rabbitmqctl add_user username password: 添加用户

rabbitmqctl list_users 列出所有用户

rabbitmqctl delete_user username 删除用户

rabbitmqctl clear_permissions -p vhostpath username: 清除用户权限

rabbitmqctl list_user_permissions username 列出用户权限

rabbitmqctl change_password username newpassword 修改密码

rabbitmqctl set_permissions -p vhostpath username ".*"".*"".*" 设置用户权限(都跟着一个米号)
```

```shell
rabbitmqctl add_vhost vhostpath  创建构建虚拟主机

rabbitmqctl list_vhosts 列出所有虚拟主机

rabbitmqctl list_permissions -p vhostpath :列出虚拟主机上所有权限

rabbitmqctl delete_vhost vhostPath 删除虚拟主机
```

对队列进行操作

```shell
rabbitmqctl list_queues 查看所有队列信息

rabbitmqctl -p vhostpath purge_queue blue 清除队列里的消息

```

高级操作

```shell
rabbitmqctl reset 移除所有数据，需要在rabbitmqctl stop_app之后使用

rabbitmqctl join——cluster<clusternode> [--ram] 组成集群命令，加入节点的时候存储节点的模式

rabbitmqctl cluster_status 查看集群状态

rabbitmqctl change_cluster_node_type disc | ram  修改集群节点的存储模式

rabbitmqctl forget_cluster_node [--offline] 忘记节点（摘除节点）  

rabbitmqctl rename_cluster_node oldnode1 newnode1 [oldnode2] [newnode2 ...]修改节点名称


```

`lsof -i:5672`这个命令可以快速的查看是否启动rabbitmq



