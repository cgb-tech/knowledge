# Tomcat篇

## 1、tomcat架构

### 1.1 Http

- 规定了浏览器与服务器之间的数据传输协议，是基于TCP/IP协议来进行传递数据的，不参与数据包的传输，主要规定了客户端和服务器之间的通信格式

![image-20200311195410209](C:\Users\888\AppData\Roaming\Typora\typora-user-images\image-20200311195410209.png)

### 1.2 tomcat处理流程

![image-20200311200213042](C:\Users\888\AppData\Roaming\Typora\typora-user-images\image-20200311200213042.png)

### 1.3 tomcat整体架构

tomcat的两个核心功能

- 处理Socket连接，负责网络字节流与Request和Response对象的转化
- 加载和管理Servlet，以及具体处理Request请求

因此Tomcat设计了两个核心组件连接器(Connector)和容器(Container)来分别做这两件事情。连接器负责对外交流，容器负责内部处理

![image-20200311200617025](C:\Users\888\AppData\Roaming\Typora\typora-user-images\image-20200311200617025.png)

### 1.4 连接器 - Coyote

#### 1.4.1 架构介绍

Coyote是Tomcat的连接器框架的名称，是Tomcat服务 器提供的供客户端访问的外部接口。客户端通过coyote与服务器建立连接、发送请求并接受响应。

Coyote封装了底层的网络通信( Socket请求及响应处理) , 为Catalina容器提供了统一-的接口 ,使Catalina容器与具体的请求协议及I0操作方式完全解耦。Coyote 将socket输入转换封装为Request对象,交由catalina容器进行处理,处理请求完成后,Catalina通过coyote提供的Response 对象将结果写入输出流。

Coyote作为独立的模块,只负责具体协议和I0的相关操作，与servlet 规范实现没有直接关系,因此即便是Request 和 Response对象也并未实现servlet规范对应的接口，而是在Catalina 中将他们进一步封装为servletRequest 和 ServletResponse。

![image-20200311201107424](C:\Users\888\AppData\Roaming\Typora\typora-user-images\image-20200311201107424.png)

#### 1.4.2 IO模型与协议

在Coyote中，Tomcat支持的多种I/ 0模型和应用层协议,具体包含哪些I0模型和应用层协议,请看下表:

Tomcat支持的Io模型(月8.5/9.0版本起. Tomcat移除了对BIO的支持) :

| IO模型 | 描述                                                         |
| ------ | ------------------------------------------------------------ |
| NIO    | 非阻塞I/O,采用Java NIO类库实现。                             |
| NIO2   | 异步I/O,采用JDK 7最新的NIO2类库实现。                        |
| APR    | 采用Apache可移植运行库实现，是c/C++编写的本地库。如果选择该方案,需要单独安装APR库。 |

Tomcat 支持的应用层协议

| 应用层协议 | 描述                                                         |
| ---------- | ------------------------------------------------------------ |
| HTTP/1.1   | 这是大部分web应用采用的访问协议。                            |
| AJP        | 用于和web服务器集成(如Apache ) , 以实现对静态资源的优化以及集群部署,当前支持AJP/1.3。 |
| HTTP/2     | HTTP 2.0大幅度的提升了web性能。下一代HTTP协议，自8. 5以及9.0版本之后支持。 |

协议分层：

![image-20200311201656597](C:\Users\888\AppData\Roaming\Typora\typora-user-images\image-20200311201656597.png)

一个容器可以对应多个连接器，两个都不能单独对外提供服务