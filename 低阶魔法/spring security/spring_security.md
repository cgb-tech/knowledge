- 我们可以在方法上加上权限注解，如下：
  
  ```java
  import org.springframework.security.access.prepost.PreAuthorize;
  import org.springframework.web.bind.annotation.RequestMapping;
  import org.springframework.web.bind.annotation.RestController;
  
  @RestController
  public class HomeController {
  
      @RequestMapping("/home")
      @PreAuthorize("hasRole('ADMIN')")
      public  String  home() {
          return "hello world";
      }
  }
  ```
  
  常用权限
  
  | 表达式                         | 描述                                                         |
  | ------------------------------ | ------------------------------------------------------------ |
  | hasRole([role])                | 当前用户是否拥有指定角色。                                   |
  | hasAnyRole([role1,role2])      | 多个角色是一个以逗号进行分隔的字符串。如果当前用户拥有指定角色中的任意一个则返回true。 |
  | hasAuthority([auth])           | 等同于hasRole                                                |
  | hasAnyAuthority([auth1,auth2]) | 等同于hasAnyRole                                             |
  | Principle                      | 代表当前用户的principle对象                                  |
  | authentication                 | 直接从SecurityContext获取的当前Authentication对象            |
  | permitAll                      | 总是返回true，表示允许所有的                                 |
  | denyAll                        | 总是返回false，表示拒绝所有的                                |
  | isAnonymous()                  | 当前用户是否是一个匿名用户                                   |
  | isRememberMe()                 | 表示当前用户是否是通过Remember-Me自动登录的                  |
  | isAuthenticated()              | 表示当前用户是否已经登录认证成功了。                         |
  | isFullyAuthenticated()         | 如果当前用户既不是一个匿名用户，同时又不是通过Remember-Me自动登录的，则返回true。 |



现在遇到的问题需要解决的:

- ==自己的登陆如何实现==
  
- 这一题放在最后的一个里面,不可以单独实现loginPage,如果实现也可以,需要在登陆的form表单里面的action中设置为/login
  
- 怎么获取他的role而不是permission

- ==他的验证环节怎么实现的==

- 用户的信息都被存放在那里了

- session的下线

  - 多人同时在线的问题解决

  - ```java
    .and()
        .sessionManagement()
        .maximumSessions(1) //最多多少个session同时在线
        .expiredUrl("/gologin") //被挤掉之后回到哪个请求
        .maxSessionsPreventsLogin(false);// 当达到maximumSessions时，true表示不能踢掉前面的登录，false表示踢掉前面的用户
    // .expiredSessionStrategy(new LzcExpiredSessionStrategy());// 当达到maximumSessions时，踢掉前面登录用户后的操作;
    ```

- ==记住我的时间修改不了,还是四个小时==

  - **这个问题我用debug的方式跟进去之后,发现我的值已经被赋值进去了,但是他的确是没有生效,可能是我默认配置的remember-me让这个方法失效了,现在开始使用自己配制的试试能不能修改成功**
  - **发现数据库的内容有些不对,开始研究自动生成表的内容**
  - **经过对浏览器的cookie的审查,发现数据库的信息可能是因为时区的问题把日期给修改的与本地时间不一样,但是cookie的信息是跟自己设置的一般无二**

- ==.loginPage和loginProcessingUrl的区别==

  - **这个区别的话,做了一个小的测试,发现如果之配置.loginPage的话,他不会默认给你找login页面,他会找你已经在controller里配置好的路径,然后跟着那个路径走**

  - **loginProcessingUrl的话,如果你配置了这个,你的controller里面没有/login的处理方法,那么他就会默认找系统提供的/login,找到系统提供的页面**

  - ```java
    @Override
    protected RequestMatcher createLoginProcessingUrlMatcher(String loginProcessingUrl) {
        return new AntPathRequestMatcher(loginProcessingUrl, "POST");
    }
    //这是loginProcessingUrl调用的方法
    //它不会将请求传递给Spring MVC和您的控制器。
    ```

  - 在loginPage里面设置自己的登陆请求,然后再loginProcessingUrl中设置页面提交的地址,这样就可以使用自己的登陆手段

- 在做remember-me的时候,重新进入之后,没有权限的页面需要重新登陆才有没有权限的提示



