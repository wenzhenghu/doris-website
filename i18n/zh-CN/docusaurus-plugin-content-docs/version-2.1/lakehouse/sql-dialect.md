---
{
    "title": "SQL 方言兼容",
    "language": "zh-CN"
}
---

:::tip
从 2.1 版本开始，Doris 可以支持多种 SQL 方言，如 Presto、Trino、Hive、PostgreSQL、Spark、Clickhouse 等等。通过这个功能，用户可以直接使用对应的 SQL 方言查询 Doris 中的数据，方便用户将原先的业务平滑的迁移到 Doris 中。
:::

:::caution
该功能目前是实验性功能，您在使用过程中如遇到任何问题，欢迎通过邮件组、[GitHub Issue](https://github.com/apache/doris/issues) 等方式进行反馈。
:::

## 部署服务

1. 下载最新版本的 [SQL 方言转换工具](https://www.selectdb.com/tools/doris-sql-convertor)

    :::info NOTE
    SQL 方言转换工具基于开源的 [SQLGlot](https://github.com/tobymao/sqlglot) ，由 SelectDB 进行二次开发，关于 SQLGlot 可参阅 [SQLGlot 官网](https://sqlglot.com/sqlglot.html)。  
    SQL Convertor 并非由 Apache Doris 维护或认可，这些工作由 Committers 和 Doris PMC 监督。使用这些资源和服务完全由您自行决定，社区不负责验证这些工具的许可或有效性。 
    ::: 

2.  在任意 FE 节点，通过以下命令启动服务：

    ```shell
    # 配置服务端口
    vim apiserver/conf/config.conf
    # 启动 SQL Converter for Apache Doris 转换服务
    sh apiserver/bin/start.sh
    # 如需前端界面，可在 webserver 中配置相应的端口并启动，不需要前端则可以忽略以下操作
    vim webserver/conf/config.conf
    # 启动前端界面
    sh webserver/bin/start.sh
    ```


    :::tip

    1. 该服务是一个无状态的服务，可随时启停

    2. 在 `apiserver/conf/config.conf` 中配置 port 来指定任意一个可用端口，配置 workers 来指定启动的线程数量。在并发场景中，可以根据需要调整，默认为 1

    3. 建议在每个 FE 节点都单独启动一个服务

    4. 如需启动前端界面，可以在 `webserver/conf/config.conf` 中配置 SQL Converter for Apache Doris 转换服务地址，默认是 `API_HOST=http://127.0.0.1:5001`
    :::

3. 启动 Doris 集群（2.1 或更高版本）

4. 通过以下命令，在 Doris 中设置 SQL 方言转换服务的 URL：

	`MySQL> set global sql_converter_service_url = "http://127.0.0.1:5001/api/v1/convert"`

    - `127.0.0.1:5001` 是 SQL 方言转换服务的部署节点 ip 和端口。
	
## 使用 SQL 方言

目前支持的方言类型包括：

- `presto`

- `trino`

- `clickhouse`

- `hive`

- `spark`

- `postgres`



示例：

### Presto

```sql
mysql> CREATE TABLE  test_sqlconvert (
         id int,
         start_time DateTime,
         value String,
         arr_int ARRAY<Int>,
         arr_str ARRAY<String>
     ) ENGINE=OLAP
     DUPLICATE KEY(`id`)
     COMMENT 'OLAP'
     DISTRIBUTED BY HASH(`id`) BUCKETS 1
     PROPERTIES (
     "replication_allocation" = "tag.location.default: 1"
     );
Query OK, 0 rows affected (0.01 sec)

mysql> INSERT INTO test_sqlconvert values(1, '2024-05-20 13:14:52', '2024-01-14',[1, 2, 3, 3], ['Hello', 'World']);
Query OK, 1 row affected (0.08 sec)

mysql> set sql_dialect=presto;
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT cast(start_time as varchar(20)) as col1,
            array_distinct(arr_int) as col2,
            FILTER(arr_str, x -> x LIKE '%World%') as col3,
            to_date(value,'%Y-%m-%d') as col4,
            YEAR(start_time) as col5,
            date_add('month', 1, start_time) as col6,
            REGEXP_EXTRACT_ALL(value, '-.') as col7,
            JSON_EXTRACT('{"id": "33"}', '$.id')as col8,
            element_at(arr_int, 1) as col9,
            date_trunc('day',start_time) as col10
         FROM test_sqlconvert
         where date_trunc('day',start_time)= DATE'2024-05-20'     
     order by id;
+---------------------+-----------+-----------+------------+------+---------------------+-------------+------+------+---------------------+
| col1                | col2      | col3      | col4       | col5 | col6                | col7        | col8 | col9 | col10               |
+---------------------+-----------+-----------+------------+------+---------------------+-------------+------+------+---------------------+
| 2024-05-20 13:14:52 | [1, 2, 3] | ["World"] | 2024-01-14 | 2024 | 2024-06-20 13:14:52 | ['-0','-1'] | "33" |    1 | 2024-05-20 00:00:00 |
+---------------------+-----------+-----------+------------+------+---------------------+-------------+------+------+---------------------+
1 row in set (0.03 sec)

```

### Clickhouse

```sql
mysql> set sql_dialect=clickhouse;
Query OK, 0 rows affected (0.00 sec)

mysql> select  toString(start_time) as col1,
             arrayCompact(arr_int) as col2,
             arrayFilter(x -> x like '%World%',arr_str)as col3,
             toDate(value) as col4,
             toYear(start_time)as col5,
             addMonths(start_time, 1)as col6,
             extractAll(value, '-.')as col7,
             JSONExtractString('{"id": "33"}' , 'id')as col8,
             arrayElement(arr_int, 1) as col9,
             date_trunc('day',start_time) as col10
          FROM test_sqlconvert
          where date_trunc('day',start_time)= '2024-05-20 00:00:00'     
     order by id;
+---------------------+-----------+-----------+------------+------+---------------------+-------------+------+------+---------------------+
| col1                | col2      | col3      | col4       | col5 | col6                | col7        | col8 | col9 | col10               |
+---------------------+-----------+-----------+------------+------+---------------------+-------------+------+------+---------------------+
| 2024-05-20 13:14:52 | [1, 2, 3] | ["World"] | 2024-01-14 | 2024 | 2024-06-20 13:14:52 | ['-0','-1'] | "33" |    1 | 2024-05-20 00:00:00 |
+---------------------+-----------+-----------+------------+------+---------------------+-------------+------+------+---------------------+
1 row in set (0.02 sec)
```

