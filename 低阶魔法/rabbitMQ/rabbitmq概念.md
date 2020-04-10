## Exchange

### 属性：

- name : 交换机的名字
- Type : 交换机类型 direct topic fanout hearders
- Durability : 是否进行持久化， true 是进行持久化
- AutoDelete : 当最后一个绑定在交换机上的队列被删除后，自动删除该交换机
- Internal : 当前交换机是否应用于rabbtmq内部，默认为False
- Arguments : 扩展参数，用于扩展AMQP协议自定化使用

Direct Exchange 固定的RouteKey

Topic Exchange  # 和 * ：#可以匹配多个单词，而*只能匹配一个单词

Fanout Exchange 不处理路由键，全部转发到与该交换机绑定的队列上，最快，性能最好

### Binding:

### Queue：消息队列

​	消息队列，实际存储消息数据

- Durability 是否持久化
- Auto Delete 是否自动删除，当设置为true的时候，当最后一个舰艇被移除的时候，该队列删除

### Message：消息

数据  里面有Properties和Payload（body）

expiration设置过期时间