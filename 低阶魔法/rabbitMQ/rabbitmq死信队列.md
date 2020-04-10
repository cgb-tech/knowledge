## 死信队列

**死信队列： DLX，Dead-Letter-Exchange**

- 利用DLX，当消息在一个队列中变成死信（dead message）之后，它能被重新publish到另一个Exchange，这个Exchange就是DLX

死信队列形成的三种情况

- 消息被拒绝后（basic.reject/basic.nack）并且requeue=false
- 消息TTL过期
- 队列达到最大长度

![1564556715578](C:\Users\888\AppData\Roaming\Typora\typora-user-images\1564556715578.png)

## 死信队列设置

1. 首先需要设置死信队列的exchange和queue，然后绑定
   1. Exchange ：dlx.exchange
   2. Queue : dlx.queue
   3. RoutingKey : #
2. 然后进行正常声明交换机、队列、绑定，只不过我们需要在队列加上一个参数即可：arguments.put("X-dead-letter-exchange","dlx.exchange");
3. 这样消息在过期、requeue、队列在达到最大长度时，消息就可以直接路由到死信队列！