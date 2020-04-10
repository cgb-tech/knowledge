### 自动配置的原理(浅层)

```java
@Configuration 		//这是一个配置类
@EnableConfigurationProperties(HttpProperties.class)//启用ConfigurationProperties功能，将配置文件中的值和httpproperties绑定起来，加入到容器中
@ConditionalOnWebApplication(type = ConditionalOnWebApplication.Type.SERVLET)//根据不同的条件来判断是不是生效
@ConditionalOnClass(CharacterEncodingFilter.class)//判断当前项目有没有这个类，乱码过滤器

@ConditionalOnProperty(prefix = "spring.http.encoding", value = "enabled", matchIfMissing = true) //判断配置文件是否存在某些配置；默认已经注入了这些值，不配置也是默认生效的

public class HttpEncodingAutoConfiguration {

    private final HttpProperties.Encoding properties;

    //只有一个有参构造器的时候，从容器中获取
	public HttpEncodingAutoConfiguration(HttpProperties properties) {
		this.properties = properties.getEncoding();
	}
    @Bean//这个组件的某些值需要在properties文件取得
	@ConditionalOnMissingBean
	public CharacterEncodingFilter characterEncodingFilter() {
		CharacterEncodingFilter filter = new OrderedCharacterEncodingFilter();
		filter.setEncoding(this.properties.getCharset().name());
		filter.setForceRequestEncoding(this.properties.shouldForce(Type.REQUEST));
		filter.setForceResponseEncoding(this.properties.shouldForce(Type.RESPONSE));
		return filter;
	}
```

***所有在配置文件中能配置的属性都是在xxxxProperties类中封装***

```java
@ConfigurationProperties(prefix = "spring.http")//从配置文件中获取值与属性绑定
public class HttpProperties {

    /**
	 * Configuration properties for http encoding.
	 */
	public static class Encoding {

		public static final Charset DEFAULT_CHARSET = StandardCharsets.UTF_8;
```



**xxxxAutoConfiguration 自动配置类**

**xxxxProperties properties文件封装配置信息**



@Conditional 用来进行判断的注解  springboot有很多派生的注解

@ConditionaOnBean 

@ConditionaOnMissingBean

...

==自动配置类的条件之后才会生效==

```xml
debug=true #可以打印很多信息，能够查看哪个自动配置类是生效的
```





