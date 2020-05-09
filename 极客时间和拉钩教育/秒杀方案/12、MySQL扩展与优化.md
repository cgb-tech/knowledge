# MySQL扩展与优化

> 能够进行系统的并发升级的三个公式
>
> 1. 缓存
> 2. 异步
> 3. 批处理

## 一、MySQL单机应用的性能优化



> - **`max_connection=1000`** (1w+)
> - **`innodb_file_per_table=1`** 以一张表为一页进行存储
> - **`innodb_buffer_pool_size=6G`** 数据buffer大小，占物理内存的 60% ~ 80%，如果是8G的话要设置到6G

> - `innodb_log_file_size=256M`
> - `innodb_log_buffer_size-16M` 

> - **`innodb_flush_log_at_trx_commit=2`** **需要放在[mysqlId_safe] 节点**
>
> 这个参数尤为重要，每次都用 `write `写到物理的缓存中，每隔一秒进行 flush 一次，可以保证`mysql`的日志一定会到物理文件中去

> - **`innodb_data_file_path=ibdata1:1G;ibdata2:1G;ibdata3:1G:auto extend`**
>
> 设置data文件的分区

![mark](http://codedorado.oss-cn-beijing.aliyuncs.com/images/20200504/104810056.png)

## 二、MySQL分布式扩展

> 主从扩展

### 1、`mysql`主从

> 1. 开启bin_log
> 2. 设置主从同步账号，配置主从配置 

> 半同步

### 2、`mysql`多主多从

> - **数据分片**
> - **分片维度**
> - **分片冗余一致性保障**
> - **无迁移扩展**

#### 数据分片

> hash + model

---

#### **分片维度**

> - 固定路由位  (比如根据用户id进行维度划分)
> - 时间自增分片

---

#### 数据分片冗余

> 用户订单
>
> 商户订单

![mark](http://codedorado.oss-cn-beijing.aliyuncs.com/images/20200504/120713238.png)

---

#### 无迁移扩展

> 首先根据时间来一次划分，然后再根据固定的序列进行路由