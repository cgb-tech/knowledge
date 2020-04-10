# 生产环境CPU过高，怎么分析和定位

## 1、先用top命令找出cpu占比最高的



## 2、ps -ef或者jps进一步定位，得知是一个怎么样的一个后台程序给我们惹事

```shell
jps -l
ps -ef |grep java|grep -v grep
```

![mark](http://codedorado.oss-cn-beijing.aliyuncs.com/images/20200316/210342990.png)

## 3、定位到具体线程或者代码

```shell
ps -mp 进程 -o THREAD,tid,time
```

![mark](http://codedorado.oss-cn-beijing.aliyuncs.com/images/20200316/210428112.png)

- -m显示所有的==线程==
- -p pid 进程使用cpu的时间
- -o 该参数后是用户自定义参数

## 4、将需要的线程ID转化为16进制格式(英文小写)

```
printf "%x\n" 有问题的线程id
```

## 5、jstack 进程ID | grep tid（16进制线程ID小写英文） -A60

```shell
jstack pid | grep 13ee -A60
```

