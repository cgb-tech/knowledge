# 栈

每一个线程在栈里面单独开辟一个栈用来保存局部变量

每一个方法的有自己的一个`栈帧`，里面保存有好几个部分

- 局部变量表
- 操作数栈       临时内存区域，用来暂时存放操作数
- 动态链接
- 方法出口      一块内存区域，存当前方法运行时应该返回到什么地方去

![1578905229396](C:\Users\888\AppData\Roaming\Typora\typora-user-images\1578905229396.png)

![1578906869611](C:\Users\888\AppData\Roaming\Typora\typora-user-images\1578906869611.png)

# 程序计数器

每个线程独有的，记录正在运行线程正在执行的哪个位置（行号），由字`节码执行引擎`去修改和记录,当运行的线程被抢占后，被挂起之后恢复（多线程切回时），需要从记录位置执行 

# 堆

new出来的对象都放在堆中

![1579014640551](C:\Users\888\AppData\Roaming\Typora\typora-user-images\1579014640551.png)

![1579015127904](C:\Users\888\AppData\Roaming\Typora\typora-user-images\1579015127904.png)

GC root根

# 方法区（元空间）

方法区里面存放常量、静态变量、类信息 

# 本地方法栈



`jvisualvm`一个java自带的工具

可以识别本机正在运行的进程信息

需要安装一个插件，`Visual GC`

![1579090602894](C:\Users\888\AppData\Roaming\Typora\typora-user-images\1579090602894.png)

java虚拟机调优的目的 减少STW   full GC占用时间很长，需要调优