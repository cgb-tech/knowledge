## 索引相关

- `db.user.getIndexes()`:找出索引

  - ```sql
    [
        {
            "v" : 1,
            "key" : {
                "_id" : 1  //表示升序
        },
            "name" : "_id_",
            "ns" : "test.user" //存储在哪个空间内
        }
    ]
    
    ```

- `db.user.createIndex(keys,opection)`: opection 中最主要的是两个选项`name`和`unique`

- `db.user.dropIndex()`：删除索引

- `db.user.find({_id:1}).explain()`:执行计划