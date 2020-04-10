# BIO

## 1、代码

server端传统代码

```java
public class TestBIOServer {
    static byte[] bs = new byte[1024];
    
    public static void main(String[] args) {
        try {
            ServerSocket serverSocket = new ServerSocket(8080);
            while (true) {
                Socket accept = serverSocket.accept();
                accept.getInputStream().read(bs);
                System.out.println(new String(bs));
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

client传统代码

```java
public class TestBIOClient {
    public static void main(String[] args) {
        try {
            Socket socket = new Socket("127.0.0.1",8080);
            socket.getOutputStream().write("hello".getBytes());
            socket.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

## 2、劣势

1. server端有两个地方有阻塞
2. 并发需要多线程支持，浪费资源

## 3、解决思路

1. 首先将 read() 解阻塞
2. 还是直接用 NIO 吧

---

# NIO

## 1、开始

- New IO （non blocking IO）
- 面向缓冲区

![mark](http://codedorado.oss-cn-beijing.aliyuncs.com/images/20200322/182138916.png)![mark](http://codedorado.oss-cn-beijing.aliyuncs.com/images/20200322/190417465.png)



## 2、Buffer

```java
public abstract class Buffer {

    /**
     * The characteristics of Spliterators that traverse and split elements
     * maintained in Buffers.
     */
    static final int SPLITERATOR_CHARACTERISTICS =
        Spliterator.SIZED | Spliterator.SUBSIZED | Spliterator.ORDERED;

    // Invariants: mark <= position <= limit <= capacity
    private int mark = -1;
    // 位置，表示缓冲区中正在操作数据的位置
    private int position = 0;
    // 界限。表示缓冲区中可以操作数据的大小
    private int limit;
    // 容量，表示缓冲区最大存储数据的容量。一旦声明不能改变。
    private int capacity;

    // Used only by direct buffers
    // NOTE: hoisted here for speed in JNI GetDirectBufferAddress
    long address;
}
```

- 直接缓冲区和非直接缓冲区：
  - 非直接缓冲区：通过allocate() 方法分配缓冲区，将缓冲区建立在 JVM 的内存中
  - 直接缓冲区： 通过 allocateDirect() 方法分配直接缓冲区，将缓冲区建立在物理内存中，可以提高效率

![mark](http://codedorado.oss-cn-beijing.aliyuncs.com/images/20200322/195135873.png)



# Channel

![mark](http://codedorado.oss-cn-beijing.aliyuncs.com/images/20200322/204525990.png)

![mark](http://codedorado.oss-cn-beijing.aliyuncs.com/images/20200322/204714169.png)

 