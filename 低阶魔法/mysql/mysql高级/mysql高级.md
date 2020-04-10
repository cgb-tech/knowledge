## 索引

索引是数据结构

是帮助MySQL高效获取数据的数据结构

提高查找效率

## 检查sql语句的性能(性能分析)

explain + sql

![1565605175984](C:\Users\888\AppData\Roaming\Typora\typora-user-images\1565605175984.png)

- id 
  - id相同的情况下,执行顺序由上到下
  - id不同(子查询,id序列号会递增),id越大,优先级越高,越先被执行
  - id值相同被认作一组,从上往下先后顺序执行,所有组中,id越大越先被执行
- select_type
  - 