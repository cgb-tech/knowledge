# redis持久化

### RDB reids database

##### 什么事RDB	

- 在指定的时间间隔内将内存中的数据集快照写入磁盘，
  也就是行话讲的Snapshot快照，它恢复时是将快照文件直接读到内存里

- Redis会单独创建（fork）一个子进程来进行持久化，会先将数据写入到
  一个临时文件中，待持久化过程都结束了，再用这个临时文件替换上次持久化好的文件。
  整个过程中，主进程是不进行任何IO操作的，这就确保了极高的性能
  如果需要进行大规模数据的恢复，且对于数据恢复的完整性不是非常敏感，那RDB方
  式要比AOF方式更加的高效。RDB的缺点是最后一次持久化后的数据可能丢失。

##### foke

- Fork的作用是复制一个与当前进程一样的进程。新进程的所有数据（变量、环境变量、程序计数器等）
  数值都和原进程一致，但是是一个全新的进程，并作为原进程的子进程



### AOF Append Only File

##### 什么是AOF

- 以日志的形式来记录每个写操作，将Redis执行过的所有写指令记录下来(读操作不记录)，
  只许追加文件但不可以改写文件，redis启动之初会读取该文件重新构建数据，换言之，redis
  重启的话就根据日志文件的内容将写指令从前到后执行一次以完成数据的恢复工作

`Aof保存的是appendonly.aof文件`

appendonly 默认是no 打开yes就可以aof化

![1564056629771](C:\Users\888\AppData\Roaming\Typora\typora-user-images\1564056629771.png)

![1564056661708](C:\Users\888\AppData\Roaming\Typora\typora-user-images\1564056661708.png)

`Redis会记录上次重写时的AOF大小，默认配置是当AOF文件大小是上次rewrite后大小的一倍且文件大于64M时触发`

![1564056698802](C:\Users\888\AppData\Roaming\Typora\typora-user-images\1564056698802.png)