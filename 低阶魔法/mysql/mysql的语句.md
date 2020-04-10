## DQL(data query language)

### 普通查询

- 可以用AS起别名

  ```mysql
  select `name` as myname from student;
  #可以把别名引起来
  ```

- 去重(把重复的行去掉)

  ```mysql
  select distinct `name` as myname from student;
  ```

- '+' 的作用

  ```mysql
  select 'john'+90;
  #结果为90
  #试图将字符转化为数字,成功则success,失败则字符为0
  #只要其中一方为null,则结果必定为null
  ```

- 查询语句中的字符串的拼接

  ```mysql
  select distinct concat(`name`,`sex`) as myname from student;
  ```

- IFNULL函数

  ```mysql
  #使用IFNULL
  IFNULL(setp,0) 
  #如果前面的字段是NULL,那么将会被第二个字段所代替
  ```

- ESCAPE

  ```mysql
  #用escape可以设置一个字符为转义字符 
  like '_$_name' escape '$'
  ```

- between and

  ```mysql
  两个数字之间的查找
  ```

- is null

  ```mysql
  # mysql 不可以直接写 = null ,需要用 is 进行判断
  ```

  

### 排序查询

- oreder by 
  - asc 升序(默认)
  - desc 降序
- `length()` 可以打印出长度 获取字节长度

### 常见函数

- 单行函数concat,length,ifnull
  - lower(变小写),upper(变大写)
  - substr() 截取字符串
  - instr(str,str2) 返回str2在str的索引
  - trim()  trim('x' from 'xxxxxx');去前后的x字符串
  - lpad('xx',number,'x') 左填充 rpad()右填充
  - replace('xxxx','xx','bb')
- 数学函数
  - round() 四舍五入 , round(numb,number) 小数点保留几位
  - ceil() 向上取整
  - floor() 向下取整
  - truncate(1.8999,1) 截断 结果:1.8 小数点后1位往后都舍去
  - mod(10,3) 取余
- 日期函数
  - now()
  - curdate()
  - curtime()
  - 年月日时分秒
- 其他函数
  - version() 查看当前版本号
  - database() 查看当前数据库
  - User() 当前用户
  - if(str1,str2,str3)
  - case()
- 分组函数
  - sum() 求和
  - avg() 平均值
  - max() 最大值
  - min() 最小值
  - count() 计算个数

### 创建表

```mysql
create table User {
	id int primary key,
	name varchar(20) unique not null,
	price float default 10,
	type int,
	foreign key(type) references VIP(id)
}
```

### 创建事务

```mysql
set autocommit = 0
xxxxxxxxxx
commit/rollback
```

