# zookeeper

## 一、zookeeper安装与配置

### 1、下载zookeeper

```shell
wget http://apache.mirrors.hoobly.com/zookeeper/zookeeper-3.5.7/apache-zookeeper-3.5.7-bin.tar.gz
```

### 2、创建目录

```shell
cd /usr/local
mkdir zookeeper
mv ~/apache-zookeeper-3.6.1-bin.tar.gz  /usr/local/zookeeper/
tar -zxvf apache-zookeeper-3.6.1-bin.tar.gz
rm -f apache-zookeeper-3.6.1-bin.tar.gz
```

### 3、修改配置文件

```shell
cd conf/
cp zoo_sample.cfg zoo.cfg
vim zoo.cfg
```

```shell
tickTime=2000
initLimit=10
syncLimit=5
dataDir=/usr/data/zookeeper
clientPort=2181
dataLogDir=/usr/local/zookeeper/apache-zookeeper-3.6.1-bin/logs
```

### 4、修改环境变量

```shell
/usr/local/zookeeper/apache-zookeeper-3.6.1-bin/bin
vim /etc/profile
# 在最后一行加入
export ZOOKEEPER_HOME=/usr/local/zookeeper/apache-zookeeper-3.6.1-bin/
export ZOOBINDIR=$ZOOKEEPER_HOME/bin
export PATH=$ZOOBINDIR:$PATH

source /etc/profile
```

### 5、启动zookeeper

```shell
cd /usr/local/zookeeper/apache-zookeeper-3.6.1-bin/bin
./zkServer.sh start
./zkCli.sh 
grep -E -i "((exception)|(error))" *
```

### 6、初步测试

```shell
ls -R /
create /app1
create /app2
# 分布式锁的实现
# 客户端1
create -e /app1/lock
quit
# 客户端2
create -e /app1/lock
stat -w /lock
create -e /app1/lock

# 监控worker节点
ls -w /worker
```

