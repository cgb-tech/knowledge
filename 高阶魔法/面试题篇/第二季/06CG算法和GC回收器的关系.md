# 六、GC算法和GC回收器之间的关系

## 1、垃圾回收算法

- 内存回收的方法论，垃圾回收期是算法的落地实现

### 1.1 引用计数

### 1.2 复制

### 1.3 标记清除

### 1.4 标记压缩

### 1.5 分代收集

## 2、垃圾回收的各种方式

### 2.1 Serial

- 串行垃圾回收器
- 为单线程环境设计且使用`一个`线程进行垃圾回收，会暂停所有的用户进程。所以不适合服务器环境
- <img src="C:\Users\888\AppData\Roaming\Typora\typora-user-images\image-20200314114727433.png" alt="image-20200314114727433" style="zoom:50%;" />

### 2.2 Parallel

- 并行垃圾回收器
- 为单线程环境设计且使用`多个`线程进行垃圾回收，会暂停所有的用户进程
- 适用于科学计算/大数据处理首页处理等弱相应场景
- ![image-20200314115210762](C:\Users\888\AppData\Roaming\Typora\typora-user-images\image-20200314115210762.png)

### 2.3 CMS

- 并发垃圾回收器
- 用户线程和垃圾收集线程同时执行(不一定是并行，可能交替执行)，不需要停顿用户线程,互联网公司多用它，适用对响应时间有要求的场景
- ![image-20200314115700786](C:\Users\888\AppData\Roaming\Typora\typora-user-images\image-20200314115700786.png)

### 2.4 G1

- G1垃圾回收期将堆内存分割成不同的区域然后并发的对其进行垃圾回收

### 2.5 ZGC

- java11 12-> ZGC 

### 小总结

![image-20200314115931324](C:\Users\888\AppData\Roaming\Typora\typora-user-images\image-20200314115931324.png)