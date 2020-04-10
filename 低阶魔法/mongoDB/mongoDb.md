## mongoDB的三个概念

- 数据库
- 集合 类似于数组，集合中可以存放文档
- 文档 文档是mongodb中最小的单位

| SQL术语/概念 | MongoDB术语/概念 | 解释/说明                           |
| ------------ | ---------------- | ----------------------------------- |
| database     | database         | 数据库                              |
| table        | collection       | 数据库表/集合                       |
| row          | document         | 数据记录行/文档                     |
| column       | field            | 数据字段/域                         |
| index        | index            | 索引                                |
| table joins  |                  | 表连接,MongoDB不支持                |
| primary key  | primary key      | 主键,MongoDB自动将_id字段设置为主键 |

## mongoDB中的数据类型

| 数据类型           | 描述                                                         |
| ------------------ | ------------------------------------------------------------ |
| String             | 字符串。存储数据常用的数据类型。在 MongoDB 中，UTF-8 编码的字符串才是合法的。 |
| Integer            | 整型数值。用于存储数值。根据你所采用的服务器，可分为 32 位或 64 位。 |
| Boolean            | 布尔值。用于存储布尔值（真/假）。                            |
| Double             | 双精度浮点值。用于存储浮点值。                               |
| Min/Max keys       | 将一个值与 BSON（二进制的 JSON）元素的最低值和最高值相对比。 |
| Array              | 用于将数组或列表或多个值存储为一个键。                       |
| Timestamp          | 时间戳。记录文档修改或添加的具体时间。                       |
| Object             | 用于内嵌文档。                                               |
| Null               | 用于创建空值。                                               |
| Symbol             | 符号。该数据类型基本上等同于字符串类型，但不同的是，它一般用于采用特殊符号类型的语言。 |
| Date               | 日期时间。用 UNIX 时间格式来存储当前日期或时间。你可以指定自己的日期时间：创建 Date 对象，传入年月日信息。 |
| Object ID          | 对象 ID。用于创建文档的 ID。                                 |
| Binary Data        | 二进制数据。用于存储二进制数据。                             |
| Code               | 代码类型。用于在文档中存储 JavaScript 代码。                 |
| Regular expression | 正则表达式类型。用于存储正则表达式。                         |

## mongoDB中的一些操作

- `show dbs`：可以显示所有数据的列表

- `db`：可以显示当前数据库对象或集合

- `use`：可以连接到一个指定的数据库

- insert
  
  - `db.collection.insert()` ：中间是集合的名字
  
- `db.collection.insert([{},{},...])`:插入多个对象
  
  - ```sql
    db.users.insertOne(
       {
          name: "sue",
          age: 19,
          status: "P"
       }
    )
    ```
  
  - ```sql
    db.users.insertMany(
       [
         { name: "bob", age: 42, status: "A", },
         { name: "ahn", age: 22, status: "A", },
         { name: "xi", age: 34, status: "D", }
       ]
    )
    ```
  
  - 
  
- query
  
  - `db.collection.find()`：集合中的所有文档,()中可以传入一个对象
  - `db.collection.find({_id:1})`：查询条件是_id为1
  - `db.collection.findOne({})`：查询出符合条件的第一个文档 
  - `db.collection.find().count()`：统计返回结果的数量 length（）也可以实现
  - `db.collection.find({}.{collection:1})`:显示投影，出现自己想要的字段
  
- update

  - `db.user.update({"sex":1},{"name":"shs"})`：默认情况下会使用新对象来替换就得对象

  - `db.user.update({"sex":1},{$set{"name":"shs"}})`：$set会只修改特定的内容 

  - `db.user.updateOne` $unset会删除里面的属性

  - `db.user.updateMany`：默认情况下只会修改一个，many可以修改匹配到的内容

  - ```sql
    db.collection.update(
       <query>,
       <update>,
       {
         upsert: <boolean>,
         multi: <boolean>, //设置可以修改多个
         writeConcern: <document>
       }
    )
    ```

  - `db.collection.replaceOne()`

- delete

  - `db.collection.deleteMany()`

  - `db.collection.remove()`：默认情况删除多个，remove必须传参 性能略差

    - ```sql
      db.collection.remove(
         <query>,
         {
           justOne: <boolean>,
           writeConcern: <document>
         }
      )
      ```

  - `db.collection.deleteOne()`

- db.collection.drop():删除整个集合,若是最后一个集合，则删除数据库、

