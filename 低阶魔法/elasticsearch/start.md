### 安装和启动

这里使用docker进行安装的

`docker pull elasticsearch`

然后使用

`docker run -e  ES_JAVA_OPTS="-Xms256m -Xmx265m" -d -p 9200:9200 -p 9300:9300 --name ES01 5acf0e8da90b`

进行启动(因为需要很大的内存空间,所以把占用的内存空间限制在256m上)

---

以上是docker下的安装,注意要安装2.6以下的版本,5x以上的版本连接9300需要在yaml里面做配置

---

### 正常的安装

### 1.安装jdk

##### 　　1.1 执行命令下面命令查看可安装java版本

```shell
yum -y list java*
```

##### 　　1.2 选择一个java版本进行安装， 

```shell
yum install -y java-1.8.0-openjdk-devel.x86_64
```

这里有个地方要注意，要选择 要带有-devel的安装，因为这个安装的是jdk，而那个不带-devel的安装完了其实是jre。

##### 　　1.3 输入`java -version`查看已安装的jdk版本，当出现如下输出表示安装成功。 

##### 　　1.4 jdk 的安装目录

```shell
/usr/lib/jvm 
```

##### 　　1.5 配置环境变量，编辑/etc/profile文件：

```shell
vi /etc/profile
```

##### 　　1.6 在文件尾部添加如下配置： 

```shell
export JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.181-3.b13.el6_10.x86_64　　　　export CLASSPATH=.:$JAVA_HOME/jre/lib/rt.jar:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar 　　　　export PATH=$PATH:$JAVA_HOME/bin
```

##### 　　1.7 `:wq`退出vim编辑器，然后使用命令更新配置 

```shell
source /etc/profile
```

 

### 2. 安装ES

#### 　　2.1 依次执行命令

```shell
wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-6.3.2.zip
wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-6.3.2.zip.sha512
shasum -a 512 -c elasticsearch-6.3.2.zip.sha512 
unzip elasticsearch-6.3.2.zip
cd elasticsearch-6.3.2/ 
```

##### 　　　　遇到错误1

```shell
-bash: shasum: command not found
```

　　　　解决方案：在CentOS上，“shasum”被称为“sha1sum”，运行下面命令修复此问题 

```shell
ln -s /usr/bin/sha1sum /usr/bin/shasum
```

##### 　　　　遇到错误2

```shell
sha1sum: invalid option -- 'a'
```

　　　　解决方案：

```shell
yum install -y perl-Digest-SHA
```

##### 　　　　遇到报错3

```shell
OpenJDK 64-Bit Server VM warning: If the number of processors is expected to increase from one, then you should configure the number of parallel GC threads appropriately using -XX:ParallelGCThreads=N　　　　　　OpenJDK 64-Bit Server VM warning: INFO: os::commit_memory(0x00000000c5330000, 986513408, 0) failed; error='Cannot allocate memory' (errno=12)
```

　　　　解决方案：调小启动内存，如果512m还是不行的话，就得要再往下调了

```shell
[root@iZwz9ahuk6xeihs1n3gqy5Z elasticsearch-6.3.2]# vi config/jvm.options　　　　　　　　
#-Xms2g　　　　　　　　
#-Xmx2g　　　　　　　　
-Xms512m　　　　　　　　
-Xmx512m
```

##### 　　　　遇到报错4：

无法以root权限启动

```shell
org.elasticsearch.bootstrap.StartupException: java.lang.RuntimeException: can not run elasticsearch as root
```

　　　　解决方案：创建一个非root用户并赋予目录该用户权限，再启动 

```shell
#root身份　groupadd es　　　　　　　
useradd es -g es -p es　　　　　　　　
chown es:es ${elasticsearch_HOME}/ #存放elasticsearch的目录　
su - es  #切换到es用户下　　　　　　　　
${elasticsearch_HOME}/bin/elasticsearch #启动elasticsearch
```

##### 　　　　遇到报错5

```shell
ERROR: [4] bootstrap checks failed
　　　　　　[1]: max file descriptors [65535] for elasticsearch process is too low, increase to at least [65536]
      [2]: max number of threads [1024] for user [e] is too low, increase to at least [4096]
　　　　　　[3]: max virtual memory areas vm.max_map_count [65530] is too low, increase to at least [262144]
　　　　　　[4]: system call filters failed to install; check the logs and fix your configuration or disable system call filters at your own risk
```

　　　　解决方案：

　　　　【5.1】【5.2】

```shell
#root身份
vi /etc/security/limits.conf  
#文件末尾添加内容（*也是要加上去）
* soft nofile 65536  
* hard nofile 131072  
* soft nproc 4096  
* hard nproc 4096  
```

　　　　【5.3】

 

```shell
#root身份
sysctl -w vm.max_map_count=262144
vim /etc/sysctl.conf     #让配置永久生效 
#文件末尾添加内容
vm.max_map_count=262144 
```

　　【5.4】

　　　　Centos6不支持SecComp，而ES6默认bootstrap.system_call_filter为true

```shell
vim config/elasticsearch.yml 
```

　　　　　　禁用：在elasticsearch.yml中配置bootstrap.system_call_filter为false，注意要在Memory下面: 　　　　　　

```shell
#注意在":"后面需要空格
bootstrap.memory_lock: false   
bootstrap.system_call_filter: false
```

##### 　　　　遇到报错6

还有一些其他的错误，比如防火墙没开放端口

##### 　　　　遇到报错7

`OpenJDK 64-Bit `

```shell
问题二：OpenJDK 64-Bit Server VM warning: If the number of processors is expected to increase from one, then you should configure the number of parallel GC threads appropriately using -XX:ParallelGCThreads=N

报错原因：某些软件包启动的时候默认启用 -XX:+AssumeMP导致

解决方案：在jvm.optipons配置文件添加 -XX:-AssumeMP
```

##### 　　　　遇到报错8

启动失败，检查没有通过，报错

```java
[2018-05-18T17:44:59,658][INFO ][o.e.b.BootstrapChecks    ] [gFOuNlS] bound or publishing to a non-loopback address, enforcing bootstrap checks
ERROR: [2] bootstrap checks failed
[1]: max file descriptors [4096] for elasticsearch process is too low, increase to at least [65536]

[2]: max virtual memory areas vm.max_map_count [65530] is too low, increase to at least [262144]
```

```shell
[1]: max file descriptors [65535] for elasticsearch process is too low, increase to at least [65536]

编辑 /etc/security/limits.conf，追加以下内容；
* soft nofile 65536
* hard nofile 65536
此文件修改后需要重新登录用户，才会生效
```



* soft nofile 65536
* hard nofile 65536
此文件修改后需要重新登录用户，才会生效

```shell
[2]: max virtual memory areas vm.max_map_count [65530] is too low, increase to at least [262144]

编辑 /etc/sysctl.conf，追加以下内容：
vm.max_map_count=655360
保存后，执行：
sysctl -p
```

重启成功

编辑 /etc/sysctl.conf，追加以下内容：
vm.max_map_count=655360
保存后，执行：
sysctl -p

在此感谢两位博主

https://blog.csdn.net/feng12345zi/article/details/80367907

https://www.cnblogs.com/jj81/p/9404576.html