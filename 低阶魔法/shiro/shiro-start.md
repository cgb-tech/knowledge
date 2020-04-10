添加依赖（springboot中）spring 和 shiro 的整合依赖

```xml
<dependency>
    <groupId>org.apache.shiro</groupId>
    <artifactId>shiro-spring</artifactId>
    <version>1.4.1</version>
</dependency>

```

shiro.ini配置文件的信息

```ini
[users]
lkr=1234
```

测试代码

```java
@Test
public void contextLoads() {

    Factory<SecurityManager> factory = new IniSecurityManagerFactory("classpath:shiro.ini");
    SecurityManager instance = factory.getInstance();
    SecurityUtils.setSecurityManager(instance);
    Subject subject = SecurityUtils.getSubject();

    UsernamePasswordToken token = new UsernamePasswordToken("lkr1", "1234");

    try {
        subject.login(token);
        System.out.println(subject.isAuthenticated());
    } catch (UnknownAccountException e) {
        e.printStackTrace();
    }catch (IncorrectCredentialsException){
        System.out.println("密码错误");
    }

    subject.logout();
    System.out.println(subject.isAuthenticated());

}
```

