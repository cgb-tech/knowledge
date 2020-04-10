## 五、谈谈你对OOM的认识

1. StackOverFlowError

2. OOM:java heap space
3. OOM:GC overhead limit exceeded
4. OOM:Direct buffer memory
5. OOM:unable to create new native thread
6. OOM:metaspace

---

### 1.java.lang.StackOverflawError

#### 1.1 循环引用

```java
public void stackOverFlowError() {
    stackOverFlowError();
}
```

---

### 2.java.lang.OutOfMemoryError:java heap space

```java
byte[] b = new byte[30 * 1024 * 1024]
//-Xms10m -Xmx10m
```

---

### 3.java.lang.OutOfMemoryError:GC overhead limit exceeded

- GC超过了最大的极限
- GC回收时间过长会跑出这个错误，过长的定义是，超过98%的时间用来做GC并且回收了不到2%的堆内存
- 连续多次GC都只回收了不到2%的极端情况下才会抛出。假如不抛出GC overhead limit错误的话，GC清理的少量内存将会再次被填满，迫使GC再次执行，形成恶性循环，CPU使用率一直是100%，而GC没有任何结果

```java
public static void main(String[] args) {

    int i = 0;
    List<String> list = new ArrayList<>();

    try {
        while (true) {
            list.add(String.valueOf(++i).intern());
        }
    } catch (Throwable e) {
        System.out.println(i);
        throw e;
    }
}
//-Xms10m -Xmx10m -XX:MaxDirectMemorySize=5m -XX:+PrintGCDetails
```

---

### 4.java.lang.OutOfMemoryError:Direct buffer memory

- 在做NIO程序的时候会经常出现
- 导致原因
  - *写NIO程序经常使用ByteBuffer来读取或者写入数据，这是一 种基于通道(Channel)与缓冲区(Buffer)的I/0方式,*它可以使用Native函数库直接分配堆外内存，然后通过一 个存储在Java堆里面的DirectByteBuffer对象作为这块内存的引用进行操作。这样能在一些场景中显著提高性能，因为避免了在Java堆和Native堆中来回复制数据。
  - ByteBuffer.allocate(capability)第一种 方式是分配VM堆内存，属FGC管辖范围，由于需要拷贝所以速度相对较慢
  - ByteBuffer.allocteDirect(capability)第一.种方式是 分配oS本地内存，不属FGC管辖范围，由于不需要内存拷贝所以速度相对较快。
  - 但如果 不断分配本地内存，堆内存很少使用，那么JVM就 不需要执行GC, DirectByteBuffer对象们就不会被回收，这时候堆内存充足，但本地内存可能已经使用光了，再次尝试分配本地内存就会出现outofMemoryError,那程序就直接崩溃了。

```java
public static void main(String[] args) {
    System.out.println("配置的maxDirectmemory"+( sun.misc.VM.maxDirectMemory() / (double) 1024/1024)+"MB");
    try {
        TimeUnit.SECONDS.sleep(3);} catch (InterruptedException e) {e.printStackTrace();}
    ByteBuffer buffer = ByteBuffer.allocateDirect(6*1024*1024);
}
//-Xms10m -Xmx10m -XX:+PrintGCDetails -XX:MaxDirectMemorySize=5m
```

---

### 5.java.lang.OutOfMemoryError:unable to create new native thread

- 创建线程的上限达到了
- 一个线程多次start会抛出IllegalThreadStateException
- 一般使用系统的2/3的线程数已经够多了
- 导致原因
  - 你的应用创建了太多线程了,一个应用进程创建多个线程,超过系统承载极限
  - 你的服务器并不允许你的应用程序创建这么多线程linux系统默认允许单个进程可以创建的线程数是1024个，你的应用创建超过这个数量,就会报java.lang.OutOfMemoryError: unable to create new native thread
- 解决办法
  1. 想办法降低 你应用程序创建线程的数量,分析应用是否真的需要创建这么多线程,如果不是，改代码将线程数降到最低
  2. 对于有的应用,磅实需要创建很多线程远超过Linux.系统的默认1024个线程的限制,可以通过修改linux服务器配置,扩大linux默认限制

```java
int i = 0;
while(true){
    new Thread(()->{
        try {
            TimeUnit.SECONDS.sleep(Integer.MAX_VALUE);} catch (InterruptedException e) {e.printStackTrace();}
    },""+(++i).start();
          }
//ulimit -u
```

---

### 6.java.lang.OutOfMemoryError:MetaSpace

#### 6.1 元空间存放的信息

1. 虚拟机加载的类信息
2. 常量池
3. 静态变量
4. 即时编译后的代码

#### 6.2 当元空间存放的数据满了的时候会抛出OOM

