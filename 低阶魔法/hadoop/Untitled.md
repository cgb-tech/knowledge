- [实验 2-2  Hadoop安装教程_单机/伪分布式配置](https://www.shuidilab.cn/console/expdesktop/4276#实验 2-2  Hadoop安装教程_单机/伪分布式配置)
- [一、实验目的](https://www.shuidilab.cn/console/expdesktop/4276#一、实验目的)
- [二、实验平台](https://www.shuidilab.cn/console/expdesktop/4276#二、实验平台)
- 三、实验内容和要求
  - [1、Hadoop安装方式简介](https://www.shuidilab.cn/console/expdesktop/4276#1、Hadoop安装方式简介)
  - [2、Hadoop基本安装配置步骤](https://www.shuidilab.cn/console/expdesktop/4276#2、Hadoop基本安装配置步骤)
  - 3、Hadoop安装准备工作
    - [（1）创建Hadoop用户](https://www.shuidilab.cn/console/expdesktop/4276#（1）创建Hadoop用户)
    - [（2）更新apt](https://www.shuidilab.cn/console/expdesktop/4276#（2）更新apt)
    - （3）安装SSH、配置SSH无密码登陆
      - [安装SSH](https://www.shuidilab.cn/console/expdesktop/4276#安装SSH)
      - [配置SSH无密码登陆](https://www.shuidilab.cn/console/expdesktop/4276#配置SSH无密码登陆)
    - （4）安装Java环境
      - [安装JDK](https://www.shuidilab.cn/console/expdesktop/4276#安装JDK)
  - [4、安装 Hadoop 2.7.1](https://www.shuidilab.cn/console/expdesktop/4276#4、安装 Hadoop 2.7.1)
  - [5、hadoop单机配置(非分布式)](https://www.shuidilab.cn/console/expdesktop/4276#5、hadoop单机配置(非分布式))
  - [6、Hadoop伪分布式配置](https://www.shuidilab.cn/console/expdesktop/4276#6、Hadoop伪分布式配置)
  - 7、运行Hadoop伪分布式实例

# 实验 2-2  Hadoop安装教程_单机/伪分布式配置

# 一、实验目的

学会Hadoop的安装与使用。

熟练掌握单机安装

熟练掌握伪分布安装

# 二、实验平台

 操作系统：Ubuntu14.04（64）

Hadoop版本：2.7.1（内置好安装好包）

# 三、实验内容和要求

## 1、Hadoop安装方式简介

**（1）单机模式：Hadoop**

​    默认模式为非分布式模式（本地模式），无需进行其他配置即可运行。非分布式即单 Java进程，方便进行调试。

 **（2）伪分布式模式**

​    Hadoop 可以在单节点上以伪分布式的方式运行，Hadoop进程以分离的 Java 进程来运行，节点既作为 NameNode 也作为DataNode，同时，读取的是 HDFS 中的文件。

 **（3）分布式模式**

​    使用多个节点构成集群环境来运行Hadoop，本实验中暂不练习。

## 2、Hadoop基本安装配置步骤

- （1）创建Hadoop用户
- （2）SSH登录权限设置
- （3）安装Java环境
- （4）单机安装配置 / 伪分布式安装配置

## 3、Hadoop安装准备工作

### （1）创建Hadoop用户

​    输入如下命令创建新用户 ：

```
# 这条命令创建了可以登录的 hadoop 用户，并使用 /bin/bash 作为 shell。useradd -m hadoop -s /bin/bash
```

​    接着使用如下命令设置密码，可简单设置为 hadoop，按提示输入两次密码：

```
passwd hadoop
```

​    可为 hadoop 用户增加管理员权限，方便部署，避免一些对新手来说比较棘手的权限问题：

```shell
adduser hadoop sudo
```

​    切换hadoop 用户进行登录。以下操作都是以Hadoop用户进行操作。切换hadoop登录的命令语句是：

```shell
su hadoop
```

![ ](https://imgcache.sdcloud.net/course-BigData/s1/media/fbee533ee19297f84f975c05f85389b8.png)

Figure 1 用hadoop用户登陆

### （2）更新apt

​    用 hadoop 用户登录后，我们先更新一下 apt，后续我们使用 apt安装软件，如果没更新可能有一些软件安装不了。执行如下命令，输入之前设置的hadoop用户密码：

```
sudo apt-get update
```

​    后续需要更改一些配置文件，建议安装使用vim（vi增强版，基本用法相同），具体使用方法可回顾一下实验一中的vi的介绍。

```
sudo apt-get install vim
```

​    安装软件时若需要确认，在提示处输入 y 即可。

![img](https://imgcache.sdcloud.net/course-BigData/s1/media/9321e3c85bb7d24e387ea398fa2209a2.png)

Figure 2 安装vim编辑器

### （3）安装SSH、配置SSH无密码登陆

#### 安装SSH

​    集群、单节点模式都需要用到 SSH 登陆（类似于远程登陆，你可以登录某台 Linux主机，并且在上面运行命令），Ubuntu 默认已安装了 SSH client，此外还需要安装 SSHserver：

```
sudo apt-get install openssh-server
```

![img](https://imgcache.sdcloud.net/course-BigData/s1/media/2c0bbc7861e52507c0d187776827388f.png)

Figure 3 安装ssh server

​    安装后，可以使用如下命令登陆本机：

```
ssh localhost
```

​    此时会有如下提示(SSH首次登陆提示)，输入 yes 。然后按提示输入hadoop密码，这样就登陆到本机了。

![img](https://imgcache.sdcloud.net/course-BigData/s1/media/c9e2a76fbc1737ae49c9816fe4077390.png)

Figure 4 SSH登陆提示

#### 配置SSH无密码登陆

​    但这样登录是需要每次输入密码的，我们需要配置成SSH无密码登陆比较方便。

​    首先退出刚才的 ssh，就回到了我们原先的终端窗口，然后利用 ssh-keygen生成密钥，并将密钥加入到授权中：

```
exit #退出刚才的 ssh localhost
```

![img](https://imgcache.sdcloud.net/course-BigData/s1/media/5de8248d4b2459357eac24b26945d49f.png)

Figure 5 退出ssh登陆

```
cd ~/.ssh/ #若没有该目录，请先执行一次`ssh localhost`ssh-keygen -t rsa
```

​    遇到黄框标识的三个提示，按回车即可。

![img](https://imgcache.sdcloud.net/course-BigData/s1/media/2595a3326fecdd4d5252fb39ef541c56.png)

Figure 6 利用ssh-keygen 生成密钥

​    接着执行
​

```
cat ./id_rsa.pub >> ./authorized_keys #加入授权
```

​    此时再用 ssh localhost 命令，无需输入密码就可以直接登陆了，如下图所示。

```
ssh localhostexit  #确认连接成功后，不要忘记退出ssh继续安装hadoop
```

![img](https://imgcache.sdcloud.net/course-BigData/s1/media/5a3f20761f5f6a48d6f272be7c2e3acc.png)

Figure 7 ssh无密码登陆

### （4）安装Java环境

​    Java环境可选择 Oracle 的 JDK，或是 OpenJDK，新版本在 OpenJDK 1.7下是没问题的。本实验中，我们直接通过命令安装 OpenJDK 7。

#### 安装JDK

```
sudo apt-get install default-jre default-jdk  #遇到是否继续提示，请输入y即可
```

​    注意：如果您是在自己的电脑上进行安装，上述安装过程需要访问网络下载相关文件，需要保持联网状态。

​    安装结束以后，需要配置JAVA_HOME环境变量，以便可以在任何目录下执行java命令。输入下面命令打开当前登录用户的环境变量配置文件.bashrc：

```
vim ~/.bashrc
```

​    在文件最前面添加如下单独一行（注意，等号“=”前后不能有空格），然后保存退出：

```
export JAVA_HOME=/usr/lib/jvm/default-java
```


![img](https://imgcache.sdcloud.net/course-BigData/help4.png) **小贴士：如何进行vi/vim操作**
    首先按键盘上的i，底部会出现INSERT,这样就进入vim的插入模式了。在首行插入文字。如下：

![img](https://imgcache.sdcloud.net/course-BigData/s1/media/32af60da161909d5f444da3944f8284a.png)

Figure 8 编辑java环境变量

​    按Esc，退出编写状态，输入 ：wq，按Enter建，保存并退出。如下图：

![img](https://imgcache.sdcloud.net/course-BigData/s1/media/c6c925cd75d036d69ba48419bf6a0550.png)

Figure 9 退出编辑

​    接下来，要让环境变量立即生效，请执行如下代码：

```
source ~/.bashrc
```

​    执行上述命令后，可以检验一下是否设置正确：

```
echo $JAVA_HOME # 检验变量值java -version $JAVA_HOME/bin/java -version #与直接执行java -version一样
```

![img](https://imgcache.sdcloud.net/course-BigData/s1/media/66882d16696d3196d12da3e17e94210a.png)

Figure 10 验证jdk安装是否正确

![img](https://imgcache.sdcloud.net/course-BigData/s1/media/1d1163f639a3f1c0dedb2aeee44654bb.png)

Figure 11 验证jdk环境变量是否正确

​    如果设置正确的话，$JAVA_HOME/bin/java -version会输出 java 的版本信息，且和`java -version`的输出结果一样。
安装好JAVA后，下面就可以进行Hadoop的安装。

​    至此，就成功安装了Java环境。下面就可以进入Hadoop的安装。

## 4、安装 Hadoop 2.7.1

​    Hadoop安装文件已下载到/root/tools路径下，选择将 Hadoop 安装至 /usr/local/ 中，操作如下： 

```
# 解压到/usr/local中sudo tar -zxvf /root/tools/hadoop-2.7.1.tar.gz -C /usr/localcd /usr/local/# 将文件夹名改为hadoopsudo mv ./hadoop-2.7.1/ ./hadoop# 修改文件权限sudo chown -R hadoop ./hadoop
```

​    Hadoop 解压后即可使用。输入如下命令来检查 Hadoop 是否可用，成功则会显示 Hadoop版本信息：

```
cd /usr/local/hadoop./bin/hadoop version
```

![img](https://imgcache.sdcloud.net/course-BigData/s1/media/583dd8c11ec40c125329104645218633.png)

Figure 12 显示hadoop版本信息（检查 Hadoop 是否可用）

## 5、hadoop单机配置(非分布式)

​    Hadoop默认模式为非分布式模式（本地模式），以上的操作即为单机配置，无需进行其他配置即可运行。非分布式即单Java 进程，方便进行调试。
​    现在我们可以执行例子来感受下 Hadoop 的运行。Hadoop 附带了丰富的例子。运行以下命令:

```
./bin/hadoop jar ./share/hadoop/mapreduce/hadoop-mapreduce-examples-2.7.1.jar
```

​    可以看到所有例子，包括 wordcount、terasort、join、grep 等。在此我们选择运行 grep 例子，我们将  input文件夹中的所有文件作为输入，筛选当中符合正则表达式 dfs[a-z.]+的单词并统计出现的次数，最后输出结果到 output  文件夹中。 

```
cd /usr/local/hadoop mkdir ./input# 将配置文件作为输入文件cp ./etc/hadoop/*.xml ./input./bin/hadoop jar ./share/hadoop/mapreduce/hadoop-mapreduce-examples-*.jar grep ./input ./output 'dfs[a-z.]+'# 查看运行结果cat ./output/*
```

​    执行成功后如下所示，输出了作业的相关信息，输出的结果是符合正则的单词 dfsadmin出现了1次，如下图所示：

![img](https://imgcache.sdcloud.net/course-BigData/s1/media/9acec22253f4fb43df8adf1f627b6520.png)

Figure 13 运行结果

​    注意:Hadoop 默认不会覆盖结果文件，因此再次运行上面实例会提示出错，需要先将./output 删除。 

```
rm -r ./output
```

## 6、Hadoop伪分布式配置

​    Hadoop 可以在单节点上以伪分布式的方式运行，Hadoop 进程以分离的 Java进程来运行，节点既作为 NameNode 也作为 DataNode，同时，读取的是 HDFS 中的文件。
​    Hadoop 的配置文件位于 /usr/local/hadoop/etc/hadoop/中，伪分布式需要修改2个配置文件 core-site.xml 和  hdfs-site.xml。Hadoop的配置文件是 xml 格式，每个配置以声明 property 的 name 和 value的方式来实现。

​    修改配置文件 core-site.xml .

```
vi /usr/local/hadoop/etc/hadoop/core-site.xml #打开core-site.xml
```

​    将当中的

```
<configuration></configuration>
```

​    修改为下面配置

```
<configuration>    <property>        <name>hadoop.tmp.dir</name>        <value>file:/usr/local/hadoop/tmp</value>        <description>Abase for other temporary directories.</description>    </property>    <property>        <name>fs.defaultFS</name>        <value>hdfs://localhost:9000</value>    </property></configuration>
```

​    如下图：

![img](https://imgcache.sdcloud.net/course-BigData/s1/media/1376a8c1b96e96e80da51474664bcb35.png)

Figure 14 修改配置文件

​    同样的，修改配置文件 **hdfs-site.xml**：

```
vi /usr/local/hadoop/etc/hadoop/hdfs-site.xml
<configuration>    <property>        <name>dfs.replication</name>        <value>1</value>    </property>    <property>        <name>dfs.namenode.name.dir</name>        <value>file:/usr/local/hadoop/tmp/dfs/name</value>    </property>    <property>        <name>dfs.datanode.data.dir</name>        <value>file:/usr/local/hadoop/tmp/dfs/data</value>    </property></configuration>
```

​    配置完成后，执行 NameNode 的格式化:

```
./bin/hdfs namenode -format
```

​    成功的话，会看到 “successfully formatted” 和 “Exitting with status 0”的提示，若为 “Exitting with status 1” 则是出错。

![img](https://imgcache.sdcloud.net/course-BigData/s1/media/72ee561496be1780120129dc3d252aff.png)

Figure 15 执行namenode格式化

​    接着开启 NameNode 和 DataNode 守护进程。

```
./sbin/start-dfs.sh  #start-dfs.sh是个完整的可执行文件，中间没有空格
```

​    若出现如下SSH提示，输入yes即可。

![img](https://imgcache.sdcloud.net/course-BigData/s1/media/d15bc22663cb43a91f1bca424ebe5f6b.png)

Figure 16 启动Hadoop时SSH提示

​    启动完成后，可以通过命令·`jps`  来判断是否成功启动，若成功启动则会列出如下进程:“NameNode”、”DataNode” 和 “SecondaryNameNode”（如果  SecondaryNameNode没有启动，请运行 sbin/stop-dfs.sh 关闭进程，然后再次尝试启动尝试）。如果没有NameNode 或 DataNode，那就是配置不成功，请仔细检查之前步骤，或通过查看启动日志排查原因。

![img](https://imgcache.sdcloud.net/course-BigData/s1/media/2723cd935a3374420c671d65ccaa67d9.png)

Figure 17 通过jps查看启动的Hadoop进程

## 7、运行Hadoop伪分布式实例

​    上面的单机模式，grep 例子读取的是本地数据，伪分布式读取的则是 HDFS上的数据。
​    以下步骤均在/usr/local/hadoop路径下运行，若不在，执行下图黄色框部分`cd /usr/local/hadoop`,再进行操作。要使用HDFS，首先需要在 HDFS 中创建用户目录：

```
./bin/hdfs dfs -mkdir -p /user/hadoop
```

​    接着将 ./etc/hadoop 中的 xml 文件作为输入文件复制到分布式文件系统中，即将/usr/local/hadoop/etc/hadoop  复制到分布式文件系统中的 /user/hadoop/input中。我们使用的是 hadoop 用户，并且已创建相应的用户目录  /user/hadoop，因此在命令中就可以使用相对路径如 input，其对应的绝对路径就是/user/hadoop/input:

```
./bin/hdfs dfs -mkdir input./bin/hdfs dfs -put ./etc/hadoop/*.xml input
```

​    复制完成后，可以通过如下命令查看文件列表：

```
./bin/hdfs dfs -ls input
```

![img](https://imgcache.sdcloud.net/course-BigData/s1/media/5dd71a495836506fd7df285c9d2b49c7.png)

Figure 18

​    伪分布式运行 MapReduce的作业的方式跟单机模式相同，区别在于伪分布式读取的是HDFS中的文件（可以将单机步骤中创建的本地input 文件夹，输出结果 output 文件夹都删掉来验证这一点）。

```
./bin/hadoop jar ./share/hadoop/mapreduce/hadoop-mapreduce-examples-*.jar grep input output 'dfs[a-z.]+'
```

​    查看运行结果的命令（查看的是位于 HDFS 中的输出结果）：

```
./bin/hdfs dfs -cat output/*
```

​    结果如下，注意到刚才我们已经更改了配置文件，所以运行结果不同。

![img](https://imgcache.sdcloud.net/course-BigData/s1/media/5347d9f1e1560d106a62f932e0e1ee1b.png)

Figure 19 Hadoop伪分布运行grep的结果

​    我们也可以将运行结果取回到本地：

```
# 先删除本地的 output 文件夹（如果存在）rm -r ./output# 将 HDFS 上的 output 文件夹拷贝到本机./bin/hdfs dfs -get output ./outputcat ./output/*
```

![img](https://imgcache.sdcloud.net/course-BigData/s1/media/461b448baa017f1cfb109d6848acbea5.png)

Figure 20

​    Hadoop 运行程序时，输出目录不能存在，否则会提示错误“org.apache.hadoop.mapred.FileAlreadyExistsException: Output directory
hdfs://localhost:9000/user/hadoop/output already exists”，因此若要再次执行，需要执行如下命令删除 output 文件夹:

```
# 删除 output 文件夹./bin/hdfs dfs -rm -r output
```

​    若要关闭 Hadoop，则运行

```
./sbin/stop-dfs.sh
```

![img](https://imgcache.sdcloud.net/course-BigData/s1/media/a3ced61d8811c0afea85feff651fab83.png)

Figure 21 关闭Hadoop