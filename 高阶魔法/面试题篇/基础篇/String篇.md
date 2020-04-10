# 前提

## 0、String、StringBuffer、StringBuilder关系

![mark](http://codedorado.oss-cn-beijing.aliyuncs.com/images/20200318/193750283.png)

## 1、String类

```java
public final class String implements java.io.Serializable, Comparable<String>, CharSequence {
    private final char value[];
    private int hash;
}
```

- 是 final 类型的，被初始化之后便不能修改
- 底层是一个 char 数组
- 保存有默认的 hash 值

## 2、StringBuffer

```java
public final class StringBuffer
    extends AbstractStringBuilder
    implements java.io.Serializable, CharSequence
{

    /**
	* A cache of the last value returned by toString. Cleared
	* whenever the StringBuffer is modified.
	*/
    private transient char[] toStringCache;
}
@Override
public synchronized int length() {
    return count;
}
```

- **它是线程安全的**，因为在他的方法中都加了 synchronizde 关键字
- 因为加了 锁 ，所以他的效率低于 StringBuilder

## 3、StringBuilder

```java
public final class StringBuilder
    extends AbstractStringBuilder
    implements java.io.Serializable, CharSequence
{
     public StringBuilder(int capacity) {
        super(capacity);
    }
}
```

- 线程不安全，但是效率高于 StringBuffer

# 区别

## 1、可变与不可变

- String 被 final 关键字所修饰，所以是不可变的 **private final char value[];**
- StringBuffer 与 StringBuilder 都是用的 **char[] value;**所以是可变的

## 2、是否多线程安全

- String 被 final 修饰，不可变，所以多线程安全
- StringBuffer 他的方法都被加了 synchronized 关键字，所以效率低，但是多线程安全
- StringBuilder 效率高，线程不安全

## 3、初始值

- String 初始值可以为null
- Stringbuffer 和 StringBuilder 初始值都不能为 null

---

# String使用陷阱

1、对于 String 类的 equals() 方法来说，它是判断当前字符串与传进来的字符串的内容是否一致。

2、对于 String 对象的相等性判断来说，请使用 equals() 方法，而不是使用 == 。

3、String是常量，其对象一旦创建完毕就无法改变。当使用+拼接字符串时，会生成新的String对象，而不是向原有的String对象追加内容。

---

4、String s="aaa";（采用字面值方式赋值）

```java
public static void main(String[] args) {
    String str1 = "aaa";
    String str2 = "aaa";
    // true
    System.out.println(str1 == str2);
}
```
1. 查找 StringPool(字符串常量池) 是否存在 "aaa" 这个对象，如果不存在则在 StringPool 中创建一个 "aaa" 对象，然后将 StringPool 中的这个 "aaa" 对象的值返回回来，赋值给变量 s ，这样 s 会指向 StringPool 中的这个 "aaa" 字符串对象
2. 如果存在，直接返回 StringPool 中 "aaa" 对象的地址，赋值给 s

---

```java
public static void main(String[] args) {
    String str3 = new String("aaa");
    String str4 = new String("aaa");
    // false
    System.out.println(str3 == str4);
}
```

1. 首先在StringPool中查找有没有"aaa"这个字符串对象，如果有，则不在StringPool中再去创建"aaa"这个对象了，直接在堆中(heap)中创建一个"aaa"字符串对象，然后将堆中的这个"aaa"对象的地址返回来，赋给s引用，导致s指向了堆中创建的这个"aaa"字符串对象。
2. 如果没有，则首先在StringPool中创建一个"aaa"对象，然后再在堆中(heap)创建一个"aaa"对象，然后将堆中这个"aaa"对象的地址返回来，赋值给s引用，导致s指向了堆中所创建的这个"aaa"对象。 

---

![mark](http://codedorado.oss-cn-beijing.aliyuncs.com/images/20200318/201626915.png)