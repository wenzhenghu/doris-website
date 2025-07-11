---
{
    "title": "Subquery",
    "language": "en"
}
---

A subquery is a `SELECT` statement that is nested within another `SELECT` statement. The nested subquery is often referred to as the inner query, while the containing query is known as the outer query or the outer query block. The subquery returns data that is used as a condition by the outer query to determine which data needs to be retrieved. There is no limit to the number of nested subqueries you can create.

Like any query, a subquery can return records from a table as single-column single-record, single-column multiple-record, or multi-column multiple-record.

## Subquery example in Where clause

```
SELECT * FROM sub_query_correlated_subquery1 WHERE k1 > (SELECT AVG(k1) FROM sub_query_correlated_subquery3) OR k1 < 10 order by k1, k2;
```

```
select * from sub_query_correlated_subquery1 where sub_query_correlated_subquery1.k1 not in (select sub_query_correlated_subquery3.k3 from sub_query_correlated_subquery3 where sub_query_correlated_subquery3.v2 = sub_query_correlated_subquery1.k2) or k1 < 10 order by k1, k2
```

##  Subquery example in Join clause

```
select t1.* from t1 left join t2 on t1.k2 = t2.k3 and t1.k1 in ( select t3.k1 from t3 where t1.k2 = t3.k2 ) or t1.k1 < 10 order by t1.k1, t1.k2;
select t1.* from t1 left join t2 on t1.k2 = t2.k3 and exists ( select t3.k1 from t3 where t1.k2 = t3.k2 ) or t1.k1 < 10 order by t1.k1, t1.k2;
```