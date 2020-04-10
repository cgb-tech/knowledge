## 2.springboot中的目录结构

### 1、基本信息

	- static ：保存所有的静态资源，css、js、img
	- templates ：保存所有的模版页面（springboot内嵌tomcat，默认不支持jsp）（官方推荐使用thymeleaf）
	- application.properties/yml ：用来保存各种配置信息的文件，修改springboot的默认值



### 2.配置信息

==YML （YAML Ain't Markup Language）一种标记语言，但不仅仅是标记语言，它以数据为中心，更适合做配置文件==

```yaml
server:
  port: 8080
```

使用yml中的数组

```yaml
person:
  - one
  - two
  - three
pserson: [one,two,three]
```

想调用配置文件中的值，需要在配置类上加一些注解**@ConfigurationProperties**

```java
@ConfigurationProperties(prefix = "person") //和配置文件中的信息绑定
```

==在pom文件中导入配置文件处理器==

```java
        <dependency>
            <groupId>maven.org.springframework.boot</groupId>
            <artifactId>spring-boot-configuration-processor</artifactId>
            <optional>true</optional>
        </dependency>
```

**还可以使用@Value的方法进行注入**在属性上加入这个注解

==@Validated 校验注解== @Email 

|                      | @ConfigurationProperties | @Value       |
| -------------------- | ------------------------ | ------------ |
| 功能                 | 批量注入配置文件中的属性 | 一个个的指定 |
| 松散绑定（松散语法） | 支持                     | 不支持       |
| SpEL                 | 不支持                   | 支持         |
| JSR303数据校验       | 支持                     | 不支持       |
| 复杂类型封装         | 支持                     | 不支持       |

### 3.@PropertySource&@ImportResource

​	***@PropertySource(value = {"classpath:a.properties"}) 加载制定的配置文件* **

​	***@ImportResource(locations = {"classpath:a.xml"}) 导入spring的配置文件，让里面的内容生效***

### 4.配置类

```java
@Configuration
public class MyAppConfig {

    @Bean //默认id是方法名
    public HelloService helloService(){
        return new HelloService();
    }
}
```

### 

### 5.profile

 - 多profile文件 启用的时候在只配置文件上加入spring.profiles.active=?(多profile文件的扩展)

 - yml支持多文档块的方式

   ```yaml
   server:
     port: 8080
   spring:
     profiles:
       active: dev
   ---
   server:
     port: 8081
   spring:
     profiles: dev
   ---
   server:
     port: 8082
   spring:
     profiles: prod
   ```

- 可以使用命令行指定的方式







