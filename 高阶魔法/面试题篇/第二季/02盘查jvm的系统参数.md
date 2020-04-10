## 二、请问如何盘点查看JVM系统默认值

### 1.JVM参数类型

- 标配参数
  - -version
  - -help
  - java -showversion
- X参数(了解)
  - -Xint 解释执行
  - -Xcomp 第一次使用就编译成本地代码
  - -Xmixed 混合模式
- ==XX参数==
  - Boolean类型
    - -XX: + | - 某个属性值  +表示开启 -表示关闭
    - -XX:+PrintGCDetails 打印日志
    - -XX:+UseSerialGC 是否使用串行垃圾回收器
  - KV设值类型
    - -XX:属性key=属性值value
    - -XX:MetaspaceSize=128m 元空间的大小
    - -XX:MaxTenuringThreshold=15 对象存活多少次进入老年代
    - -Xms1024m 等价于-XX:InitialHeapSize
    - -Xmx1024m 等价于-XX:MaxHeapSize
    - -Xss128k 栈空间大小

### 2.`盘点家底查看JVM默认值`

==有:=的是被修改过的值==

- -XX:PrintFlagsInitial
  - 主要查看初始默认值
  - 公式
    - java -XX:+PrintFlagsInitial -version
    - java -XX:+PrintFlagsInitial
- -XX:PrintFlagsFinal
  - 主要查看修改更新
  - 公式
    - java -XX:+PrintFlagsFinal -version
- PrintFlagsFinal举例，运行java命令的同时打印出参数
- -XX:+PrintCommandLineFlags -version 最主要查看的是使用的哪种垃圾回收器

