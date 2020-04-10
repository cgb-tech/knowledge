### 存储过程:

```mysql
create procedure 存储过程名(参数)
begin
	declare result varchar(20) default '';
	select count(*) into result;
	存储过程体(一组合法的sql语句)
end
#参数的实例
IN name varchar(20)
IN 输入
OUT 输出
INOUT 输入加输出
#如果只有一句话的话begin end可以省略
#整个方法体的结尾需要使用 delimter来标识

```

调用的时候使用 `call 存储过程名();`来调用

### 函数:

```mysql
create function 函数名(参数列表) returns 返回类型
begin
	函数体;
	return 返回值;
end $
```

- 调用

  - ```mysql
    select 函数名()
    ```

    