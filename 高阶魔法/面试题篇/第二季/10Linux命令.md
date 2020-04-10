# Linux查看性能的命令

## 1、top

![mark](http://codedorado.oss-cn-beijing.aliyuncs.com/images/20200316/202316035.png)

- 负载值相加/3 >60%代表系统压力重

---

## 2、uptime

精简版top

---

## 3、vmstat

```shell
vmstat -n 2 3
mpstat -P ALL 2
pidstat -u 1 -p pid
//查看cpu
```

- 每两秒采样一次，共计三次
- 主要查看CPU，但不限于CPU

![mark](http://codedorado.oss-cn-beijing.aliyuncs.com/images/20200316/203000482.png)

- procs
  - r:运行和等待CPU时间片的进程数，原则上1核的CPU的运行队列不要超过2，整个系统的运行队列不能超过总核数的2倍，否则代表系统压力过大
  - b:等待资源的进程数，比如正在等待磁盘I/0、网络I/0等。
- cpu
  - us: 用户进程消耗CPU时间百分比，us值高，用户进程消耗CPU时间多，如果长期大于50%， 优化程序;
  - sy:内核进程消耗的CPU时间百分比;
  - us + sy 参考值为80%
  - id：处于空闲的cpu百分比
  - wa：系统等待IO的cpu时间百分比
  - st：来自于一个虚拟机偷取的cpu时间的百分比

---

## 4、内存free

```shell
free -m  
pidstat -p pid -r time
```

查看内存

---

## 5、硬盘df

```shell
df -h
```

![mark](http://codedorado.oss-cn-beijing.aliyuncs.com/images/20200316/204740417.png)

查看磁盘剩余空间数

---

## 6、磁盘IO：iostat

```shell
iostat -xdk 2 3
pidstat -d 2 -p pid
```

![mark](http://codedorado.oss-cn-beijing.aliyuncs.com/images/20200316/205108976.png)

- rkB/s每秒读取数据量kB;
- wkB/s每秒写入数据量kB;
- svctm I/O请求的平均服务时间，单位毫秒;
- await I/O0请求的平均等待时间，单位毫秒;值越小，性能越好:
- ==util一.秒中有百分几的时间用于I/O操作。接近100%时，表示磁盘带宽跑满，需要优化程序或者增加磁盘;==
- rkB/s、wkB/s根据系统应用不同会有不同的值，但有规律遵循:长期、超大数据读写，肯定不正常，需要优化程序读取。
- svctm的值与await的值很接近，表示几乎没有I/O等待，磁盘性能好，.
- 如果await的值远高于svctm的值，则表示I/O队列等待太长，需要优化程序或更换更快磁盘。

## 7、网络IO ifstat

```shell
ifstat time	
```

## 8、服务类

```shell
cnetos6：
service network status
service name start
service name restart
service name stop
service name reload
centos7：
systemctl start name
systemctl restart name
systemctl stop name
systemctl reload name
systemctl status name 
#查询所有的服务
systemctl list-unit-files
#设置开机不启动
systemctl disable|enable firewalld
```

## 9、chkconfig --list

![mark](http://codedorado.oss-cn-beijing.aliyuncs.com/images/20200317/181033099.png)

- 开关服务

```shell
chkconfig --level 5 服务名 on|off
```

