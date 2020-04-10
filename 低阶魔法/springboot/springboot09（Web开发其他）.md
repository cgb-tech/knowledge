## 1、错误处理机制

1. 默认效果：返回一个错误页面
2. 如果是其他客户端，默认相应一个json数据

```java
@Configuration
@ConditionalOnWebApplication(type = Type.SERVLET)
@ConditionalOnClass({ Servlet.class, DispatcherServlet.class })
// Load before the main WebMvcAutoConfiguration so that the error View is available
@AutoConfigureBefore(WebMvcAutoConfiguration.class)
@EnableConfigurationProperties({ ServerProperties.class, ResourceProperties.class, WebMvcProperties.class })
public class ErrorMvcAutoConfiguration {

```

```java

@Bean
@ConditionalOnMissingBean(value = ErrorAttributes.class, search = SearchStrategy.CURRENT)
public DefaultErrorAttributes errorAttributes() {
    return new DefaultErrorAttributes(this.serverProperties.getError().isIncludeException());
}

@Bean
@ConditionalOnMissingBean(value = ErrorController.class, search = SearchStrategy.CURRENT)
public BasicErrorController basicErrorController(ErrorAttributes errorAttributes) {
    return new BasicErrorController(errorAttributes, this.serverProperties.getError(), this.errorViewResolvers);
}

@Bean
public ErrorPageCustomizer errorPageCustomizer() {
    return new ErrorPageCustomizer(this.serverProperties, this.dispatcherServletPath);
}
@Bean
@ConditionalOnBean(DispatcherServlet.class)
@ConditionalOnMissingBean
public DefaultErrorViewResolver conventionErrorViewResolver() {
    return new DefaultErrorViewResolver(this.applicationContext, this.resourceProperties);
}
```

- DefaultErrorAttributes

  //帮我们在页面共享信息

- BasicErrorController

- ErrorPageCustomizer

- DefaultErrorViewResolver

```java
@Override
public void registerErrorPages(ErrorPageRegistry errorPageRegistry) {
    ErrorPage errorPage = new ErrorPage(
        this.dispatcherServletPath.getRelativePath(this.properties.getError().getPath()));
    errorPageRegistry.addErrorPages(errorPage);
}
//定制error相应
	@Value("${error.path:/error}")
	private String path = "/error";
//默认路径是"/error"
```

BasicErrorController

```java
//处理默认的“/error”请求
@Controller
@RequestMapping("${server.error.path:${error.path:/error}}")
public class BasicErrorController extends AbstractErrorController {

    public BasicErrorController(ErrorAttributes errorAttributes, ErrorProperties errorProperties, List<ErrorViewResolver> errorViewResolvers) {
        super(errorAttributes, errorViewResolvers);
        Assert.notNull(errorProperties, "ErrorProperties must not be null");
        this.errorProperties = errorProperties;
    }
    //    public static final String TEXT_HTML_VALUE = "text/html";
  	// 产生html数据
    @RequestMapping(produces = MediaType.TEXT_HTML_VALUE)
    public ModelAndView errorHtml(HttpServletRequest request, HttpServletResponse response) {
        HttpStatus status = getStatus(request);
        Map<String, Object> model = Collections.unmodifiableMap(getErrorAttributes(request, isIncludeStackTrace(request, MediaType.TEXT_HTML)));
        response.setStatus(status.value());
        //去哪个页面作为错误页面；modelAndView包含错误页面和内容
        ModelAndView modelAndView = resolveErrorView(request, response, status, model);
        return (modelAndView != null) ? modelAndView : new ModelAndView("error", model);
    }

    @RequestMapping //产生json数据
    public ResponseEntity<Map<String, Object>> error(HttpServletRequest request) {
        Map<String, Object> body = getErrorAttributes(request, isIncludeStackTrace(request, MediaType.ALL));
        HttpStatus status = getStatus(request);
        return new ResponseEntity<>(body, status);
    }

```

1. 有模版引擎的情况下，回来到“/error”下找到状态码的页面，如果没有相对应的错误页面，可以使用4xx或者5xx页面当作错误页面，优先使用精确的。

---

## 2、嵌入式servlet容器

![1563935174950](C:\Users\888\AppData\Roaming\Typora\typora-user-images\1563935174950.png)

- 在spring boot中会有非常多的xxxConfigurer帮助我们进行配置扩展
- 在spring boot中会有很多xxxCustomizer帮组我们定制配置



























