##  三、平时工作中常用的基本配置参数有哪些

### 1.小知识

- jdk1.8 以后，元空间取代了永久代，元空间并不在虚拟机中，而是使用本机的物理内存
- Runtime.getRuntime().totalMemory(); 返回java虚拟机中的内存总量
- Runtime.getRuntime().maxMemory();返回java虚拟机在试图使用的最大内存量

### 2.参数列表

- -Xms
  - 初始值为内存的1/64
  - 等价于`-XX:InitialHeapSize`
- -Xmx
  - 初始值为内存的1/4
  - 等价于`-XX:MaxHeapSize`
- -Xss
  - 设置`单个线程栈`的大小，一般默认为512k~1024k
  - 等价于`-XX:ThreadStackSize`
  - 如果是0用的是系统的默认值，跟平台有关
- -Xmn
  - 设置年轻代大小
- -XX:MetaSpaceSize
  - 元空间的本质和永久代类似，都是对JVM规范中方法区的实现
  - 元空间不在虚拟机中，而是使用本地内存，默认情况下，元空间的大笑仅收本地内存限制
  - 默认元空间是很小的，需要自己手动给调整
- -XX:+PrintGCDetails
  - 输出GC的收集详细信息
  - ![image-20200312114054447](C:\Users\888\AppData\Roaming\Typora\typora-user-images\image-20200312114054447.png)

- -XX:SurvivorRatio
  - 可以调整比例  新生代中Eden与幸存区的比例
  - -XX:SurvivorRatio=8,Eden:s0:s1=8:1:1
- -XX:NewRatio
  - 堆中老年代与新生代的比例
  - -XX:NewRatio=4  old:young=4:1，默认是3:1
  - 这边要注意下,-当-Xmn和-XX:NewRatio同时存在的时候以-Xmn为准
- -XX:MaxTenuringThreshold
  - 设置垃圾最大年龄 默认是15，最多是15

