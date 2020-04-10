

```java
xxxxAutoConfiguration
xxxxproperties
```

## 对静态资源的映射规则

1. webjars

   ```java
   @ConfigurationProperties(prefix = "spring.resources", ignoreUnknownFields = false)
   public class ResourceProperties {
       //可以设置与资源有关的参数，比如 缓存时间
   ```

   

```java
public class WebMvcAutoConfiguration {

    public static final String DEFAULT_PREFIX = "";

    public static final String DEFAULT_SUFFIX = "";

    private static final String[] SERVLET_LOCATIONS = { "/" };

    @Overwrite//添加资源映射,利用maven引入依赖可以使用一些资源，webjars
    public void addResourceHandlers(ResourceHandlerRegistry registry) {
        if (!this.resourceProperties.isAddMappings()) {
            logger.debug("Default resource handling disabled");
            return;
        }
        Duration cachePeriod = this.resourceProperties.getCache().getPeriod();
        CacheControl cacheControl = this.resourceProperties.getCache().getCachecontrol().toHttpCacheControl();
        if (!registry.hasMappingForPattern("/webjars/**")) {
            //任何webjars之后的请求都去classpath:/META-INF/resources/webjars/下找
            customizeResourceHandlerRegistration(registry.addResourceHandler("/webjars/**")
                                                 .addResourceLocations("classpath:/META-INF/resources/webjars/")
                                                 .setCachePeriod(getSeconds(cachePeriod)).setCacheControl(cacheControl));
        }
        String staticPathPattern = this.mvcProperties.getStaticPathPattern();
        if (!registry.hasMappingForPattern(staticPathPattern)) {
            customizeResourceHandlerRegistration(registry.addResourceHandler(staticPathPattern)
                                                 .addResourceLocations(getResourceLocations(this.resourceProperties.getStaticLocations()))
                                                 .setCachePeriod(getSeconds(cachePeriod)).setCacheControl(cacheControl));
        }
        //欢迎页的映射
        @Bean
        public WelcomePageHandlerMapping welcomePageHandlerMapping(ApplicationContext applicationContext) {
            return new WelcomePageHandlerMapping(new TemplateAvailabilityProviders(applicationContext),
                                                 applicationContext, getWelcomePage(), this.mvcProperties.getStaticPathPattern());
        }

        //配置喜欢的图标
        @Configuration
        @ConditionalOnProperty(value = "spring.mvc.favicon.enabled", matchIfMissing = true)
        public static class FaviconConfiguration implements ResourceLoaderAware {

            private final ResourceProperties resourceProperties;

            private ResourceLoader resourceLoader;

            public FaviconConfiguration(ResourceProperties resourceProperties) {
                this.resourceProperties = resourceProperties;
            }

            @Override
            public void setResourceLoader(ResourceLoader resourceLoader) {
                this.resourceLoader = resourceLoader;
            }

            @Bean
            public SimpleUrlHandlerMapping faviconHandlerMapping() {
                SimpleUrlHandlerMapping mapping = new SimpleUrlHandlerMapping();
                mapping.setOrder(Ordered.HIGHEST_PRECEDENCE + 1);
                mapping.setUrlMap(Collections.singletonMap("**/favicon.ico", faviconRequestHandler()));
                return mapping;
            }

            @Bean
            public ResourceHttpRequestHandler faviconRequestHandler() {
                ResourceHttpRequestHandler requestHandler = new ResourceHttpRequestHandler();
                requestHandler.setLocations(resolveFaviconLocations());
                return requestHandler;
            }

            private List<Resource> resolveFaviconLocations() {
                String[] staticLocations = getResourceLocations(this.resourceProperties.getStaticLocations());
                List<Resource> locations = new ArrayList<>(staticLocations.length + 1);
                Arrays.stream(staticLocations).map(this.resourceLoader::getResource).forEach(locations::add);
                locations.add(new ClassPathResource("/"));
                return Collections.unmodifiableList(locations);
            }

        }
        ...
    }
```

```xml
<!-- https://mvnrepository.com/artifact/org.webjars/jquery -->
//引入的依赖jq
<dependency>
    <groupId>org.webjars</groupId>
    <artifactId>jquery</artifactId>
    <version>3.4.1</version>
</dependency>

```

2. "/**" 访问当前项目的任何资源（静态资源的文件夹）

```java
private static final String[] CLASSPATH_RESOURCE_LOCATIONS = { "classpath:/META-INF/resources/","classpath:/resources/", "classpath:/static/", "classpath:/public/" };

```

```java
@Bean
public WelcomePageHandlerMapping welcomePageHandlerMapping(ApplicationContext applicationContext) {
    return new WelcomePageHandlerMapping(new TemplateAvailabilityProviders(applicationContext),
                                         applicationContext, getWelcomePage(), this.mvcProperties.getStaticPathPattern());
}

	public String getStaticPathPattern() {
		return this.staticPathPattern;
	}
	//private String staticPathPattern = "/**";

//欢迎页的数组
private Optional<Resource> getWelcomePage() {
    String[] locations = getResourceLocations(this.resourceProperties.getStaticLocations());
    return Arrays.stream(locations).map(this::getIndexHtml).filter(this::isReadable).findFirst();
}

//静态资源文件夹下的所有index.html ，被/**映射
private Resource getIndexHtml(String location) {
    return this.resourceLoader.getResource(location + "index.html");
}
```

图标**/favicon.ico：

```java
@Bean
public ResourceHttpRequestHandler faviconRequestHandler() {
    ResourceHttpRequestHandler requestHandler = new ResourceHttpRequestHandler();
    requestHandler.setLocations(resolveFaviconLocations());
    return requestHandler;
}

private List<Resource> resolveFaviconLocations() {
    String[] staticLocations = getResourceLocations(this.resourceProperties.getStaticLocations());
    List<Resource> locations = new ArrayList<>(staticLocations.length + 1);
    Arrays.stream(staticLocations).map(this.resourceLoader::getResource).forEach(locations::add);
    locations.add(new ClassPathResource("/"));
    return Collections.unmodifiableList(locations);
}
```

