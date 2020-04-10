## mysql的视图功能

### 创建视图

```mysql
create view view_name as 查询语句 
```

### 修改视图

```mysql
create or replace view view_name as 查询语句
  or
alter view view_name as 查询语句
```

### 删除视图

```mysql
drop view view_name1,view_name2 ;
```

### 查看视图

```mysql
show create view view_name
```

### 插入数据

```mysql
insert into view_name values(str1,str2)
```

### 修改数据

```mysql
update view_name set str = 'xxx' where xxx = 'xxx'
```

