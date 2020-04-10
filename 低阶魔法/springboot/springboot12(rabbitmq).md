## RabbitAutoConfiguration

```java
@Configuration
@ConditionalOnClass({ RabbitTemplate.class, Channel.class })
@EnableConfigurationProperties(RabbitProperties.class)
@Import(RabbitAnnotationDrivenConfiguration.class)
public class RabbitAutoConfiguration {
	//配置连接工厂
    @Bean
    public CachingConnectionFactory rabbitConnectionFactory(RabbitProperties properties,
                                                            ObjectProvider<ConnectionNameStrategy> connectionNameStrategy) throws Exception {
        PropertyMapper map = PropertyMapper.get();
        CachingConnectionFactory factory = new CachingConnectionFactory(
            getRabbitConnectionFactoryBean(properties).getObject());
        map.from(properties::determineAddresses).to(factory::setAddresses);
        map.from(properties::isPublisherConfirms).to(factory::setPublisherConfirms);
        map.from(properties::isPublisherReturns).to(factory::setPublisherReturns);
        RabbitProperties.Cache.Channel channel = properties.getCache().getChannel();
        map.from(channel::getSize).whenNonNull().to(factory::setChannelCacheSize);
        map.from(channel::getCheckoutTimeout).whenNonNull().as(Duration::toMillis)
            .to(factory::setChannelCheckoutTimeout);
        RabbitProperties.Cache.Connection connection = properties.getCache().getConnection();
        map.from(connection::getMode).whenNonNull().to(factory::setCacheMode);
        map.from(connection::getSize).whenNonNull().to(factory::setConnectionCacheSize);
        map.from(connectionNameStrategy::getIfUnique).whenNonNull().to(factory::setConnectionNameStrategy);
        return factory;
    }
```

```java
RabbitProperties.class // 封装了rabbitmq的所有配置
```

```java
RabbitTemplate // 给rabbitmq发送和接收消息
```

==AmqpAdmin==  **//rabbitmq系统功能管理组件**	

消息转换器，在rabbitmqtemplate中默认使用SimpleMessageConverter

```java
private MessageConverter messageConverter = new SimpleMessageConverter();
```

## 测试的代码

```java
@RunWith(SpringRunner.class)
@SpringBootTest
public class SpringbootRabbitmqApplicationTests {

    @Autowired
    RabbitTemplate rabbitTemplate;

    @Test
    public void contextLoads() {

        //Object 默认当成消息体
        //rabbitTemplate.convertAndSend(exchange,routeKey,Object);
        Map<String, Object> map = new HashMap<>();
        map.put("msg", "这是java发的第一个消息");
        map.put("data", Arrays.asList("hello", "what is your name?"));
        rabbitTemplate.convertAndSend("exchange.direct", "delay", map);

    }

    @Test
    public void receive() {
        Object delay = rabbitTemplate.receiveAndConvert("delay");
        System.out.println(delay.getClass());
        System.out.println(delay);
    }
    @Test
    public void fanout(){

        rabbitTemplate.convertAndSend("exchange.fanout","",new Book("你是谁？","199"));

    }
}

```

启动类

```java
@EnableRabbit
@SpringBootApplication
public class SpringbootRabbitmqApplication {

    public static void main(String[] args) {
        SpringApplication.run(SpringbootRabbitmqApplication.class, args);
    }

}

```

配置类

```java
@Configuration
public class MyAMQPConfig {

    @Bean
    public MessageConverter messageConverter(){
        return new Jackson2JsonMessageConverter();
    }

}
```

service

```java
@Service
public class BookService {

    @RabbitListener(queues = "delay")
    public void  receive(Book book){
        System.out.println(book.toString());
    }
    @RabbitListener(queues = "delay.news")
    public void receive2(Message message){
        System.out.println(message.getBody());
        System.out.println(message.getMessageProperties());
    }

}

```

