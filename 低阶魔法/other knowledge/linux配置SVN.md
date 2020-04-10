## CentOS7下配置svn服务器

1. ```shell
   yum install subversion
   ```

首先是在linux上安装svn

2. 检查svn是不是安装成功

```shell
svnserve --version
```

3. 创建svn仓库

   ```shell
   [root@localhost ~]# svnadmin create /svndir
   [root@localhost ~]# cd /svndir/
   [root@localhost svndir]# ls
   conf  db  format  hooks  locks  README.txt
   [root@localhost svndir]# cd conf/
   [root@localhost conf]# ls
   authz  passwd  svnserve.conf
   ```

 2，新建一个目录用于存储SVN目录



```shell

[root@localhost svn]# svnadmin create /svn/test/
[root@localhost svn]# ll /svn/test/
total 24
drwxr-xr-x. 2 root root 4096 Jul 28 18:12 conf
drwxr-sr-x. 6 root root 4096 Jul 28 18:12 db
-r--r--r--. 1 root root    2 Jul 28 18:12 format
drwxr-xr-x. 2 root root 4096 Jul 28 18:12 hooks
drwxr-xr-x. 2 root root 4096 Jul 28 18:12 locks
-rw-r--r--. 1 root root  229 Jul 28 18:12 README.txt

```





以下关于目录的说明：

hooks目录：放置hook脚步文件的目录

locks目录：用来放置subversion的db锁文件和db_logs锁文件的目录，用来追踪存取文件库的客户端

format目录：是一个文本文件，里边只放了一个整数，表示当前文件库配置的版本号

conf目录：是这个仓库配置文件（仓库用户访问账户，权限）

 

