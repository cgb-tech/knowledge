# java锁

`why`？为了保证多个线程更新一个资源时，防止数据冲突和脏乱，做到`线程安全`

|          | **乐观锁**                                   | **悲观锁**                                                   |
| -------- | -------------------------------------------- | ------------------------------------------------------------ |
| 定义     | 不加锁，但是依据是否有被修改过来判断失败与否 | 加锁，锁住资源不让其他线程操作，保证只有占有锁的线程去更新资源 |
| 区别     | 不加锁                                       | 加锁                                                         |
| 适用场景 | 大量读取（写入较少）                         | 大量写入                                                     |
| 实现举例 | CAS、java.util.concurrent.atomic包下的类     | Synchronized、ReentrantLock                                  |

- CAS解释：全名：compare and swap，先比较然后设置，CAS是一个原子操作
- 适用场景：更新一个值，不依赖于加锁实现，可以接受CAS失败
- 局限：只可以更新一个值，如AtomicReference、AtomicInteger需要同时更新时，无法做到原子性

AtomicInteger类中的compareAndSwapInt方法是native方法
是调用JNI的方法，底层是通过一个CPU指令完成

`CAS的自旋问题`

![1580970077062](C:\Users\888\AppData\Roaming\Typora\typora-user-images\1580970077062.png)![1580970083092](C:\Users\888\AppData\Roaming\Typora\typora-user-images\1580970083092.png)

导致的结果：如果CAS不成功，则会原地自旋，长时间自旋会给CPU带来非常大的开销

`CAS的ABA问题`

![1580970122644](C:\Users\888\AppData\Roaming\Typora\typora-user-images\1580970122644.png)

# 数据库悲观锁乐观锁实现

## `数据库悲观锁`

- SELECT ... LOCK IN SHARE MODE
- SELECT ... FOR UPDATE

### SELECT ... `LOCK IN SHARE MODE`

- 共享锁，在事务内生效
- 给符合条件的行添加的是共享锁，其他事务会话同样可以继续给这些行添加共享锁，在锁释放前，其他事务
- 法对这些行进行删除和修改；
- 两个事务同时对一行加共享锁后，无法更新，直到只有一个事务对该行加共享锁；
- 如果两个加了共享锁的事务同时更新一行，会发生Deadlock死锁问题；
- 某行已有排它锁，无法继续添加共享锁
- 不会阻塞正常读

### SELECT ... `FOR UPDATE`

- 排它锁，在事务内生效
- 给符合条件的行添加的是排它锁，其他事务无法再加排它锁，在释放前，其他事务无法对这些行进行删除和修改
- 某行已有共享锁，无法继续添加排它锁
- 第一个事务对某行加了排它锁，第二个事务继续加排它锁，第二个事务需要等待
- 加锁有超时时间
- 不会阻塞正常读

## 数据库乐观锁

UPDATE set … `version = version + 1 where version = $version$`

- CAS思路，使用version版本控制，保证同一时间只有一个事务可以更新成功
- 根据影响的行数来判断是否更新成功，更新失败的继续重新获取version值更新，可以设置最大重试次数



电商下单的库存更新
使用更新语句：update product_stock set number= number – 1 where product_id = $productId$ and number – 1 >= 0
判断更新影响的行数来成功还是失败

--------

# java锁(*)

## 锁改变的是对象的什么？对象头

锁---sync---上锁就是改变对象的对象头`reentrantLock`

reentrantLock是什么？---java的对象布局---java对象由什么组成

1. `java对象的实例数据`---不固定
2. `对象头`---固定
3. `数据对齐`---jvm要求是8字节的倍数

`深入jvm虚拟机(book)` `java并发编程艺术`

### 什么是java对象头

对象的第一个部分、所有对象公共部分

### java对象头是由什么组成的

- mark word  64bit
- klass pointer/class MetaData Address  32bit(虚拟机开启指针压缩后)/64bit(不压缩)

### 使用jol来查看具体的`对象结构`

```xml
<!-- https://mvnrepository.com/artifact/org.openjdk.jol/jol-core -->
<dependency>
    <groupId>org.openjdk.jol</groupId>
    <artifactId>jol-core</artifactId>
    <version>0.9</version>
</dependency>

```

```java
Integer.toHexString(l.hashCode());
ClassLayout.parseInstance(l).toPrintable();
```



### 什么是JVM？什么是HotSpot(TM)？

JVM是一种规范/标准

hostpot是对标准的实现

## 使用synchronized关键字对象有几种状态

1. 无状态 刚new出来
2. 偏向锁
3. 轻量锁
4. 重量锁
5. gc标记





























​                                      