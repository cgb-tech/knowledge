# Netty怎么切换三种I/O模式

## 一、什么是经典的三种I/O模式

> 阻塞与非阻塞
>
> 阻塞:没有数据传过来时，读会阻塞直到有数据;缓冲区满时，写操作也会阻塞。
>
> 非阻塞遇到这些情况，都是直接返回

> 同步与异步
>
> 数据就绪后需要自己去读是同步，数据就绪直接读好再回调给程序是异步。

## 二、Netty对三种I/O模式的支持

![mark](http://codedorado.oss-cn-beijing.aliyuncs.com/images/20200502/201141352.png)

## 三、为什么Netty仅支持NIO了?

>为什么不建议(deprecate) 阻塞I/0 (BI0/0I0) ?
>
>连接数高的情况下:阻塞->耗资源、效率低

> 为什么删掉已经做好的AIO支持?
>
> - Windows 实现成熟， 但是很少用来做服务器。
> - Linux 常用来做服务器， 但是AlO实现不够成熟。
> - Linux 下AIO相比较NIO的性能提升不明显 。

## 四、为什么Netty有多种NIO实现?

![mark](http://codedorado.oss-cn-beijing.aliyuncs.com/images/20200502/201240298.png)

> 通用的NIO实现(Common)在Linux下也是使用`epoll`,为什么自己单独实现?
>
> > - Netty 暴露了更多的可控参数，例如:
> >   - jDK 的 NIO 默认实现是水平触发
> >   - Netty 是边缘触发(默认)和水平触发可切换
> > - Netty 实现的垃圾回收更少、性能更好

## 五、NIO一定优于BIO么?

> - BIO代码简单。
>
> - 特定场景:连接数少，并发度低，BIO性能不输NIO。

## 六、源码解读Netty怎么切换I/O模式?

### 1、怎么切换？

...

### 2、原理是什么？

...  泛型 反射 工厂模式

### 3、为什么服务器开发并不需要切换客户端对应 Socket？