## springboot与日志

#### 1.springBoot日志关系

```xml
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-logging</artifactId>
      <version>2.1.6.RELEASE</version>
      <scope>compile</scope>
    </dependency>
```

![1563802325233](C:\Users\888\AppData\Roaming\Typora\typora-user-images\1563802325233.png)

```java
Logger logger = LoggerFactory.getLogger(getClass());

@Test
public void contextLoads() {


    //级别由低到高
    //可以调整日志的级别
    logger.trace("这是trace日志");
    logger.debug("debug");
    logger.info("info");
    logger.warn("warn");
    logger.error("error");

}
springboot默认是info以上的 root级别
```

```xml
logging.file=*:/*.log 
logging.path=/spring/log
#冲突属性，一般用path，两者一起的时候默认用file
```

 在springboot中logging![1563804607225](C:\Users\888\AppData\Roaming\Typora\typora-user-images\1563804607225.png)

每个框架放上自己的配置文件就可以使用高级的一些日志配置

logback-spring.log加了spring后缀可以使用一些高级功能

<springProfile>标签可以使用：可以指定某段配置在某段环境生效



 



