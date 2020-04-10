### 1.首先加入的是放在generatorConfig文件夹下的`generatorConfig.xml`文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE generatorConfiguration
        PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
        "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">

<generatorConfiguration>
    <!--注意这里的targetRuntime="MyBatis3Simple"，指定了不生成Example相关内容-->
    <context id="MysqlTables" targetRuntime="MyBatis3Simple">

        <commentGenerator>
            <property name="suppressDate" value="true"/>
            <!-- 是否去除自动生成的注释 true：是 ： false:否 -->
            <property name="suppressAllComments" value="true"/>
        </commentGenerator>

        <!-- 生成一对一配置 -->
        <!--<plugin type="cc.bandaotixi.plugins.OneToOnePlugin" />-->
        <!-- 生成一对多配置 -->
        <!--<plugin type="cc.bandaotixi.plugins.OneToManyPlugin" />-->
        <!-- 生成批量配置 -->
        <!--<plugin type="cc.bandaotixi.plugins.BatchInsertPlugin" />
        <plugin type="cc.bandaotixi.plugins.BatchUpdatePlugin" />-->

        <!-- jdbc链接信息 -->
        <jdbcConnection driverClass="com.mysql.cj.jdbc.Driver"
                        connectionURL="jdbc:mysql://localhost:3306/springboot?useUnicode=true&amp;useJDBCCompliantTimezoneShift=true&amp;useLegacyDatetimeCode=false&amp;serverTimezone=UTC"
                        userId="root" password="123456">
        </jdbcConnection>

        <javaTypeResolver>
            <property name="forceBigDecimals" value="false"/>
        </javaTypeResolver>

        <!-- 生成PO类的位置 -->
        <javaModelGenerator targetPackage="com.delay.po"
                            targetProject="src/main/java">
            <property name="enableSubPackages" value="true"/>
            <property name="trimStrings" value="true"/>
        </javaModelGenerator>

        <!-- mapper映射文件生成的位置 -->
        <sqlMapGenerator targetPackage="mapper"
                         targetProject="src/main/resources">
            <property name="enableSubPackages" value="true"/>
        </sqlMapGenerator>

        <!-- mapper接口生成的位置 -->
        <javaClientGenerator type="XMLMAPPER"
                             targetPackage="com.delay.mapper"
                             targetProject="src/main/java">
            <property name="enableSubPackages" value="true"/>
        </javaClientGenerator>

        <!-- 指定要生成的表 -->
        <table tableName="sys_user" domainObjectName="SysUser">
            <property name="useActualColumnNames" value="true"/>
            <generatedKey column="id" sqlStatement="MySql" identity="true"/>
        </table>

        <table tableName="sys_role" domainObjectName="SysRole">
            <property name="useActualColumnNames" value="true"/>
            <generatedKey column="id" sqlStatement="MySql" identity="true"/>
        </table>

        <table tableName="sys_permission" domainObjectName="SysPermission">
            <property name="useActualColumnNames" value="true"/>
            <generatedKey column="id" sqlStatement="MySql" identity="true"/>
        </table>
    </context>
</generatorConfiguration>
```

里面的很多东西是根据自己的表来设置的

### 2.在pom.xml文件中的plugins中加入插件

```xml
<plugin>
    <groupId>org.mybatis.generator</groupId>
    <artifactId>mybatis-generator-maven-plugin</artifactId>
    <version>1.3.2</version>
    <configuration>
        <configurationFile>src/main/resources/generatorConfig/generatorConfig.xml</configurationFile>
        <verbose>true</verbose>
        <overwrite>true</overwrite>
    </configuration>
    <!--mybatise-generator-->
    <dependencies>
        <dependency>
            <groupId>org.mybatis.generator</groupId>
            <artifactId>mybatis-generator-core</artifactId>
            <version>1.3.2</version>
        </dependency>
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>8.0.16</version>
        </dependency>
    </dependencies>
</plugin>

```

### 3.最后是启动插件

![1565191590560](C:\Users\888\AppData\Roaming\Typora\typora-user-images\1565191590560.png)

### 4. 附项目的目录结构

![1565191569588](C:\Users\888\AppData\Roaming\Typora\typora-user-images\1565191569588.png)

### 5.自己的properties文件的内容

```properties
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
spring.datasource.url=jdbc:mysql://localhost:3306/springboot?useUnicode=true&useJDBCCompliantTimezoneShift=true&useLegacyDatetimeCode=false&serverTimezone=UTC
spring.datasource.username=root
spring.datasource.password=123456


mybatis.mapper-locations=classpath:mapper/*.xml
mybatis.type-aliases-package=com.delay.po
```

