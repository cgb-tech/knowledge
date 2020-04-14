# Spring 整合 Mybatis 的步骤

如果按照从`mybatis`的`MapperScan`进入的话步骤如下:

```java
@MapperScan 
@Import(MapperScannerRegistrar.class)
```

```java
public class MapperScannerRegistrar implements ImportBeanDefinitionRegistrar, ResourceLoaderAware {
```

```txt
利用spring的扫描逻辑,扫描指定包路径得到BeanDefinition，BeanDefinition里的
beanClass为接口
Mybatis处理BeanDefinition，修改当前beanClass为mapperFactoryBean
通过getSqlSession0.getMapper(this.mapperInterface)拿到Mybatis的代理对象
```



## 1、首先是MapperScan定义要扫描的包

```java
@Retention(RetentionPolicy.RUNTIME)
@Target({ElementType.TYPE})
@Documented
// 首先是在程序运行的时候导入这个类，运行期中定义的方法
@Import({MapperScannerRegistrar.class})
@Repeatable(MapperScans.class)
public @interface MapperScan {
    String[] value() default {};
    // ... 
}
```

## 2、MapperScannerRegistrar.class

```java
/**
 * @see MapperFactoryBean
 */
public class MapperScannerRegistrar implements ImportBeanDefinitionRegistrar, ResourceLoaderAware {

    @Override
    public void registerBeanDefinitions(AnnotationMetadata importingClassMetadata, BeanDefinitionRegistry registry) {
        // 利用 spring 中的包扫描技术将扫描出来的集合传给接下来的方法
        AnnotationAttributes mapperScanAttrs = AnnotationAttributes
            .fromMap(importingClassMetadata.getAnnotationAttributes(MapperScan.class.getName()));
        if (mapperScanAttrs != null) {
            registerBeanDefinitions(mapperScanAttrs, registry, generateBaseBeanName(importingClassMetadata, 0));
        }
    }
    void registerBeanDefinitions(AnnotationAttributes annoAttrs, BeanDefinitionRegistry registry, String beanName) {

        BeanDefinitionBuilder builder = BeanDefinitionBuilder.genericBeanDefinition(MapperScannerConfigurer.class);
        builder.addPropertyValue("processPropertyPlaceHolders", true);
        // ... 
        List<String> basePackages = new ArrayList<>();
        // 将扫描到的类的名字都加入到 basePackages 集合中去 
        basePackages.addAll(
            Arrays.stream(annoAttrs.getStringArray("value")).filter(StringUtils::hasText).collect(Collectors.toList()));

        basePackages.addAll(Arrays.stream(annoAttrs.getStringArray("basePackages")).filter(StringUtils::hasText)
                            .collect(Collectors.toList()));

        basePackages.addAll(Arrays.stream(annoAttrs.getClassArray("basePackageClasses")).map(ClassUtils::getPackageName)
                            .collect(Collectors.toList()));
    }
    

    
```

## 3、利用spring中的包扫描

```java
package org.springframework.core.annotation;
public class AnnotationAttributes extends LinkedHashMap<String, Object> {
    @Nullable
    public static AnnotationAttributes fromMap(@Nullable Map<String, Object> map) {
        if (map == null) {
            return null;
        }
        if (map instanceof AnnotationAttributes) {
            return (AnnotationAttributes) map;
        }
        return new AnnotationAttributes(map);
    }
}
```

## 4、MapperFactoryBean

```java
public class MapperFactoryBean<T> extends SqlSessionDaoSupport implements FactoryBean<T> {
    public MapperFactoryBean(Class<T> mapperInterface) {
        this.mapperInterface = mapperInterface;
    }
    @Override
    public T getObject() throws Exception {
        // 利用sqlSession的getMapper方法的到对应的对象
        return getSqlSession().getMapper(this.mapperInterface);
    }
}
```

## 5、MapperScannerConfigurer 

```java
public class MapperScannerConfigurer implements BeanDefinitionRegistryPostProcessor ... {
   
    @Override
    public void postProcessBeanDefinitionRegistry(BeanDefinitionRegistry registry) {
        if (this.processPropertyPlaceHolders) {
            processPropertyPlaceHolders();
        }

        ClassPathMapperScanner scanner = new ClassPathMapperScanner(registry);
        scanner.setAddToConfig(this.addToConfig);
        scanner.setAnnotationClass(this.annotationClass);
        scanner.setMarkerInterface(this.markerInterface);
        scanner.setSqlSessionFactory(this.sqlSessionFactory);
        scanner.setSqlSessionTemplate(this.sqlSessionTemplate);
        scanner.setSqlSessionFactoryBeanName(this.sqlSessionFactoryBeanName);
        scanner.setSqlSessionTemplateBeanName(this.sqlSessionTemplateBeanName);
        scanner.setResourceLoader(this.applicationContext);
        scanner.setBeanNameGenerator(this.nameGenerator);
        scanner.setMapperFactoryBeanClass(this.mapperFactoryBeanClass);
        if (StringUtils.hasText(lazyInitialization)) {
            scanner.setLazyInitialization(Boolean.valueOf(lazyInitialization));
        }
        scanner.registerFilters();
        scanner.scan(
            StringUtils.tokenizeToStringArray(this.basePackage, ConfigurableApplicationContext.CONFIG_LOCATION_DELIMITERS));
    }

}
```

这里我们重点关注三个主要的方法，分别是

```java
processPropertyPlaceHolders();
scanner.registerFilters();
scanner.scan(StringUtils.tokenizeToStringArray(this.basePackage, ConfigurableApplicationContext.CONFIG_LOCATION_DELIMITERS));
```

## processPropertyPlaceHolders属性处理

执行属性的处理，简单的说，就是把xml中${XXX}中的XXX替换成属性文件中的相应的值

## 根据配置属性生成过滤器

scanner.registerFilters();方法会根据配置的属性生成对应的过滤器，然后这些过滤器在扫描的时候会起作用。

## 扫描java文件

scanner.scan(StringUtils.tokenizeToStringArray(this.basePackage, ConfigurableApplicationContext.CONFIG_LOCATION_DELIMITERS));
 该方法主要做了以下操作：
 1）扫描basePackage下面的java文件
 2）解析扫描到的java文件
 3）调用各个在上一步骤注册的过滤器，执行相应的方法。
 4）为解析后的java注册bean，注册方式采用编码的动态注册实现。
 5）构造MapperFactoryBean的属性，mapperInterface，sqlSessionFactory等等，填充到BeanDefinition里面去。