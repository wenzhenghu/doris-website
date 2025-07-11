---
{
    "title": "JSONB_EXTRACT",
    "language": "zh-CN"
}
---

## jsonb_extract

jsonb_extract

## 描述
## 语法

```sql
JSONB jsonb_extract(JSONB j, VARCHAR json_path)
BOOLEAN jsonb_extract_isnull(JSONB j, VARCHAR json_path)
BOOLEAN jsonb_extract_bool(JSONB j, VARCHAR json_path)
INT jsonb_extract_int(JSONB j, VARCHAR json_path)
BIGINT jsonb_extract_bigint(JSONB j, VARCHAR json_path)
DOUBLE jsonb_extract_double(JSONB j, VARCHAR json_path)
STRING jsonb_extract_string(JSONB j, VARCHAR json_path)
```

jsonb_extract是一系列函数，从JSONB类型的数据中提取json_path指定的字段，根据要提取的字段类型不同提供不同的系列函数。
- jsonb_extract返回JSONB类型
- jsonb_extract_isnull返回是否为json null的BOOLEAN类型
- jsonb_extract_bool返回BOOLEAN类型
- jsonb_extract_int返回INT类型
- jsonb_extract_bigint返回BIGINT类型
- jsonb_extract_double返回DOUBLE类型
- jsonb_extract_STRING返回STRING类型

特殊情况处理如下：
- 如果json_path指定的字段在JSON中不存在，返回NULL
- 如果json_path指定的字段在JSON中的实际类型和jsonb_extract_t指定的类型不一致，如果能无损转换成指定类型返回指定类型t，如果不能则返回NULL

## 举例

参考 [jsonb tutorial](../../sql-reference/Data-Types/JSONB.md) 中的示例

### keywords
JSONB, JSON, jsonb_extract, jsonb_extract_isnull, jsonb_extract_bool, jsonb_extract_int, jsonb_extract_bigint, jsonb_extract_double, jsonb_extract_string

