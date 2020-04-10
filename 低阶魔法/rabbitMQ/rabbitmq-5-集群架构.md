## RabbitMQ集群架构模式

- ==主备模式==: 实现rabbitmq的高可用集群,一般在并发和数据量不高的情况下,这种模式非常的好用且简单.主备模式也称之为Warren模式

![1564884401352](C:\Users\888\AppData\Roaming\Typora\typora-user-images\1564884401352.png)

- ==远程模式==: 远程模式可以实现双活的一种模式,简称shovel模式,所谓shovel就是我们可以把消息进行不同数据中心的复制工作,我们可以跨地域的让两个mq集群互联

![1564884705395](C:\Users\888\AppData\Roaming\Typora\typora-user-images\1564884705395.png)

![1564884732841](C:\Users\888\AppData\Roaming\Typora\typora-user-images\1564884732841.png)

![1564884762384](C:\Users\888\AppData\Roaming\Typora\typora-user-images\1564884762384.png)

- ==镜像模式==: 集群模式非常经典的就是Mirror镜像模式,保证100%数据不丢失,在实际工作中也是用的最多的.并且实现集群非常的简单,一般互联网大厂都会构建这种镜像集模式,主要就是实现数据的同步,一般来讲是2-3个节点实现数据同步(对于100%数据可靠性解决方案一般是3节点)

![1564885253226](C:\Users\888\AppData\Roaming\Typora\typora-user-images\1564885253226.png)

- ==多活模式==: 这种模式也是实现异地数据复制的主流模式,因为shovel模式配置比较复杂,所以一般来说实现异地集群都是使用这种双活 或着 多活模型来去实现的.这种模式需要以来rabbitmq的federation插件,可以实现持续的可靠的AMQP数据通信,多活模式在实际配置与应用非常的简单

![1564885623240](C:\Users\888\AppData\Roaming\Typora\typora-user-images\1564885623240.png)

![1564885652716](C:\Users\888\AppData\Roaming\Typora\typora-user-images\1564885652716.png)

![1564885664279](C:\Users\888\AppData\Roaming\Typora\typora-user-images\1564885664279.png)

5-7