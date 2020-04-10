## 在springboot中使用mq

- 首先引入依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-amqp</artifactId>
</dependency>

//里面有另外的依赖
<dependency>
    <groupId>org.springframework.amqp</groupId>
    <artifactId>spring-rabbit</artifactId>
    <version>2.1.7.RELEASE</version>
    <scope>compile</scope>
    <exclusions>
        <exclusion>
            <artifactId>http-client</artifactId>
            <groupId>com.rabbitmq</groupId>
        </exclusion>
    </exclusions>
</dependency>
//下面还有他的子依赖
<dependency>
    <groupId>com.rabbitmq</groupId>
    <artifactId>amqp-client</artifactId>
    <version>5.4.3</version>
    <scope>compile</scope>
</dependency>
<dependency>
    <groupId>com.rabbitmq</groupId>
    <artifactId>http-client</artifactId>
    <version>2.1.0.RELEASE</version>
    <scope>compile</scope>
    <optional>true</optional>
</dependency>
//等
```

