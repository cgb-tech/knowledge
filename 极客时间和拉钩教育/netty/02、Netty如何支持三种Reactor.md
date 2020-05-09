#  Netty如何支持三种Reactor

> 什么是Reactor及三种版本
>
> 如何在Netty中使用Reactor模式
>
> 解析Netty对Reactor模式支持的常见疑问

## 一、什么是Reactor及三种版本

> Reactor是-种开发模式，模式的核心流程:
>
> 注册感兴趣的事件->扫描是否有感兴趣的事件发生->事件发生后做出相应的处理。

| client/Server | SocketChannel/ServerSocketChannel | OP_ACCEPT | OP_CONNECT | OP_WRITE | OP_READ |
| ------------- | --------------------------------- | --------- | ---------- | -------- | ------- |
| client        | SocketChannel                     |           | Y          | Y        | Y       |
| server        | ServerSocketChannel               | Y         |            |          |         |
| server        | SocketChannel                     |           |            | Y        | Y       |

>  BIO对应的模式：

![mark](http://codedorado.oss-cn-beijing.aliyuncs.com/images/20200502/204449049.png)

> Reactor模式V1:单线程

![mark](http://codedorado.oss-cn-beijing.aliyuncs.com/images/20200502/204619002.png)

> Reactor模式V2:多线程

![mark](http://codedorado.oss-cn-beijing.aliyuncs.com/images/20200502/204658171.png)

> Reactor模式V3:主从多线程

![mark](http://codedorado.oss-cn-beijing.aliyuncs.com/images/20200502/204839095.png)

## 二、如何在Netty中使用Reactor模式

![mark](http://codedorado.oss-cn-beijing.aliyuncs.com/images/20200502/205003961.png)

## 三、解析Netty对Reactor模式支持的常见疑问

> 1. Netty如何支持主从Reactor模式的?
> 2. 为什么说Netty的main reactor大多并不能用到一个线程组，只能线程组里面的一个？
> 3. Netty给Channel分配NIO event loop的规则是什么
> 4. 通用模式的NIO实现多路复用器是怎么跨平台的