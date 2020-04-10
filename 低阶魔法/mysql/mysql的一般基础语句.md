## 数据库的基本语句

1. 查看当前所有的数据库

```mysql
show databases;
```

2. 打开指定的库

```mysql
use 库名
```

3. 查看库当前所有的表

```mysql
show tables;
```

4. 查看其他库的所有表

```mysql
show tables from 库名;
```

5. 创建表

```mysql
create table 表名 {
	列名	类型,
	列名	类型,
	...
}
```

6. 查看表结构

```mysql
desc 表名;
```

## MySQL的语法规范

- 不区分大小写
- 关键字大写
- 每条命令最好分号结尾
- 每条语句根据需要换行,关键字单独一行
- 注释:
  - #
  - -- 文字
  - /* */
  - MySQL中索引从一开始

https://blog.csdn.net/u013517229/article/details/79412170