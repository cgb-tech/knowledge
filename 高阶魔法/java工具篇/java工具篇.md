# Java工具篇

## 1. jps

- 查看当前java运行的进程
- jps -l

## 2. jstack 

- 查看执行pid的线程
- jstack pid

## 3. jinfo

- jinfo -flag PrintGCDetails pid 查看指定进程号是否开启参数（+|-）,参数的值是什么
- jinfo -flags pid 把当前所有的参数都显示出来