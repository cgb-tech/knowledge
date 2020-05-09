# 交易链路优化

## 一、rocketmq遇到的问题

1、在启动项目的时候可能会遇到启动不起来的问题，因为rocketmq设置的是内网的ip，需要设置一个公网的ip

项目启动的时候就开始打印远程地址为空，发送消息失败并且也打印远程地址为空：

```shell
RocketmqRemoting: closeChannel: close the connection to remote address[] result: true
```

解决方法：

```shell
//查看broker配置
sh ./bin/mqbroker -m
```

在`conf/broker.conf`配置文件中增加

```shell
brokerIP1=X.X.X.X（公网IP）
```

再次启动broker服务

```shell
nohup sh bin/mqbroker -n x.x.x.x:9876 -c conf/broker.conf autoCreateTopicEnable=true &
```

启动结果：

```shell
The broker[broker-a, X.X.X.X:10911] boot success. serializeType=JSON and name server is x.x.x.x:9876
```

重启本地项目，测试producer发送消息成功

## 二、下单逻辑优化

### 1、以前的问题

在数据库的操作和对`redis`与`rocketmq`的操作之间，可能系统会出现一些异常或者错误，导致消息投递失败，数据库和`redis`数据不一致的问题，接下来解决这些问题

### 2、优化

**思路：先扣减redis的库存，生成订单，最后再发送消息**

```java
// itemServiceImpl
@Override
@Transactional(rollbackFor = {})
public boolean decreaseStock(Long itemId, Integer amount) throws BusinessErrorException {
    Long increment = redisTemplate.opsForValue().increment("promo_item_stock_" + itemId, amount * -1);
    if (increment >= 0) {
        return true;
    }
    increaseStock(itemId, amount);
    return false;
}
@Override
public boolean asyncDecreaseStock(Long itemId, Integer amount) throws BusinessErrorException {
    return mqProducer.asyncReduceStock(itemId, amount);
}
@Override
public boolean increaseStock(Long itemId, Integer amount) throws BusinessErrorException {
    redisTemplate.opsForValue().increment("promo_item_stock_" + itemId, amount);
    return true;
}
```

```java
// 启用异步消息的机制
// MqProducer
// 事务型同步库存扣减消息
public boolean transactionAsyncReduceStock(Long userId, Long promoId, Long itemId, Integer amount) {
    HashMap<String, Object> bodyMap = new HashMap<>(8);
    bodyMap.put("itemId", itemId);
    bodyMap.put("amount", amount);

    HashMap<String, Object> argsMap = new HashMap<>(8);
    argsMap.put("itemId", itemId);
    argsMap.put("amount", amount);
    argsMap.put("userId", userId);
    argsMap.put("promoId", promoId);

    TransactionSendResult transactionSendResult = null;
    Message increase = new Message(topicName, "increase", JSON.toJSON(bodyMap).toString().getBytes(StandardCharsets.UTF_8));
    try {
        transactionSendResult = transactionMQProducer.sendMessageInTransaction(increase, argsMap);
    } catch (MQClientException e) {
        e.printStackTrace();
        return false;
    }
    if (transactionSendResult.getLocalTransactionState() == LocalTransactionState.ROLLBACK_MESSAGE) {
        return false;
    } else {
        return transactionSendResult.getLocalTransactionState() == LocalTransactionState.COMMIT_MESSAGE;
    }
}

@PostConstruct
public void init() throws MQClientException {
    producer = new DefaultMQProducer("producer_group");
    producer.setNamesrvAddr(nameAddr);
    producer.start();

    transactionMQProducer = new TransactionMQProducer("transaction_producer_group");
    transactionMQProducer.setNamesrvAddr(nameAddr);
    transactionMQProducer.start();
    transactionMQProducer.setTransactionListener(new TransactionListener() {
        @Override
        public LocalTransactionState executeLocalTransaction(Message message, Object o) {
            try {
                Map<String,Object> args = (Map<String, Object>) o;
                Long itemId = (Long) args.get("itemId");
                Long promoId = (Long) args.get("promoId");
                Long userId = (Long) args.get("userId");
                Integer amount = (Integer) args.get("amount");
                orderService.createOrder(userId, itemId, promoId, amount);
            } catch (BusinessErrorException e) {
                e.printStackTrace();
                return LocalTransactionState.ROLLBACK_MESSAGE;
            }
            return LocalTransactionState.COMMIT_MESSAGE;
        }
        @Override
        public LocalTransactionState checkLocalTransaction(MessageExt messageExt) {
            return null;
        }
    });

}
```

```java
// 在 orderController中
boolean flag = mqProducer.transactionAsyncReduceStock(model.getId(), itemId, promoId, amount);
if (!flag) {
    throw new BusinessErrorException(EmBusinessError.UNKNOW_ERROR, "下单失败");
}
```

---

## 三、异步同步数据库

### 1、问题

- **异步消息发送失败**
- **扣减操作执行失败**
- **下单失败五福正确补回库存**

### 2、操作流水

```shell
# 数据类型
# 主业务数据：master data
# 操作型数据：log data
```

### 3、库存数据库最终一致性保证

```shell
# 方案
# 引入库存操作流水
# 引入事务性消息机制
# 问题
# redis 不可用时如何处理
# 扣减流水错误如何处理
```

### 4、业务场景决定高可用技术实现

- 设计原则
  - 宁可少买，不能超卖
- 方案
  - redis可以比实际数据库中少
  - 超时释放

5、库存售罄























