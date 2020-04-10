# 消息如何保障100%的投递成功

### 什么是生产端的可靠投递

✔ 保障消息的成功发出

- 消息落库，对消息状态进行打标

![1564538722197](C:\Users\888\AppData\Roaming\Typora\typora-user-images\1564538722197.png)

![1564539032457](C:\Users\888\AppData\Roaming\Typora\typora-user-images\1564539032457.png)

- 消息的延迟投递，做二次确认回调检查

✔ 保障MQ节点的成功接收

✔ 发送端收到MQ节点（Broker）确认应答

✔ 完善的消息进行补偿机制



# 幂等性概念详解

 ![1564539843521](C:\Users\888\AppData\Roaming\Typora\typora-user-images\1564539843521.png)

![1564539965832](C:\Users\888\AppData\Roaming\Typora\typora-user-images\1564539965832.png)

![1564540184476](C:\Users\888\AppData\Roaming\Typora\typora-user-images\1564540184476.png)

# 在海量订单产生的业务高峰期，如何避免消息的重复消费问题





# Confirm确认消息、Return返回消息

![1564540245372](C:\Users\888\AppData\Roaming\Typora\typora-user-images\1564540245372.png)

![1564540325590](C:\Users\888\AppData\Roaming\Typora\typora-user-images\1564540325590.png)

![1564542234153](C:\Users\888\AppData\Roaming\Typora\typora-user-images\1564542234153.png)

