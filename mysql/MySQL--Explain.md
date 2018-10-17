# MySQL--Explain

[参考文章](https://www.jianshu.com/p/73f2c8448722)



### EXPLAIN输出项（来源于mysql5.7文档）

备注:当使用FORMAT=JSON， 返回的数据为json结构时，JSON Name为null的不显示。

| Column        | JSON Name     | Meaning                                        |
| ------------- | ------------- | ---------------------------------------------- |
| id            | select_id     | The SELECT identifier                          |
| select_type   | None          | The SELECT type                                |
| table         | table_name    | The table for the output row                   |
| partitions    | partitions    | The matching partitions                        |
| type          | access_type   | The join type                                  |
| possible_keys | possible_keys | The possible indexes to choose                 |
| key           | key           | The index actually chosen                      |
| key_len       | key_length    | The length of the chosen key                   |
| ref           | ref           | The columns compared to the index              |
| rows          | rows          | Estimate of rows to be examined                |
| filtered      | filtered      | Percentage of rows filtered by table condition |
| Extra         | None          | Additional information                         |

#### 注意：在5.7以前的版本中，想要显示partitions需要使用explain partitions命令；想要显示filtered需要使用explain extended命令。在5.7版本后，默认explain直接显示partitions和filtered中的信息。



#### 1.id的含义:

id为SELECT的标识符。它是在SELECT查询中的顺序编号。如果这一行表示其他行的union结果，这个值可以为空。在这种情况下，table列会显示为形如<union M,N>，表示它是id为M和N的查询行的联合结果。

```
注意：id列数字越大越先执行，如果说数字一样大，那么就从上往下依次执行。
```



#### 2.select_type可能出现的情况（来源于Mysql5.7文档）







