## RabbitAdmin

![1564558438062](C:\Users\888\AppData\Roaming\Typora\typora-user-images\1564558438062.png)

![1564558515575](C:\Users\888\AppData\Roaming\Typora\typora-user-images\1564558515575.png)

![1564558553540](C:\Users\888\AppData\Roaming\Typora\typora-user-images\1564558553540.png)

```java
public void testAdmin() {
    rabbitAdmin.declareExchange(new DirectExchange("text.exchange",false,false));

    rabbitAdmin.declareQueue(new Queue("test.queue",false));

    rabbitAdmin.declareBinding(new Binding("test.queue",
                                           Binding.DestinationType.QUEUE,"text.exchange",
                                           "direct",new HashMap<>()));
}
rabbitAdmin.declareBinding(BindingBuilder
                .bind(new Queue("test.queue",false))
                           .to(new DirectExchange("text.exchange",false,false))
                           .with("test.queue"));
```

还可以使用配置类的方式来进行组件的声明

## RabbitTemplate

![1564562302578](C:\Users\888\AppData\Roaming\Typora\typora-user-images\1564562302578.png)

在springboot-rabbit中有，在此不过多介绍了

## SimpleMessageListenerContainer

### 简单消息监听器

![1564563091993](C:\Users\888\AppData\Roaming\Typora\typora-user-images\1564563091993.png)

![1564563111729](C:\Users\888\AppData\Roaming\Typora\typora-user-images\1564563111729.png)

![1564563133969](C:\Users\888\AppData\Roaming\Typora\typora-user-images\1564563133969.png)

![1564563162160](C:\Users\888\AppData\Roaming\Typora\typora-user-images\1564563162160.png)

这个在springboot中被整合了

```java
@Bean
public SimpleMessageListenerContainer simpleMessageListenerContainer() {
    SimpleMessageListenerContainer simpleMessageListenerContainer = new SimpleMessageListenerContainer();
    simpleMessageListenerContainer.setAcknowledgeMode(AcknowledgeMode.AUTO);
    return simpleMessageListenerContainer;
}
```

也可以在yaml配置

```yaml
spring:
  rabbitmq:
    host: 39.96.11.225
    username: guest
    password: guest
    listener:
      simple:
        default-requeue-rejected: false
        acknowledge-mode: auto
```



## MessageListenerAdapter

### 消息监听适配器

MessageListenerAdapter adapter = new MessageListenerAdapter(new myAdapter())

## MessageConverter

![1564566733283](C:\Users\888\AppData\Roaming\Typora\typora-user-images\1564566733283.png)

![1564566793774](C:\Users\888\AppData\Roaming\Typora\typora-user-images\1564566793774.png)

```java
@Configuration
public class MyAMQPConfig {

    @Bean
    public MessageConverter messageConverter() {
        return new Jackson2JsonMessageConverter();
    }
}
```

## springboot整合配置详解

![1564836446568](C:\Users\888\AppData\Roaming\Typora\typora-user-images\1564836446568.png)

![1564836664895](C:\Users\888\AppData\Roaming\Typora\typora-user-images\1564836664895.png)

![1564836720227](C:\Users\888\AppData\Roaming\Typora\typora-user-images\1564836720227.png)

![1564836816445](C:\Users\888\AppData\Roaming\Typora\typora-user-images\1564836816445.png)

```java
//消息发送的测试用例   
@Test
public void sendMessage() {
    Book book = new Book("1","333");
    for (int i=0;i<10000;i++){
        book.setName("" + i);
        book.setPrice("" + i);
        CorrelationData cd = new CorrelationData(DateUtil.now().toString());
        rabbitTemplate.convertAndSend("","delay",book,cd);
    }

}
```

```java
//book类的声明
@Data
@AllArgsConstructor
@NoArgsConstructor
@Component
public class Book implements Serializable {
    private String name;
    private String price;
}

```

```yaml
//yaml文件的配置
spring:
  rabbitmq:
    host: 39.96.11.225
    username: guest
    password: guest
    listener:
      simple:
        default-requeue-rejected: false
        acknowledge-mode: manual #设置手动签收
        max-concurrency: 20
        concurrency: 5
      direct:
        acknowledge-mode: manual
    template:
      mandatory: true #保证监听有效
    publisher-returns: true
    publisher-confirms: true
    connection-timeout: 15000
```

```java
//主配置类开启rabbit的注解
@EnableRabbit
@SpringBootApplication
public class SpringbootRabbitmqApplication {

    public static void main(String[] args) {
        SpringApplication.run(SpringbootRabbitmqApplication.class, args);
    }

}

```

```java
//消费端的实现
private Logger logger = LoggerFactory.getLogger(getClass());

/**
     *  这个方法是对消息队列的监听方法,可以随时获取消息队列发来的消息
     * @param book 消息内容的属性,用这个对象来接收消息的内容
     * @param channel 必不可少的对象,用来做手动签收必须要的东西
     * @param map 这里面报错了头信息,可以以此来获取 AmqpHeaders.DELIVERY_TAG
     * @throws IOException IO异常,如消息转换异常(经常是因为消息队列中的类型不一致导致的)
     */
@RabbitListener(queues = "delay")
@RabbitHandler
public void receive(@Payload Book book, Channel channel, @Headers Map<String,Object> map) throws IOException {
    System.out.println(book.toString());
    System.out.println(map.get(AmqpHeaders.DELIVERY_TAG));
    logger.info(book.toString());
    Long aLong = (Long) map.get(AmqpHeaders.DELIVERY_TAG);
    channel.basicAck(aLong,false);
    // book.
    // channel.basicAck();
    // Long lang = (Long)message.getMessageProperties().getHeaders().get(AmqpHeaders.DELIVERY_TAG);
    // channel.basicAck(,false);
}
```

至于config中的实现,在上文已经定义好了

