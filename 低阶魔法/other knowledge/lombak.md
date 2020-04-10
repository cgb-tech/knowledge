## Lombok 

首先是几个常用的注解（最常用到的方法,超简单的用）

```java
@Data
@AllArgsConstructor
@NoArgsConstructor
public class Trial_Process {

    private Integer trial_process_id;

    private Integer project_id;

    private Integer step_one;

    private Integer step_two;

    private Integer step_three;

    private Integer step_four;

    private Integer step_five;

    private Integer step_six;

    private Integer step_seven;

    private String trial_process_remark;
}
```

```java
@Getting 
@Setting
@ToString
@AllArgsConstructor
@NoArgsConstructor
@Data
```

#### @Getting

将此注解加在类的上方，可以对此类中的所有属性自动生成get方法

#### @Setting

将此注解加在类的上方，可以对此类中的所有属性自动生成set方法

#### @ToString

该注解使用在**类**上，该注解默认生成任何非讲台字段以名称-值的形式输出。
1、如果需要可以通过注释参数`includeFieldNames`来控制输出中是否包含的属性名称。
2、可以通过`exclude`参数中包含字段名称，可以从生成的方法中排除特定字段。
3、可以通过`callSuper`参数控制父类的输出。

#### @AllArgsConstructor

该注解使用在**类**上，该注解提供一个全参数的构造方法，默认不提供无参构造。

#### @NoArgsConstructor

该注解使用在**类**上，使用类中所有带有 `@NonNull` 注解的或者带有 `final` 修饰的成员变量生成对应的构造方法。

#### @Data

该注解使用在**类**上，该注解是最常用的注解，它结合了`@ToString`，`@EqualsAndHashCode`， `@Getter`和`@Setter`。

