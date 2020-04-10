## MVC

WebMvcAutoConfiguration.java

```java
@ConditionalOnMissingBean(name = "viewResolver", value = ContentNegotiatingViewResolver.class)
public ContentNegotiatingViewResolver viewResolver(BeanFactory beanFactory) {
    ContentNegotiatingViewResolver resolver = new ContentNegotiatingViewResolver();
    resolver.setContentNegotiationManager(beanFactory.getBean(ContentNegotiationManager.class));
    // ContentNegotiatingViewResolver uses all the other view resolvers to locate
    // a view so it should have a high precedence
    resolver.setOrder(Ordered.HIGHEST_PRECEDENCE);
    return resolver;
}

//解析视图
public View resolveViewName(String viewName, Locale locale) throws Exception {
    RequestAttributes attrs = RequestContextHolder.getRequestAttributes();
    Assert.state(attrs instanceof ServletRequestAttributes, "No current ServletRequestAttributes");
    List<MediaType> requestedMediaTypes = getMediaTypes(((ServletRequestAttributes) attrs).getRequest());
    if (requestedMediaTypes != null) {
        //获取候选的视图对象
        List<View> candidateViews = getCandidateViews(viewName, locale, requestedMediaTypes);
        //获取最适合的视图对象
        View bestView = getBestView(candidateViews, requestedMediaTypes, attrs);
        if (bestView != null) {
            return bestView;
        }
    }

    String mediaTypeInfo = logger.isDebugEnabled() && requestedMediaTypes != null ?
        " given " + requestedMediaTypes.toString() : "";

    if (this.useNotAcceptableStatusCode) {
        if (logger.isDebugEnabled()) {
            logger.debug("Using 406 NOT_ACCEPTABLE" + mediaTypeInfo);
        }
        return NOT_ACCEPTABLE_VIEW;
    }
    else {
        logger.debug("View remains unresolved" + mediaTypeInfo);
        return null;
    }
}

```

ContentNegotiatingViewResolver用来组合所有的视图解析器的

```java
protected void initServletContext(ServletContext servletContext) {
    Collection<ViewResolver> matchingBeans =
        //利用BeanFactoryUtils工具获取所有的视图解析器对象
        BeanFactoryUtils.beansOfTypeIncludingAncestors(obtainApplicationContext(), ViewResolver.class).values();
    if (this.viewResolvers == null) {
        this.viewResolvers = new ArrayList<>(matchingBeans.size());
        for (ViewResolver viewResolver : matchingBeans) {
            if (this != viewResolver) {
                this.viewResolvers.add(viewResolver);
            }
        }
    }
```

## 如何修改springboot的默认配置

模式：

​	1、springboot在自动配置很多组件的时候，先看容器中有没有用户自己配置的，如果有用户自己配置的组件，那么就使用用户自己制定的，如果没有，则使用默认的配置；有些组件可以有很多，springboot将用户配置的和默认的组合起来，一起使用。

​	2、利用configruation的注解进行配置

```java
public class MyMvcConfig extends WebMvcConfigurationSupport
//需要继承这个类来实现一些配置类的重写
```

```java
@Configuration
public class MyMvcConfig extends WebMvcConfigurationSupport {
    @Override
    public void addViewControllers(ViewControllerRegistry registry) {
        registry.addViewController("  ").setViewName("success");
    }
}
```

扩展springMvc

添加@EnableWEebMvc 将全面接管mvc