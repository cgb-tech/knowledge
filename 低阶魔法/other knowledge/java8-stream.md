# java8-Stream

## 什么是 Stream？

> Stream（流）是一个来自数据源的元素队列并支持聚合操作
>
> - 元素是特定类型的对象，形成一个队列。 Java中的Stream并不会存储元素，而是按需计算。
> - **`数据源`*** 流的来源。 可以是**集合**，**数组**，**I/O channel**， **产生器generator** 等。
> - **``聚合操作``** 类似SQL语句一样的操作， 比如filter, map, reduce, find, match, sorted等。
>
> 和以前的Collection操作不同， Stream操作还有两个基础的特征：
>
> - **`Pipelining`**: 中间操作都会返回流对象本身。 这样多个操作可以串联成一个管道， 如同流式风格（fluent style）。 这样做可以对操作进行优化， 比如延迟执行(laziness)和短路( short-circuiting)。
> - **``内部迭代``**： 以前对集合遍历都是通过Iterator或者For-Each的方式, 显式的在集合外部进行迭代， 这叫做外部迭代。 Stream提供了内部迭代的方式， 通过访问者模式(Visitor)实现

​												 		————————以上是`菜鸟教程`的说明

接下来还是菜鸟教程的内容

首先是生成流的类别

- 串行流 **stream()** 
- 并行流 **parallelStream()** 

## 串行流

首先来看一下一个小的demo

```java
List<String> strings = Arrays.asList("abc", "", "bc", "efg", "abcd", "", "jkl");
List<String> filtered = strings.stream()
                        .filter(string -> !string.isEmpty())
                        .collect(Collectors.toList());
filtered.forEach(System.out::println);

输出结果：
abc
bc
efg
abcd
jkl

```

### forEach

```java
Random random = new Random();
random.ints().limit(10).forEach(System.out::println);

用ints()生成的对象是 IntStream 类型的对象，是int值流
```

- **random.ints()**
  - 用于得到一个**有效无限**的伪随机 int 值流
    - 带有一个参数的时候，得到`传入参数`个的有效无限的伪随机int值流
    - 带有两个参数的时候，得到在这个范围内的int流值，如果不设置limit(个数)的话，会无限生成
- limit()
  - 用来限制生成多少个值（在此处的理解）

### map

map 方法用于映射每个元素到对应的结果，以下代码片段使用 map 输出了元素对应的平方数：

```java
List<Integer> numbers = Arrays.asList(3, 2, 2, 3, 7, 3, 5);
// 获取对应的平方数
List<Integer> squaresList = numbers.stream().map( i -> i*i).distinct().collect(Collectors.toList());
squaresList.forEach(System.out::println);
结果：
9
4
49
25
```

- distinct() 此函数是用来去重的函数

### filter

filter 方法用于通过设置的条件过滤出元素。以下代码片段使用 filter 方法过滤出空字符串：

```java
List<String>strings = Arrays.asList("abc", "", "bc", "efg", "abcd","", "jkl");
// 获取空字符串的数量
long count = strings.stream().filter(string -> string.isEmpty()).count();
```

```java
 List<String> strings = Arrays.asList("abc", "", "bc", "efg", "abcd", "", "jkl");
List<String> list = strings.stream().filter(string->!string.isEmpty()).map(string->
" "+string+" ").collect(Collectors.toList());
list.forEach(System.out::println);
输出结果：
 abc 
 bc 
 efg 
 abcd 
 jkl 
```

可以多个方法链式使用，以达到自己的目的 

collect(Collectors.toList())将自己的流数据冲到list集合里面去

### limit

limit 方法用于获取指定数量的流。 以下代码片段使用 limit 方法打印出 10 条数据：

```java
Random random = new Random();
random.ints().limit(10).forEach(System.out::println);
```

### sorted

sorted 方法用于对流进行排序。以下代码片段使用 sorted 方法对输出的 10 个随机数进行排序：

```java
Random random = new Random();
random.ints().limit(10).sorted().forEach(System.out::println);
输出结果：
-1560117986
-1364889229
-1336273802
-1272156577
-1143677960
-659024975
-520656633
1433415374
1633707646
1972427861

```

- 显然使用sorted()方法对结果集默认使用的是`从小到大`进行排序

```java
/**
* Returns a stream consisting of the elements of this stream in sorted
* order.
*
* <p>This is a <a href="package-summary.html#StreamOps">stateful
* intermediate operation</a>.
*
* @return the new stream
*/
IntStream sorted();
官方给的说明
```

如果想使用`逆序排序`的话

从[此博客](https://blog.csdn.net/qq_34996727/article/details/94472999)上找出了可以实现的方法

由此展开说明

- 首先是如果元素类想使用sorted进行排序，则元素类必须实现`Comparable`接口

- 自然降序则需要使用`Comparator.reverseOrder()`作为sorted的参数

- ```java
  List<Integer> list = Arrays.asList(1,2,3,4,5,6,7);
  list.stream().sorted(Comparator.reverseOrder()).forEach(System.out::println);
  输出结果：
  7
  6
  5
  4
  3
  2
  1
  ```

- 如果想对自己定义的类进行排序，可以参照上方的博客进行细读和研讨

### 统计

另外，一些产生统计结果的收集器也非常有用。它们主要用于int、double、long等基本类型上，它们可以用来产生类似如下的统计结果。

```java
List<Integer> numbers = Arrays.asList(3, 2, 2, 3, 7, 3, 5);

IntSummaryStatistics stats = numbers.stream().mapToInt((x) -> x).summaryStatistics();

System.out.println("列表中最大的数 : " + stats.getMax());
System.out.println("列表中最小的数 : " + stats.getMin());
System.out.println("所有数之和 : " + stats.getSum());
System.out.println("平均数 : " + stats.getAverage());
输出结果：
列表中最大的数 : 7
列表中最小的数 : 2
所有数之和 : 25
平均数 : 3.5714285714285716
```



## 并行（parallel）程序

parallelStream 是流并行处理程序的代替方法。以下实例我们使用 parallelStream 来输出空字符串的数量：

```java
List<String> strings = Arrays.asList("abc", "", "bc", "efg", "abcd", "", "jkl");
// 获取空字符串的数量
long count = strings.parallelStream().filter(string -> string.isEmpty()).count();

System.out.println(count);
输出结果：
2
```

看上去并没有什么不同的，但是既然说并行处理的话，就找多个数据进行输出处理来进行尝试

```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9);
numbers.parallelStream().forEach(System.out::println);
输出结果：
6
3
5
4
2
1
8
7
9
结果显而易见，数序发生了错误，接下来打印线程信息来验证
numbers.parallelStream() .forEach(num->System.out.println(Thread.currentThread().getName()+"->"+num));
输出结果：
ForkJoinPool.commonPool-worker-1->3
ForkJoinPool.commonPool-worker-1->4
ForkJoinPool.commonPool-worker-1->2
ForkJoinPool.commonPool-worker-1->1
ForkJoinPool.commonPool-worker-1->8
ForkJoinPool.commonPool-worker-1->9
ForkJoinPool.commonPool-worker-1->7
ForkJoinPool.commonPool-worker-1->5
main->6
```

根据以上的输出结果可知，`parallelStream`是利用多线程来进行的，可以简化我们对并发操作的使用

[参考](https://blog.csdn.net/yy1098029419/article/details/89452380) `圆圆同学`的博客

剩下的一些以后再做补充，如果有疑问或者看法请随时联系

