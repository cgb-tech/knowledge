**一、使用以下命令查看当前安装mysql情况，查找以前是否装有mysql**

```
rpm -qa|grep -i mysql 
```

可以看到如下图的所示：

![img](http://files.jb51.net/file_images/article/201611/2016111793322836.jpg?2016101793335)

显示之前安装了：

   MySQL-client-5.5.25a-1.rhel5

   MySQL-server-5.5.25a-1.rhel5

**2、停止mysql服务、删除之前安装的mysql**

删除命令：`rpm -e –nodeps 包名`

```
rpm -ev MySQL-client-5.5.25a-1.rhel5 
rpm -ev MySQL-server-5.5.25a-1.rhel5 
```

如果提示依赖包错误，则使用以下命令尝试

```
rpm -ev MySQL-client-5.5.25a-1.rhel5 --nodeps 
```

如果提示错误：`error: %preun(xxxxxx) scriptlet failed, exit status 1`

则用以下命令尝试：

```
rpm -e --noscripts MySQL-client-5.5.25a-1.rhel5 
```

**3、查找之前老版本mysql的目录、并且删除老版本mysql的文件和库**

```
find / -name mysql
```

查找结果如下：

```
find / -name mysql 

/var/lib/mysql
/var/lib/mysql/mysql
/usr/lib64/mysql
```

删除对应的mysql目录

```
rm -rf /var/lib/mysql
rm -rf /var/lib/mysql
rm -rf /usr/lib64/mysql
```

具体的步骤如图：查找目录并删除

![img](http://files.jb51.net/file_images/article/201611/2016111793651688.jpg?201610179371)

**注意：**卸载后/etc/my.cnf不会删除，需要进行手工删除

```
rm -rf /etc/my.cnf 
```

**4、再次查找机器是否安装mysql**

```
rpm -qa|grep -i mysql 
```