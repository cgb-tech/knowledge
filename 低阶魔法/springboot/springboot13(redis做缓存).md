# redis做springboot2.x的缓存

## 1.首先是引入依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
<!-- 缓存依赖-->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-cache</artifactId>
</dependency>
<!--spring2.0集成redis所需common-pool2-->
<dependency>
    <groupId>org.apache.commons</groupId>
    <artifactId>commons-pool2</artifactId>
    <version>2.4.2</version>
</dependency>
//下面的可要可不要
<dependency>
    <groupId>redis.clients</groupId>
    <artifactId>jedis</artifactId>
</dependency>
```

## 2. 在配置文件的东西(yaml or properties)

```properties
spring.redis.database=2 //第几个数据库，由于redis中数据库不止一个
spring.redis.host=localhost // 也可指定为127.0.0.1
spring.redis.port=6379 // 默认端口
spring.redis.password= // 默认为空

# springboot2.x以上如此配置，由于2.x的客户端是lettuce
# 单位要带上
spring.redis.lettuce.pool.max-active=8
spring.redis.lettuce.pool.min-idle=0
spring.redis.lettuce.pool.max-idle=8
spring.redis.lettuce.pool.max-wait=10000ms
spring.redis.lettuce.shutdown-timeout=100ms

# springboot1.x如此配置，由于1.x的客户端是jedis
#spring.redis.jedis.pool.max-active=8
#spring.redis.jedis.pool.min-idle=0
#spring.redis.jedis.pool.max-idle=8
#spring.redis.jedis.pool.max-wait=-1
#spring.redis.timeout=500
```

## 3. redisConfig里面的配置

```java
/**
 * Author:delay_boy
 * Date:2019-08-07 18:43
 * Description:(描述)
 */
@Configuration
@EnableCaching
public class RedisConfig extends CachingConfigurerSupport {

    private Logger logger = LoggerFactory.getLogger(getClass());

    /**
     * 这个方法是用来配置key的生成策略的
     * @return KeyGenerator
     */
    @Override
    @Bean
    public KeyGenerator keyGenerator() {

        return (o, method, params) -> {
            StringBuilder sb = new StringBuilder();
            sb.append(o.getClass().getName()); // 类名
            sb.append(method.getName()); // 方法名
            for (Object param: params) {
                sb.append(param.toString()); // 参数名
            }
            logger.info("这里面的内容是:{}",sb.toString());
            return sb.toString();
        };

    }

    @Bean
    public CacheManager cacheManager(RedisConnectionFactory redisConnectionFactory) {
        RedisCacheConfiguration config = RedisCacheConfiguration.defaultCacheConfig()
            .entryTtl(Duration.ofSeconds(3600)) // 60s缓存失效
            // 设置key的序列化方式
            .serializeKeysWith(RedisSerializationContext.SerializationPair.fromSerializer(keySerializer()))
            // 设置value的序列化方式
            .serializeValuesWith(RedisSerializationContext.SerializationPair.fromSerializer(valueSerializer()))
            // 不缓存null值
            .disableCachingNullValues()
            .prefixKeysWith("SysUser");

        RedisCacheManager redisCacheManager = RedisCacheManager.builder(redisConnectionFactory)
            .cacheDefaults(config)
            .transactionAware()
            .build();

        logger.info("自定义RedisCacheManager加载完成");
        return redisCacheManager;
    }

    // key键序列化方式
    private RedisSerializer<String> keySerializer() {
        return new StringRedisSerializer();
    }

    // value值序列化方式
    private GenericJackson2JsonRedisSerializer valueSerializer() {
        return new GenericJackson2JsonRedisSerializer();
    }
}

```

## 4. 在service类里里面的设置

```java
/**
 * Author:delay_boy
 * Date:2019-08-07 19:53
 * Description:(描述)
 */
@Service
public class SysUserService {

    private Logger logger = LoggerFactory.getLogger(getClass());
    @Resource
    private SysUserMapper sysUserMapper;

    @Cacheable(value = "user")
    public List<SysUser> findAllSysUser() {
        logger.info(sysUserMapper.selectAll().toString());
        return sysUserMapper.selectAll();
    }

    @Cacheable(value = "user")
    public SysUser findById(int id) {
        logger.info(sysUserMapper.selectByPrimaryKey(id).toString());
        return sysUserMapper.selectByPrimaryKey(id);
    }

    @CacheEvict("user")
    public void dropById(int id) {
        sysUserMapper.deleteByPrimaryKey(id);
    }

    @CachePut("user")
    public void updateUser(SysUser user) {
        sysUserMapper.updateByPrimaryKey(user);
    }

}

```

## 5.在controller里面的配置

```java
@RestController
public class UserController {

    @Autowired
    private SysUserService sysUserService;

    @RequestMapping("/findall")
    public List<SysUser> findall() {
        return sysUserService.findAllSysUser();
    }
    @RequestMapping("/findbyid/{id}")
    public SysUser findbyid(@PathVariable("id") int id) {
        return sysUserService.findById(id);
    }
    @RequestMapping("/deletebyid/{id}")
    public String deletebyid(@PathVariable("id") int id) {
        sysUserService.dropById(id);
        return "ok";
    }

}

```

项目的demo已经上传到github中,可以自行下载测试

https://github.com/lkr-china/springboot-redis