- skip（）：跳过多少条数据

- limit（）：显示多少条数据

- count（）：计算数量

- sort({fetched:-1/1,fetched:-1/1}):排序功能 

## mongoDB中的查询关键字（Query Selectors）

### Comparison

For comparison of different BSON type values, see the [specified BSON comparison order](https://docs.mongodb.com/v3.2/reference/bson-types/#bson-types-comparison-order).

| Name                                                         | Description                                                  |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| [`$eq`](https://docs.mongodb.com/v3.2/reference/operator/query/eq/#op._S_eq) | Matches values that are equal to a specified value.          |
| [`$gt`](https://docs.mongodb.com/v3.2/reference/operator/query/gt/#op._S_gt) | Matches values that are greater than a specified value.      |
| [`$gte`](https://docs.mongodb.com/v3.2/reference/operator/query/gte/#op._S_gte) | Matches values that are greater than or equal to a specified value. |
| [`$lt`](https://docs.mongodb.com/v3.2/reference/operator/query/lt/#op._S_lt) | Matches values that are less than a specified value.         |
| [`$lte`](https://docs.mongodb.com/v3.2/reference/operator/query/lte/#op._S_lte) | Matches values that are less than or equal to a specified value. |
| [`$ne`](https://docs.mongodb.com/v3.2/reference/operator/query/ne/#op._S_ne) | Matches all values that are not equal to a specified value.  |
| [`$in`](https://docs.mongodb.com/v3.2/reference/operator/query/in/#op._S_in) | Matches any of the values specified in an array.             |
| [`$nin`](https://docs.mongodb.com/v3.2/reference/operator/query/nin/#op._S_nin) | Matches none of the values specified in an array.            |

### Logical

| Name                                                         | Description                                                  |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| [`$or`](https://docs.mongodb.com/v3.2/reference/operator/query/or/#op._S_or) | Joins query clauses with a logical `OR` returns all documents that match the conditions of either clause. |
| [`$and`](https://docs.mongodb.com/v3.2/reference/operator/query/and/#op._S_and) | Joins query clauses with a logical `AND` returns all documents that match the conditions of both clauses. |
| [`$not`](https://docs.mongodb.com/v3.2/reference/operator/query/not/#op._S_not) | Inverts the effect of a query expression and returns documents that do *not* match the query expression. |
| [`$nor`](https://docs.mongodb.com/v3.2/reference/operator/query/nor/#op._S_nor) | Joins query clauses with a logical `NOR` returns all documents that fail to match both clauses. |



### Element

| Name                                                         | Description                                            |
| :----------------------------------------------------------- | :----------------------------------------------------- |
| [`$exists`](https://docs.mongodb.com/v3.2/reference/operator/query/exists/#op._S_exists) | Matches documents that have the specified field.       |
| [`$type`](https://docs.mongodb.com/v3.2/reference/operator/query/type/#op._S_type) | Selects documents if a field is of the specified type. |

### Evaluation

| Name                                                         | Description                                                  |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| [`$mod`](https://docs.mongodb.com/v3.2/reference/operator/query/mod/#op._S_mod) | Performs a modulo operation on the value of a field and selects documents with a specified result. |
| [`$regex`](https://docs.mongodb.com/v3.2/reference/operator/query/regex/#op._S_regex) | Selects documents where values match a specified regular expression. |
| [`$text`](https://docs.mongodb.com/v3.2/reference/operator/query/text/#op._S_text) | Performs text search.                                        |
| [`$where`](https://docs.mongodb.com/v3.2/reference/operator/query/where/#op._S_where) | Matches documents that satisfy a JavaScript expression.      |

### Geospatial

| Name                                                         | Description                                                  |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| [`$geoWithin`](https://docs.mongodb.com/v3.2/reference/operator/query/geoWithin/#op._S_geoWithin) | Selects geometries within a bounding [GeoJSON geometry](https://docs.mongodb.com/v3.2/reference/geojson/#geospatial-indexes-store-geojson). The [2dsphere](https://docs.mongodb.com/v3.2/core/2dsphere/) and [2d](https://docs.mongodb.com/v3.2/core/2d/) indexes support [`$geoWithin`](https://docs.mongodb.com/v3.2/reference/operator/query/geoWithin/#op._S_geoWithin). |
| [`$geoIntersects`](https://docs.mongodb.com/v3.2/reference/operator/query/geoIntersects/#op._S_geoIntersects) | Selects geometries that intersect with a [GeoJSON](https://docs.mongodb.com/v3.2/reference/glossary/#term-geojson) geometry. The [2dsphere](https://docs.mongodb.com/v3.2/core/2dsphere/) index supports [`$geoIntersects`](https://docs.mongodb.com/v3.2/reference/operator/query/geoIntersects/#op._S_geoIntersects). |
| [`$near`](https://docs.mongodb.com/v3.2/reference/operator/query/near/#op._S_near) | Returns geospatial objects in proximity to a point. Requires a geospatial index. The [2dsphere](https://docs.mongodb.com/v3.2/core/2dsphere/) and [2d](https://docs.mongodb.com/v3.2/core/2d/) indexes support [`$near`](https://docs.mongodb.com/v3.2/reference/operator/query/near/#op._S_near). |
| [`$nearSphere`](https://docs.mongodb.com/v3.2/reference/operator/query/nearSphere/#op._S_nearSphere) | Returns geospatial objects in proximity to a point on a sphere. Requires a geospatial index. The [2dsphere](https://docs.mongodb.com/v3.2/core/2dsphere/) and [2d](https://docs.mongodb.com/v3.2/core/2d/) indexes support [`$nearSphere`](https://docs.mongodb.com/v3.2/reference/operator/query/nearSphere/#op._S_nearSphere). |

### Array

| Name                                                         | Description                                                  |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| [`$all`](https://docs.mongodb.com/v3.2/reference/operator/query/all/#op._S_all) | Matches arrays that contain all elements specified in the query. |
| [`$elemMatch`](https://docs.mongodb.com/v3.2/reference/operator/query/elemMatch/#op._S_elemMatch) | Selects documents if element in the array field matches all the specified [`$elemMatch`](https://docs.mongodb.com/v3.2/reference/operator/query/elemMatch/#op._S_elemMatch) conditions. |
| [`$size`](https://docs.mongodb.com/v3.2/reference/operator/query/size/#op._S_size) | Selects documents if the array field is a specified size.    |

### Bitwise

| Name                                                         | Description                                                  |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| [`$bitsAllSet`](https://docs.mongodb.com/v3.2/reference/operator/query/bitsAllSet/#op._S_bitsAllSet) | Matches numeric or binary values in which a set of bit positions *all* have a value of `1`. |
| [`$bitsAnySet`](https://docs.mongodb.com/v3.2/reference/operator/query/bitsAnySet/#op._S_bitsAnySet) | Matches numeric or binary values in which *any* bit from a set of bit positions has a value of `1`. |
| [`$bitsAllClear`](https://docs.mongodb.com/v3.2/reference/operator/query/bitsAllClear/#op._S_bitsAllClear) | Matches numeric or binary values in which a set of bit positions *all* have a value of `0`. |
| [`$bitsAnyClear`](https://docs.mongodb.com/v3.2/reference/operator/query/bitsAnyClear/#op._S_bitsAnyClear) | Matches numeric or binary values in which *any* bit from a set of bit positions has a value of `0`. |

### Comments

| Name                                                         | Description                          |
| :----------------------------------------------------------- | :----------------------------------- |
| [`$comment`](https://docs.mongodb.com/v3.2/reference/operator/query/comment/#op._S_comment) | Adds a comment to a query predicate. |

## Projection Operators

| Name                                                         | Description                                                  |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| [`$`](https://docs.mongodb.com/v3.2/reference/operator/projection/positional/#proj._S_) | Projects the first element in an array that matches the query condition. |
| [`$elemMatch`](https://docs.mongodb.com/v3.2/reference/operator/projection/elemMatch/#proj._S_elemMatch) | Projects the first element in an array that matches the specified [`$elemMatch`](https://docs.mongodb.com/v3.2/reference/operator/projection/elemMatch/#proj._S_elemMatch) condition. |
| [`$meta`](https://docs.mongodb.com/v3.2/reference/operator/projection/meta/#proj._S_meta) | Projects the document’s score assigned during [`$text`](https://docs.mongodb.com/v3.2/reference/operator/query/text/#op._S_text) operation. |
| [`$slice`](https://docs.mongodb.com/v3.2/reference/operator/projection/slice/#proj._S_slice) | Limits the number of elements projected from an array. Supports skip and limit slices. |

## Update Operators

### Fields

| Name                                                         | Description                                                  |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| [`$inc`](https://docs.mongodb.com/v3.2/reference/operator/update/inc/#up._S_inc) | Increments the value of the field by the specified amount.   |
| [`$mul`](https://docs.mongodb.com/v3.2/reference/operator/update/mul/#up._S_mul) | Multiplies the value of the field by the specified amount.   |
| [`$rename`](https://docs.mongodb.com/v3.2/reference/operator/update/rename/#up._S_rename) | Renames a field.                                             |
| [`$setOnInsert`](https://docs.mongodb.com/v3.2/reference/operator/update/setOnInsert/#up._S_setOnInsert) | Sets the value of a field if an update results in an insert of a document. Has no effect on update operations that modify existing documents. |
| [`$set`](https://docs.mongodb.com/v3.2/reference/operator/update/set/#up._S_set) | Sets the value of a field in a document.                     |
| [`$unset`](https://docs.mongodb.com/v3.2/reference/operator/update/unset/#up._S_unset) | Removes the specified field from a document.                 |
| [`$min`](https://docs.mongodb.com/v3.2/reference/operator/update/min/#up._S_min) | Only updates the field if the specified value is less than the existing field value. |
| [`$max`](https://docs.mongodb.com/v3.2/reference/operator/update/max/#up._S_max) | Only updates the field if the specified value is greater than the existing field value. |
| [`$currentDate`](https://docs.mongodb.com/v3.2/reference/operator/update/currentDate/#up._S_currentDate) | Sets the value of a field to current date, either as a Date or a Timestamp. |

### Array

#### Operators

| Name                                                         | Description                                                  |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| [`$`](https://docs.mongodb.com/v3.2/reference/operator/update/positional/#up._S_) | Acts as a placeholder to update the first element that matches the query condition in an update. |
| [`$addToSet`](https://docs.mongodb.com/v3.2/reference/operator/update/addToSet/#up._S_addToSet) | Adds elements to an array only if they do not already exist in the set. |
| [`$pop`](https://docs.mongodb.com/v3.2/reference/operator/update/pop/#up._S_pop) | Removes the first or last item of an array.                  |
| [`$pullAll`](https://docs.mongodb.com/v3.2/reference/operator/update/pullAll/#up._S_pullAll) | Removes all matching values from an array.                   |
| [`$pull`](https://docs.mongodb.com/v3.2/reference/operator/update/pull/#up._S_pull) | Removes all array elements that match a specified query.     |
| [`$pushAll`](https://docs.mongodb.com/v3.2/reference/operator/update/pushAll/#up._S_pushAll) | *Deprecated.* Adds several items to an array.                |
| [`$push`](https://docs.mongodb.com/v3.2/reference/operator/update/push/#up._S_push) | Adds an item to an array.                                    |

#### Modifiers

| Name                                                         | Description                                                  |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| [`$each`](https://docs.mongodb.com/v3.2/reference/operator/update/each/#up._S_each) | Modifies the [`$push`](https://docs.mongodb.com/v3.2/reference/operator/update/push/#up._S_push) and [`$addToSet`](https://docs.mongodb.com/v3.2/reference/operator/update/addToSet/#up._S_addToSet) operators to append multiple items for array updates. |
| [`$slice`](https://docs.mongodb.com/v3.2/reference/operator/update/slice/#up._S_slice) | Modifies the [`$push`](https://docs.mongodb.com/v3.2/reference/operator/update/push/#up._S_push) operator to limit the size of updated arrays. |
| [`$sort`](https://docs.mongodb.com/v3.2/reference/operator/update/sort/#up._S_sort) | Modifies the [`$push`](https://docs.mongodb.com/v3.2/reference/operator/update/push/#up._S_push) operator to reorder documents stored in an array. |
| [`$position`](https://docs.mongodb.com/v3.2/reference/operator/update/position/#up._S_position) | Modifies the [`$push`](https://docs.mongodb.com/v3.2/reference/operator/update/push/#up._S_push) operator to specify the position in the array to add elements. |

### Bitwise

| Name                                                         | Description                                                  |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| [`$bit`](https://docs.mongodb.com/v3.2/reference/operator/update/bit/#up._S_bit) | Performs bitwise `AND`, `OR`, and `XOR` updates of integer values. |

### Isolation

| Name                                                         | Description                                                  |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| [`$isolated`](https://docs.mongodb.com/v3.2/reference/operator/update/isolated/#up._S_isolated) | Modifies the behavior of a write operation to increase the isolation of the operation. |

