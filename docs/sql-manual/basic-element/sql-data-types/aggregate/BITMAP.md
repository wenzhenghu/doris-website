---
{
    "title": "BITMAP",
    "language": "en"
}
---

## BITMAP
### Description
BITMAP

The columns of the BITMAP type can be used in Aggregate table, Unique table or Duplicate table.
When used in a Unique table or Duplicate table, they must be used as non-key columns.
When used in an Aggregate table, they must be used as non-key columns, and the aggregation type is BITMAP_UNION when building the table.
The user does not need to specify the length and default value. The length is controlled within the system according to the degree of data aggregation.
And the BITMAP column can only be queried or used by supporting functions such as bitmap_union_count, bitmap_union, bitmap_hash and bitmap_hash64.
    
The use of BITMAP in offline scenarios will affect the import speed. In the case of a large amount of data, the query speed will be slower than HLL and better than Count Distinct.
Note: If BITMAP does not use a global dictionary in real-time scenarios, using bitmap_hash() may cause an error of about one-thousandth. If the error rate is not tolerable, bitmap_hash64 can be used instead.

### example

Create table example:

    create table metric_table (
      datekey int,
      hour int,
      device_id bitmap BITMAP_UNION
    )
    aggregate key (datekey, hour)
    distributed by hash(datekey, hour) buckets 1
    properties(
      "replication_num" = "1"
    );

Insert data example:

    insert into metric_table values
    (20200622, 1, to_bitmap(243)),
    (20200622, 2, bitmap_from_array([1,2,3,4,5,434543])),
    (20200622, 3, to_bitmap(287667876573));

Query data example:

    select hour, BITMAP_UNION_COUNT(pv) over(order by hour) uv from(
       select hour, BITMAP_UNION(device_id) as pv
       from metric_table -- Query the accumulated UV per hour
       where datekey=20200622
    group by hour order by 1
    ) final;

You can set the [session variable](../../../sql-statements/session/variable/SET-VARIABLE) `return_object_data_as_binary` to `true`. Consequently, the bitmap will be returned in binary format.
    
### keywords
BITMAP
